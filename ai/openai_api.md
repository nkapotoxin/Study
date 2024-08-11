#### 1. Function Calling介绍

*内容来自 https://platform.openai.com/docs/guides/function-calling*

1. OpenAI GPT 通过Function Calling功能实现LLM和external tools的结合

2. 应用场景
	1. 实现assistants的fetch data, 如查询用户订单等
	2. 实现assistants的take actions, 如定会议
	3. 实现assistants的perform computations, 比如数学计算

3. Function Call交互流程
![](function-calling-diagram.png)

4. 如何使用Function Call

4.1) 确定要调用的Function
4.2) 定义GPT能够识别的函数定义
```
{
    "name": "get_delivery_date",
    "description": "Get the delivery date for a customer's order. Call this whenever you need to know the delivery date, for example when a customer asks 'Where is my package'",
    "parameters": {
        "type": "object",
        "properties": {
            "order_id": {
                "type": "string",
                "description": "The customer's order ID.",
            },
        },
        "required": ["order_id"],
        "additionalProperties": false,
    }
}
```
4.3) 调用GPT接口时, 作为tools参数传给openai
```
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_delivery_date",
            "description": "Get the delivery date for a customer's order. Call this whenever you need to know the delivery date, for example when a customer asks 'Where is my package'",
            "parameters": {
                "type": "object",
                "properties": {
                    "order_id": {
                        "type": "string",
                        "description": "The customer's order ID.",
                    },
                },
                "required": ["order_id"],
                "additionalProperties": False,
            },
        }
    }
]

messages = [
    {"role": "system", "content": "You are a helpful customer support assistant. Use the supplied tools to assist the user."},
    {"role": "user", "content": "Hi, can you tell me the delivery date for my order?"}
]

response = openai.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools,
)
```

4.4) 基于GPT的返回确定Function名字和入参
```
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_delivery_date",
            "description": "Get the delivery date for a customer's order. Call this whenever you need to know the delivery date, for example when a customer asks 'Where is my package'",
            "parameters": {
                "type": "object",
                "properties": {
                    "order_id": {
                        "type": "string",
                        "description": "The customer's order ID."
                    }
                },
                "required": ["order_id"],
                "additionalProperties": False
            }
        }
    }
]

messages = []
messages.append({"role": "system", "content": "You are a helpful customer support assistant. Use the supplied tools to assist the user."})
messages.append({"role": "user", "content": "Hi, can you tell me the delivery date for my order?"})
messages.append({"role": "assistant", "content": "Hi there! I can help with that. Can you please provide your order ID?"})
messages.append({"role": "user", "content": "i think it is order_12345"})

response = client.chat.completions.create(
    model='gpt-4o',
    messages=messages,
    tools=tools
)

Choice(
    finish_reason='tool_calls',
    index=0,
    logprobs=None,
    message=chat.completionsMessage(
        content=None,
        role='assistant',
        function_call=None,
        tool_calls=[
            chat.completionsMessageToolCall(
                id='call_62136354',
                function=Function(
                    arguments='{"order_id":"order_12345"}',
                    name='get_delivery_date'),
                type='function')
        ])
)
```

4.5) 调用函数
```
# Extract the arguments for get_delivery_date
# Note this code assumes we have already determined that the model generated a function call. See below for a more production ready example that shows how to check if the model generated a function call
tool_call = response.choices[0].message.tool_calls[0]
arguments = json.loads(tool_call['function']['arguments'])

order_id = arguments.get('order_id')

# Call the get_delivery_date function with the extracted order_id
delivery_date = get_delivery_date(order_id)
```
	
4.6) 拿到本地函数返回,再次调用GPT接口, 获取chat风格的输出给最终用户
```
# Simulate the order_id and delivery_date
order_id = "order_12345"
delivery_date = datetime.now()

# Simulate the tool call response
response = {
    "choices": [
        {
            "message": {
                "tool_calls": [
                    {"id": "tool_call_1"}
                ]
            }
        }
    ]
}

# Create a message containing the result of the function call
function_call_result_message = {
    "role": "tool",
    "content": json.dumps({
        "order_id": order_id,
        "delivery_date": delivery_date.strftime('%Y-%m-%d %H:%M:%S')
    }),
    "tool_call_id": response['choices'][0]['message']['tool_calls'][0]['id']
}

# Prepare the chat completion call payload
completion_payload = {
    "model": "gpt-4o",
    "messages": [
        {"role": "system", "content": "You are a helpful customer support assistant. Use the supplied tools to assist the user."},
        {"role": "user", "content": "Hi, can you tell me the delivery date for my order?"},
        {"role": "assistant", "content": "Hi there! I can help with that. Can you please provide your order ID?"},
        {"role": "user", "content": "i think it is order_12345"},
        response['choices'][0]['message'],
        function_call_result_message
    ]
}

# Call the OpenAI API's chat completions endpoint to send the tool call result back to the model
response = openai.chat.completions.create(
    model=completion_payload["model"],
    messages=completion_payload["messages"]
)

# Print the response from the API. In this case it will typically contain a message such as "The delivery date for your order #12345 is xyz. Is there anything else I can help you with?"
print(response)
```

