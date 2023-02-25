# Reflection in UE4

## 1 Get/Set property from actor

	AActor* actor;
	UClass* ac = actor->GetClass();
	if (FProperty* Property = ac->FindPropertyByName("UserPlayerInfo"))
	{
		void* playerInfoAddress = Property->ContainerPtrToValuePtr<void>(actor);
		if (FStructProperty* StructProp = Cast<FStructProperty>(Property))
		{
			UScriptStruct* ScriptStruct = StructProp->Struct;
			for(FProperty* PropertyNew = ScriptStruct->PropertyLink; PropertyNew != NULL; PropertyNew = PropertyNew->PropertyLinkNext)
			{
				if (PropertyNew->GetName().Contains(TEXT("UserName")))
				{
					if (FStrProperty* ChildStrProp = Cast<FStrProperty>(PropertyNew))
					{
						ChildStrProp->GetPropertyValue_InContainer(playerInfoAddress);
					}
					
					if (FIntProperty* ChildIntProp = Cast<FIntProperty>(PropertyNew))
					{
						ChildIntProp->SetPropertyValue_InContainer(playerInfoAddress, 1);
					}
				}
			}
		}
	}


## 2 Call function from c++/bp

	AActor* actor;
	UFunction* Function = actor->GetClass()->FindFunctionByName();

