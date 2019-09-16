
You define the structure and workflow for a Logic App using a JSON document. This document contains a JSON description of the elements that make up the Logic App ad is validated by the Workflow Definition Language schema.  The easiest way to explain the schema is to examine an existing Logic App created using the Logic Apps Designer in the Azure portal, and then view the JSON description of this app.

In the sample scenario, you want to provide your consultants with common workflows, which they can adapt to the specific needs of the universities they work with. You want to make it as easy as possible to customize and deploy each workflow, so you decide to take a look at the code behind the workflow, namely the workflow definition JSON. 

## The Logic Apps designer

The Logic Apps Designer enables you to create and debug the workflow for a Logic App graphically. The Logic Apps Designer also lets developers look under the hood of a workflow to see how it's implemented. The following image shows an example of a simple Logic App. The workflow is triggered by sending an HTTP GET request to a specified URL. The result is returned in the HTTP response. In this example, the workflow is sending back a simple *Hello Logic Apps Template!* message.

![workflow designer overview](../media/2-workflowdesigner.png)

 Lets now look at the workflow definition language as used by the JSON template.

## The Code view

The Code View window in the Logic Apps Designer shows the JSON document that describes the workflow. In the sample app, the code looks like this:

```JSON
{
    "definition": {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
        "actions": {
            "Response": {
                "inputs": {
                    "body": "Hello Logic Apps Template!",
                    "statusCode": 200
                },
                "kind": "Http",
                "runAfter": {},
                "type": "Response"
            }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {},
        "parameters": {},
        "triggers": {
            "manual": {
                "inputs": {
                    "method": "GET",
                    "schema": {}
                },
                "kind": "Http",
                "type": "Request"
            }
        }
    }
}
```

 Notice the sections in the `definition` scope that relate to the actions and triggers shown in the Logic Apps Designer. You can edit the JSON code in this document to reflect any changes required in the functionality of the Logic App. You can also add further actions, and specify how the logic in the workflow runs from one action to the next.

### The triggers section

The triggers section contains the description of the type of trigger, and how it can be invoked. In this example, the trigger is a simple HTTP trigger that that runs in response to an HTTP GET request.

```JSON
"triggers": {
    "manual": {
        "inputs": {
            "method": "GET",
            "schema": {}
        },
        "kind": "Http",
        "type": "Request"
    }
}
```

A trigger must include the following elements:

- A unique name inside the workflow. In the example above, the trigger is named *manual*, but you could replace this with a more meaningful identifier.
- The trigger type. The type indicates the event that causes the trigger to run. A *Request* trigger runs in response to an HTTP request. Other trigger types available include 
    - *Recurrence* for creating a trigger that runs according to a recurring schedule
    - *HttpWebhook* for listening to events on an endpoint
    - *ApiConnection* for responding to events triggered by other Azure services (for example, a message arriving on a message queue, an email message, and so on). The *ApiConnection* trigger type is generalized, and you specify further details indicating the type of the service, and any connection information required.
- The *inputs* section. This section specifies the data that defines the behavior of the trigger. For a Request trigger, the *method* indicates the type of HTTP request that causes the trigger to run. For an *ApiConnection* trigger, the *inputs* section contains information about how to connect to the resource triggering the event (a message queue connection string, for example). If the trigger is a *Request* trigger, the *schema* section of the input definition specifies the schema that the payload of the request body should conform to. HTTP GET requests don't have a request body, so the *schema* is empty in the example above.

The following example shows the definition of another *Request* trigger, for a Logic App that receives HTTP POST requests. A POST request typically provides a request body, containing the data to be posted. The request body in this example contains a customer name and address, comprising the street and city.