#### 2 Function Calling注意事项

##### Edge Cases

Function calling有很多异常场景需要注意特殊处理, 比如输出max_tokens等, 建议使用SDK自动帮你搞定

```
# Check if the conversation was too long for the context window
if response['choices'][0]['message']['finish_reason'] == "length":
    print("Error: The conversation was too long for the context window.")
    # Handle the error as needed, e.g., by truncating the conversation or asking for clarification
    handle_length_error(response)
    
# Check if the model's output included copyright material (or similar)
if response['choices'][0]['message']['finish_reason'] == "content_filter":
    print("Error: The content was filtered due to policy violations.")
    # Handle the error as needed, e.g., by modifying the request or notifying the user
    handle_content_filter_error(response)
    
# Check if the model has made a tool_call. This is the case either if the "finish_reason" is "tool_calls" or if the "finish_reason" is "stop" and our API request had forced a function call
if (response['choices'][0]['message']['finish_reason'] == "tool_calls" or 
    # This handles the edge case where if we forced the model to call one of our functions, the finish_reason will actually be "stop" instead of "tool_calls"
    (our_api_request_forced_a_tool_call and response['choices'][0]['message']['finish_reason'] == "stop")):
    # Handle tool call
    print("Model made a tool call.")
    # Your code to handle tool calls
    handle_tool_call(response)
    
# Else finish_reason is "stop", in which case the model was just responding directly to the user
elif response['choices'][0]['message']['finish_reason'] == "stop":
    # Handle the normal stop case
    print("Model responded directly to the user.")
    # Your code to handle normal responses
    handle_normal_response(response)
    
# Catch any other case, this is unexpected
else:
    print("Unexpected finish_reason:", response['choices'][0]['message']['finish_reason'])
    # Handle unexpected cases as needed
    handle_unexpected_case(response)
```

##### Structured Outputs

GPT在给出function call建议时, 可能会出错, 比如处理的parameter with complicated schemas, 可能会发生miss parameters/get their types wrong

Structured Outputs特性就是优化这个问题的(ensures model outputs for function calls will exactly match your supplied schema)

具体使用方法参考: https://platform.openai.com/docs/guides/structured-outputs

##### parallel function calling

Model会判断在一个message中回复调用多个函数. 比如一个请求查询三个城市的天气.

You can also disable parallel function calling by setting parallel_tool_calls: false

In order to ensure strict schema adherence, disable parallel function calls by supplying parallel_tool_calls: false. With this setting, the model will generate one function call at a time.

##### 函数选择功能

We offer three ways to customize the default behavior:

	To force the model to always call one or more functions, you can set tool_choice: "required". The model will then always select one or more function(s) to call. This is useful for example if you want the model to pick between multiple actions to perform next.
	To force the model to call a specific function, you can set tool_choice: {"type": "function", "function": {"name": "my_function"}}.
	To disable function calling and force the model to only generate a user-facing message, you can either provide no tools, or set tool_choice: "none".
	By default, the model is configured to automatically select which functions to call, as determined by the tool_choice: "auto" setting.

##### Tips and best practices

1. Turn on Structured Outputs by setting strict: "true"

2. Name functions intuitively, with detailed descriptions

	1. update your function names
	2. update function descriptions
	3. avoid using abbreviations or acronyms to shorten function and argument names.
	4. include detailed descriptions for when a tool should be called.
	5. include descriptions for each of the arguments to help the model know what it needs to ask the user to collect that argument.

3. Name function parameters intuitively, with detailed descriptions
	Use clear and descriptive names for function parameters. For example, specify the expected format for a date parameter (e.g., YYYY-mm-dd or dd/mm/yy) in the description.

4. Consider providing additional information about how and when to call functions in your system message
	Use check_order_status when the user inquires about the status of their order, such as 'Where is my order?' or 'Has my order shipped yet?'
	Before scheduling a meeting with schedule_meeting, check the user's calendar for availability using check_availability to avoid conflicts.

5.  Use enums for function arguments when possible

6. Keep the number of functions low for higher accuracy
	
	1. We recommend that you use no more than 20 functions in a single tool call. 
	2. fine-tuning or break out the tools and group them logically to create a multi-agent system.

7. Set up evals to act as an aid in prompt engineering your function definitions and system messages

cook book with set up evals: https://cookbook.openai.com/examples/evaluation/getting_started_with_openai_evals

8. Fine-tuning may help improve accuracy for function calling.

参考:https://cookbook.openai.com/examples/fine_tuning_for_function_calling
	
















