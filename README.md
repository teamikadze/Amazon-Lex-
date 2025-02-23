☁️ Overview of Project ☁️

In this project, we'll be building a language translation bot using Amazon Lex.

If you want to translate a word or sentence into another language, all you have to do is type it into the chatbot - and it will output the translation.
Steps to be performed 👩‍💻

In the next few lessons, we'll be going through the following steps.

    Creating an empty chatbot
    Specifying intents and slots
    Specify Fulfilment
    Create an IAM role
    Create a Lambda function
    Test the Lambda function
    Test the chatbot

Services Used 🛠

    Amazon Lex: Build the chatbot and define conversation flow.
    AWS Lambda: Get the book recommendation using a third party API.
    AWS IAM: Ensures secure access by managing user permissions.
    Amazon Translate: Used for translation of the sentence according to the input language specified.

Estimated Time & Cost ⚙️

    This project is estimated to take about 1-2 Hours
    Cost: Free (When using the AWS Free Tier)

➡️ Architectural Diagram

This it the architectural diagram for the project:

![01](https://github.com/user-attachments/assets/1bd8d162-7c57-4d37-b2bc-9a68010b0a72)

Create an empty chatbot

    Login to your AWS management console and navigate to Amazon Lex from the search bar.

![03](https://github.com/user-attachments/assets/912196cb-8589-46a6-94bb-d6e9bfa93bbe)

Click on ‘Create Bot’.

In the Creation method, go with ‘Create a blank bot’.

Give the chatbot a suitable name and description.

![04](https://github.com/user-attachments/assets/764ef99a-a012-4167-bb9d-7b218151f0ce)

For the IAM role, choose ‘Create a role with basic Amazon Lex permissions’ which will automatically create a role for you.

Choose ‘No’ for the option Children’s Online Privacy Protection Act (COPPA), leave the rest as default and click Next.
![05](https://github.com/user-attachments/assets/ab72b5d3-0512-4a0a-a46e-bb4eb398d2a8)

You can choose the language you want to communicate with the bot and can also choose between different voice interaction modules.
The Intent classification confidence score threshold is like a confidence bar that the system uses to decide if it's sure enough about what the user meant. For now let’s keep it default and click Done.

![07](https://github.com/user-attachments/assets/b940a4ec-c40c-4f76-b7b9-1388f79da5f4)

Your empty chatbot has been created! You will lead on the Intent page where we will work on the conversational flow of the chatbot.

Specify Intent and Slots

![P1](https://github.com/user-attachments/assets/413e7096-92c0-4756-a9e2-579e1349d67d)

Let’s first understand some terms used in Amazon Lex:

- Intent: The goal or purpose behind what the user is saying.
- Utterance: The actual words or phrases spoken or typed by the user.
- Slots: Specific pieces of information or variables within the user's utterance that the system needs to extract.
- Fulfilment: The action or response that the system takes based on the user's intent and the information provided in the slots.

Take a look at this example:

In a pizza ordering chatbot, the intent reflects what the user wants, like ordering pizza or checking an order. Utterances are what users say, such as "I want a large pepperoni pizza." Slots are specific details in these utterances, like size and toppings. Fulfillment is the chatbot's response, confirming the order and providing information.

Now that we are familiar with the basic terms, let’s start with building our chatbot.
In the Intent details, fill in an appropriate Intent name and description.

![08](https://github.com/user-attachments/assets/0b02f118-0756-4c1f-beaa-1bff6c1d21e3)

In the sample utterances, add some inputs that can be asked by the user. Amazon Lex will try to understand the user input by aligning the input with the utterances provided.

![09](https://github.com/user-attachments/assets/f0c1a897-4d13-418a-b32c-59c7fa9d7612)

We will add some more utterances later on after creation of slots.

Go to the slots option and click on Add Slot. We need to take the language in which the text to be translated as a slot. For that we need to first create the Slot type. Go back and click on Save Intent.

Click on back to Intents list and navigate to slot type, add Slot type.

Choose ‘Add blank slot type’ and give a name to the slot type (here ‘language’).
Add some languages as Slot type values similar to given below:
![10](https://github.com/user-attachments/assets/a38f88d6-c102-4f32-b54d-3d3295ca04dd)

(Let us go with the same slot type values for now instead of adding new ones as there would be modifications required in the Lambda function too)

Click on Save slot type.

Navigate back to the Intents to use this slot type for our slot.
![12](https://github.com/user-attachments/assets/c1f8a769-e690-4feb-af67-521a3b2dcc84)


Click on Add Slot. Give the slot name and choose the slot type ‘language’ previously made. Write the prompt where the chatbot asks for user choice for language translation.

Click on Add.

Create another slot ‘text’ which takes the text to be translated as an input. Choose AMAZON.FreeFormInput as the slot type option and enter a suitable prompt asking for the text to be translated. Click on Add.
We can add some more utterances specifying the Slots.

For example : Instead of ‘I want to translate’ , if user inputs ‘I want to translate to French’ with the language specified in the starting intent itself, it should not ask for the language again by running the language prompt. Instead we can specify Slots like below:
![13](https://github.com/user-attachments/assets/bc544063-c339-455c-83c4-a3bb1490a396)


![14](https://github.com/user-attachments/assets/a980770d-3559-4d04-b226-c60c4d719c3f)
This will automatically understand the language slot type if already specified and ask for the input text to be translated directly.

We can also add an Initial response to the initial intent as a feedback message.


![15](https://github.com/user-attachments/assets/b65312bb-f828-47dc-bd64-4fbb25c9e3cf)
Specify Fulfilment

    Fulfilment runs the Lambda function to fulfil the intent and informs users about it’s status once it is complete.

    Write a suitable prompt on successful fulfilment and in case of a failure.

Click on ‘Advanced Options’ and check ‘Use a Lambda function for fulfilment’. Click on update options.

We can also provide a Closing message after completion of the intent.
    Click on ‘Save Intent’.

We have setup the conversation flow of our chatbot. Next, we will proceed to creating the Lambda function for actual serverless text translation.

Create an IAM role

    From your AWS management console, navigate to IAM from your search bar.
    Navigate to roles and Create Role. This role will be used for the Lambda function to provide basic Lambda execution permissions and access to Amazon Translate.
    Select the trusted entity type as AWS service and select Lambda as the use case. Click on Next.

    ![17](https://github.com/user-attachments/assets/182423b6-5b77-408a-a71f-c0350a653a3b)

    Attach the following policies to the role :

    TranslateFullAccess
    AWSLambdaBasicExecutionRole

Click on Next. Enter a suitable name and description for the role and Create Role.
![19](https://github.com/user-attachments/assets/8ef6e98f-01fe-49dd-98bf-d3580eb2ae89)

Creating a Lambda function

1. From your AWS management console, search for Lambda from your search bar.

![18](https://github.com/user-attachments/assets/99ae89e4-0967-4496-962b-f5ad7afcaae1)

2. Click on Create Function. Give the function a suitable name and choose the runtime as Python 3.12.

3. Choose the previously created role by toggling the default execution role option. Keep the rest of the values as default and ‘Create Function’.

4. Let us first look at the Lambda code and we will understand the code below:

python
import boto3

def lambda_handler(event, context):
    try:
        input_text = event['sessionState']['intent']['slots']['text']['value']['interpretedValue'].strip()
        language_slot = event['sessionState']['intent']['slots']['language']['value']['interpretedValue']

        if not input_text:
            raise ValueError("Input text is empty.")

        language_codes = {
            'French': 'fr',
            'Japanese': 'ja',
            'Chinese': 'zh',
            'Spanish': 'es',
            'German': 'de',
            'Norwegian': 'no'
        }


        if language_slot not in language_codes:
            raise ValueError(f"Unsupported language: {language_slot}")

        target_language_code = language_codes[language_slot]

        # Initialize the Amazon Translate client
        translate_client = boto3.client('translate')

        # Call Amazon Translate to perform translation
        response = translate_client.translate_text(
            Text=input_text,
            SourceLanguageCode='auto',  # Auto-detect source language
            TargetLanguageCode=target_language_code
        )

        translated_text = response['TranslatedText']

        lex_response = {
            "sessionState": {
              "dialogAction": {
                  "type" : "Close"
              },
              "intent" : {
                "name" : "TranslateIntent", #Add your Intent Name
                "state" : "Fulfilled"
              }
            },
            "messages": [
                {
                    "contentType": "PlainText",
                    "content": translated_text
                }
            ]
        }

        return lex_response

    except Exception as error:
        error_message = "Lambda execution error: " + str(error)
        print(error_message)
        lex_error_response = {
            "sessionState": {
              "dialogAction": {
                  "type" : "Close"
              },
              "intent" : {
                "name" : "TranslateIntent",
                "state" : "Fulfilled"
              }
            },
            "messages": [
                {
                    "contentType": "PlainText",
                    "content": error_message
                }
            ]
        }

        return lex_error_response

a) boto3 is used to interact with AWS services through code.

b) Get Input:

input_text = event['sessionState']['intent']['slots']['text']['value']['interpretedValue'].strip()
language_slot = event['sessionState']['intent']['slots']['language']['value']['interpretedValue']


Here, the code extracts the user's input text and the target language from the Lex event. If you have kept different slot names from the ones followed in this documentation, remember to change them accordingly.

c) Language codes:

language_codes = {
            'French': 'fr',
            'Japanese': 'ja',
            'Chinese': 'zh',
            'Spanish': 'es',
            'German': 'de',
            'Norwegian': 'no'
}


These are the language codes format used by Amazon Translate to identify the language to be used. We need to pass this language_code in the translate_text function to translate the input according to the mapped value of the language code.

You can further add more languages as you prefer, however remember to also add them in the slot type language.

d) Check Input:

if not input_text:
    raise ValueError("Input text is empty.")

if language_slot not in language_codes:
    raise ValueError(f"Unsupported language: {language_slot}")


These lines ensure that the input text is not empty and that the target language is supported.

e) Translate:

response = translate_client.translate_text(
    Text=input_text,
    SourceLanguageCode='auto',  # Auto-detect source language
    TargetLanguageCode=target_language_code
)
translated_text = response['TranslatedText']


This section uses Amazon Translate to translate the input text into the target language specified by the user.

f) Prepare Response:

lex_response = {
    "sessionState": {
        "dialogAction": {
            "type": "Close"
        },
        "intent": {
            "name": "TranslateIntent", #Add your intent name
            "state": "Fulfilled"
        }
    },
    "messages": [
        {
            "contentType": "PlainText",
            "content": translated_text
        }
    ]
}


After translation, the code constructs a response for the chatbot with the translated text.

g) Handle Errors:

except Exception as error:
    error_message = "Lambda execution error: " + str(error)
    print(error_message)
    lex_error_response = {
        "sessionState": {
            "dialogAction": {
                "type": "Close"
            },
            "intent": {
                "name": "TranslateIntent",
                "state": "Fulfilled"
            }
        },
        "messages": [
            {
                "contentType": "PlainText",
                "content": error_message
            }
        ]
    }
    return lex_error_response


This part catches any errors that occur during the process and sends an appropriate error message back to the chatbot.

5. Click on ‘Deploy’ to deploy the Lambda function.


   Testing the Lambda function

1. To test the Lambda function, click on Test and configure the Test event.

2. Give a suitable name to the test event and keep the Event JSON in the format below:


![20](https://github.com/user-attachments/assets/81861b4c-5a5e-423f-b1c8-1814f4d8c076)

Testing the Lambda function

The reason that we are providing this format is because your Lambda function gets an input from the Amazon Lex in this specified format.

3. Save the test event and click on Test again to Invoke the event.

The function gives an output in a JSON format which is the required format to give an input for Amazon Lex. It will get the message content and display it to the user.

Next, let’s test out the chatbot working.

Testing the chatbot

    Navigate to the Intent page of your previously created Chatbot. Before testing it, we need to specify the Lambda function to be used by Lex.



    Click on the settings option present on your top right of the chatbot window and choose the Source of Lambda function as your previous created function.

    Now you are ready to go! Test out the chatbot, here are some conversations:

    ![02](https://github.com/user-attachments/assets/8a7fab98-1d01-410e-8cb2-5ef48dad42b1)


































    
