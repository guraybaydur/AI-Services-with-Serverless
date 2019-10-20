# How to use multiple AI Services with Functions

## Introduction
Artificial Intelligence (AI) is becoming the ultimate technology that we apply in our daily routines. Each day we get benefit from AI tools with search recommendations, image detection and so on. We are on the verge of an era where every individual is educated and grown up with AI and at this point, to keep up with this movement, we need to learn and implement AI on our own. 

Considering fast implementation, Serverless is the concept where developers only focus on writing their code regardless of server maintenance, upgrades, the operating system etc. They implement functions in any programming language they want and call them on demand. 

In this tutorial, with the help of Serverless technology, we use multiple IBM Cloud Watson (AI) Services consecutively to practice how AI can be applied in rapid way. We input texts in different languages to IBM Watson Language Translator Action to retrieve English translation of the text and then we give the English text into IBM Watson Natural Language Understanding Action to detect the emotions inside the text. 

## Prerequisites

- IBM Cloud Account
- IBM Cloud CLI Tools

## Estimated Time
30-35 minutes

## Steps

## 1. Install Watson Language Translator Package, modify codes and params
- Login to IBM Cloud with your credentials.
- From the search bar at the top, search for Functions.
![alt text](https://github.ibm.com/Guray-Baydur/Cloud-Functions-Multiple-AI-Services/blob/master/Screenshots/Screen%20Shot%202019-10-20%20at%2015.01.19.png)
- Click Start Creating.
- Choose Install Packages.
- From the Installable Packages, choose Watson.
- Choose Language Translator and click install. You will be directed to Actions page where you can find predefined Watson Language Translator Actions Templates.
- First we need to identify the language from the given text. Therefore click "identify" under "language-translator-v3".
- Copy the whole code inside identify.js which is in our repository and paste it into identify action that we opened in Step 7. Please note the code between the green comment field inside function main, it indicates that we will use two parameters inside the function. "text" parameter is the text that we will investigate. "version" parameter is the Language Translator version that we will use. Click Save.
- Now we need to add the two parameters that we discussed. From the left sidebar, click Parameters. Click Add Parameter. Fill the empty field under "Parameter Name" with text. Fill the empty field under "Parameter Value" with some text you want to investigate (in our case it is "Hello! This is the sample text."). Click Add from top right  and fill the empty field under "Parameter Name" with version. Fill the empty field under "Parameter Value" with "2018-05-01". Click Save.
- Go back to Actions main page (cloud.ibm.com/functions/actions). Click translate under "language-translator-v3".
- Copy the whole code inside translate.js which is in our repository and paste it into translate action that we opened in Step 10. Please note the code between the green comment field inside function main. We first extract the result from identify action and then check if the language is already in English or not. If the language is not English we make an api call to Language Translator with the source and target language parameters. Otherwise we send the English text directly to NLU Function we are going to implement without making any changes. Click Save.

## 2. Install Watson Natural Language Understanding Package, modify the code and params

- Go back to Functions main page (cloud.ibm.com/functions).
- Choose Install Packages.
- From the Installable Packages, choose Watson.
- Choose Natural Language Understanding and click install. You will be directed to Actions page where you can find predefined Watson Natural Language Understanding Actions Templates.
- Now we need to analyze the translated text. Therefore click "analyze" under "natural-language-understanding-v1".
- Copy the whole code inside analyze.js which is in our repository and paste it into analyze action that we opened in Step 16. Please note the code between the green comment fields (there are two fields) inside function main. First we give the credentials. Then we check the text whether it is in English or not (it is just for parsing purposes). Finally,between the second comment field, we alter the code in such a way that it gives no bugs. Click Save.
- Now we need to add the two parameters, version and features to analyze. From the left sidebar, click Parameters. Click Add Parameter. Fill the empty field under "Parameter Name" with version. Fill the empty field under "Parameter Value" with "2019-07-12". Click Add from top right  and fill the empty field under "Parameter Name" with features. Fill the empty field under "Parameter Value" with {"emotion":{"limit":2}}. Click Save.

## 3. Create a Sequence

- Go back to Functions main page (cloud.ibm.com/functions).
- Click Start Creating. It is now to connect all these three functions by making them a sequence where output of one is the input of next function.
- Click Create Sequence. Give the sequence a name you want. Select Default Package and Select Existing and choose "language-translator-v3/identify" to start the sequence with our first function. Click Create.
- From your sequence page, click add in top right. Click Select Existing and choose "language-translator-v3/translate". Click Add.
- From your sequence page, click add in top right. Click Select Existing and choose "natural-language-understanding-v1/analyze". Click Add. Click Save.
- It is time to create our Language Trnaslator and Natural Language Understanding Services. We will do this from CLI. Open your terminal.

- Login to IBM Cloud from terminal by entering the code below
```
    ibmcloud login
```
- Set your region and space automatically
```
    ibmcloud target --cf
```
- Create a free Language Translator instance
```
    ibmcloud service create language_translator lite demo-language-translator 
```
- Create a free Language Translator instance
```
    ibmcloud service create natural-language-understanding free demo-natural-language-understanding  
```
- Create a credential for your Language Translator service in order to use it in Functions 
```
    ibmcloud service key-create demo-natural-language-understanding demo-natural-language-understanding-key
```
- Create a credential for your Natural Language Understanding service in order to use it in Functions 
```
    ibmcloud service key-create demo-language-translator demo-language-translator-key
```
- Copy your Language Translator and Natural Language Understanding package name to your clipboard by listing your packages.
```
    ibmcloud fn list
``` 
- Bind your related service to the related packages
```
    ibmcloud fn service bind language_translator <your-language-translator-package-name>

    ibmcloud fn service bind natural-language-understanding <your-natural-language-understanding-package-name>
````
## 4. Adjust credentials and test

- Now go to your analyze action. From the left sidebar. Click Parameters. You will see a new parameter "__bx_cred" is added. Change its name to credentials. 
- Go to your sequence page, click Invoke. You will see different emotions detected from your text with different certainty values

## Summary

Artificial Intelligence is getting stronger with combination of its practices and it is inevitable to see the great outcomes that it serves to us. Having a chance to quickly experience this technology with IBM Cloud Functions gives us the agility to innovate like we never did before.

## Related Links

https://developer.ibm.com/tutorials/leverage-deep-learning-in-apache-openwhisk-ibm-cloud-functions/

 #### Additional Note:  We constructed the sequence but we did not open it as a web endpoint to call from outside. Hence, from left side bar of your sequence page, click Endpoints and mark Enable as Web Action. Click Save. Copy the URL under Web Action and put .json at the end of the URL. You can now use this endpoint to call the sequence.



 
