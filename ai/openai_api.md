#### 1. Function Calling

*内容来自 https://platform.openai.com/docs/guides/function-calling*

1. OpenAI GPT 通过Function Calling功能实现LLM和external tools的结合

2. 应用场景
	1. 实现assistants的fetch data, 如查询用户订单等
	2. 实现assistants的take actions, 如定会议
	3. 实现assistants的perform computations, 比如数学计算

3. Function Call交互流程
![](function-calling-diagram.png)

4. 如何使用Function Call

	1. 确定要调用的Function
	2. 定义GPT能够识别的函数定义
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
	3. 调用GPT接口时, 作为tools参数传给openai
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

	4. 基于GPT的返回确定Function名字和入参
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

	5. 调用函数
```
# Extract the arguments for get_delivery_date
# Note this code assumes we have already determined that the model generated a function call. See below for a more production ready example that shows how to check if the model generated a function call
tool_call = response.choices[0].message.tool_calls[0]
arguments = json.loads(tool_call['function']['arguments'])

order_id = arguments.get('order_id')

# Call the get_delivery_date function with the extracted order_id
delivery_date = get_delivery_date(order_id)
```
	
	6. 拿到本地函数返回,再次调用GPT接口, 获取chat风格的输出给最终用户
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



	