```JSON
"mypostrequest": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

A trigger can also specify conditions. The trigger will only fire if these conditions are met. You define conditions in an optional *conditions* section. For example, you might want to run the *mypostrequest* trigger shown above only if the request body specifies a city of *New York*:

```JSON
"mypostrequest": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      ...
   }
   "conditions": [
      {
        "expression": "@equals(triggerOutputs()['body']['customerAddress']['city'], 'New York')"
      }
   ]
}
```

### The actions section

The actions section of a Logic App defines the logic and structure of the workflow. It contains a series of *action* items. An action item is a basic building block for constructing workflows. Action items take inputs and produce outputs, which are passed to the next action item in the workflow. The different types of action items available include:

- *ApiConnection*. Use this type of action to send an HTTP request to a Microsoft Service. This action type enables you to integrate a Logic App with Azure features such as Azure Service Bus, and Azure Event Grid, amongst others. The action requires inputs that include a connection string for accessing the service, and any additional information and parameters required to invoke the service.
- *Compose*. Use this action to combine multiple inputs and expressions into a single output.
- *Function*. This action enables you to call an Azure Function.
- *HTTP*. This action can send an HTTP request to an HTTP endpoint (as opposed to an Azure service).
- *Join*. This action takes an array of data items as input and generates string containing these items separated by a specified delimiter.
- *Parse*. Use this action to parse a JSON document into a set of tokens, using a specified schema.
- *Query*. This action enables you to filter the items in an input array using a specified condition.
- *Response*. Use this action to create a response for an HTTP request.
- *Table*. This action can generate an HTML table from an array of JSON objects.
- *Terminate*. Use this action to cancel a workflow immediately.
- *Wait*. This action pauses the workflow for a specified interval or until a timeout occurs.
- *Workflow*. Use this action to run another Logic App.
- *Condition*. This is actually a set of action types (*Foreach*, *If*, *Switch*, and *Until*) that enable you to implement programmatic flow of control in a workflow. You can iterate through the items in a collection, make decisions based on the values of input parameters, and loop until some condition is met.
- *InitializeVariable*, *IncrementVariable*, *DecrementVariable*, and *SetVariable*. Use these actions to define, initialize, assign, and modify variables that you can pass between action items in a workflow.

Like a trigger, each action must have a unique name in the workflow. The example shown below names the item *Response*, but you could use any valid identifier. An action must have an *inputs* section that specifies the data that the action works on. For an HTTP Response action, this is where you can specify the data for an expression to be returned in the response message, together with an HTTP status code.

In our simple app definition, the action generates an HTTP response where the body is a simple message.

```JSON
"actions": {
    "Response": {
        "inputs": {
            "body": "Hello Logic Apps Template!",
            "statusCode": 200
        },
        "kind": "Http",
        "runAfter": {},
        "type": "Response"
    }
}
```

The *runAfter* section indicates where the action runs in the workflow sequence. In the example given, there's only a single action, so it always runs when the trigger fires. If the Logic App had multiple actions, you could specify the name of an action and a status for that action in this section. The action will run if the *runAfter* action completes with the specified status. The code below shows an example. The action *mySecondAction* runs after *myFirstAction*, but only if *myFirstAction* finishes with a status of "Succeeded":

```JSON
"actions": {
    "mySecondAction": {
        "inputs": {
            ...
        },
        "runAfter": {
            "myFirstAction": [
                "Succeeded"
            ]
        },
        "type": ...
    },
    "myFirstAction": {
        "inputs": {
            ...
        },
        "runAfter": {},
        "type": ...
    }
}
```

### The outputs section

Use the outputs section to define the data that your workflow can return when it has completed running. You could track a specific status or data for each run of the workflow. You can examine the output from each run of a workflow using the Logic Apps run history, available via the Azure portal of the Workflow REST API.

The format of the *outputs* section looks like this:

```JSON
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

### Logic Apps expressions

You can use a Logic Apps expression in place of any fixed value, variable, or constant. You can also place an expression anywhere in a JSON string value by prefixing the expression with the at-sign (@). For example, you can use the *@parameters* function in an expression to retrieve the value of a named parameter (parameters are described in the next section).

```JSON
"customerFullName": "Bill Frost",
"accountName": "@parameters('customerName')"
```

Logic Apps provides a set of built-in functions that you can use to create complex expressions. These include:

- String functions, for concatenating or splitting strings, converting characters between upper and lower case, and searching for substrings.
- Collection functions, for detecting whether a collection contains items that match a specific pattern, retrieving items from a collection, and combining collections.
- Logical comparison functions, for detecting whether operands are the same, different, numerically greater, or numerically less than each other.
- Conversion functions, for changing the type or format of data.
- Maths functions, such as *add*, *sub*, *div*, and *mul*, plus several others.
- Date and time functions, for parsing and processing dates and times.
- Workflow functions, for retrieving information about the data passed to a workflow action. For example, the *parameter* function (shown above) fetches the value of a named parameter, and the *body* function (shown earlier) returns the data being generated by an action.
- JSON and XML manipulation functions, for parsing and processing JSON and XML documents.

You can define variables in the *inputs* section of an *InitializeVariable* action, and you can manipulate these variables using expressions. You read the value of a variable using the *variables* function. The following example uses an *InitializeVariable* action to create an integer variable named *myIntegerVariable* and initialize it to 99. The example also shows a *Condition* action with the *If* type. The condition uses an expression to test the value of the *myIntegerVariable* variable, and if it matches the value 100, the condition uses an HTTP action to perform a GET request.

```JSON
"actions": {
    "Condition": {
        "actions": {
            "HTTP": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://dummyurl.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "expression": {
            "equals": [
                "@variables('myIntegerVariable')",
                100
            ]
        }        ,
        "runAfter": {
            "Initialize": [
                "Succeeded"
            ]
        },
        "type": "If"
    },
    "Initialize": {
        "inputs": {
            "variables": [
                {
                    "name": "myIntegerVariable",
                    "type": "Integer",
                    "value": 99
                }
            ]
        },
        "runAfter": {},
        "type": "InitializeVariable"
    }
}
```

### The parameters section

The parameters section enables you to parameterize a workflow. At runtime, you can provide values for each of these parameters. You reference the parameters anywhere in the workflow where you might use a constant or expression.

You can add a parameters definition with a default value. The default value is used if you don't provide a value for the parameter at runtime. The next example shows how to define a parameter named *cityParam*. The parameter is used inside the condition for the *mypostrequest* action, to perform the action only if the request document contains a city that matches the parameter value. The default parameter value is *New York*:

```JSON

    "definition": {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
        "actions": {
            ...
        },
        "contentVersion": "1.0.0.0",
        "outputs": {},
        "parameters": {
            "cityParam": {
                "defaultValue": "New York",
                "type": "String"
            }
        },
        "triggers": {
            "mypostrequest": {
                "conditions": [
                    {
                        "expression": "@equals(triggerOutputs()['body']['customerAddress']['city'], parameters('cityParam'))"
                    }
                ],
                "inputs": {
                    ...
                },
                "kind": "Http",
                "type": "Request"
            }
        }
    }
}
```