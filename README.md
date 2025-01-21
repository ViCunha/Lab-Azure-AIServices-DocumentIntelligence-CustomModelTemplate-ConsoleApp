
### Overview
---
In this exercise, you'll use the Azure Document Intelligence service to train and test a custom model using the Python or .NET SDK.
   
### Key Aspects
---
Azure AI Serivces # Document Intelligence

### Environments
---
- Microsoft Lab Sandbox
  - Microsfot Visual Studio Code
  - .NET 8 (Long Term Support)

Azure Portal
- Valid subscription

### Diagrams
---
![image](https://github.com/user-attachments/assets/0b9f2056-cd28-429b-b7ea-06c5fe5f42d8)


### Actions
---
Prepare to develop an app in Visual Studio Code
Now let's use the service SDK to develop an app using Visual Studio Code. The code files for your app have been provided in a GitHub repo.

- Start Visual Studio Code

- Open the palette (SHIFT+CTRL+P) and run a Git: Clone command to clone the https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence repository to a local folder (it doesn't matter which folder).

- When the repository has been cloned, open the folder in Visual Studio Code.
  Note: If Visual Studio Code shows you a pop-up message to prompt you to trust the code you are opening, click on Yes, I trust the authors option in the pop-up.

- Wait while additional files are installed to support the C# code projects in the repo.
  Note: If you are prompted to add required assets to build and debug, select Not Now. If you are prompted with the Message Detected an Azure Function Project in folder, you can safely close that message.

Create a Azure AI Document Intelligence resource
To use the Azure AI Document Intelligence service, you need a Azure AI Document Intelligence or Azure AI Services resource in your Azure subscription. You'll use the Azure portal to create a resource.

- In a browser tab, open the Azure portal at https://portal.azure.com, signing in with the Microsoft account associated with your Azure subscription.

- On the Azure portal home page, navigate to the top search box and type Document Intelligence and then press Enter.

- On the Document Intelligence page, select Create.

- On the Create Document Intelligence page, use the following to configure your resource:
  - Subscription: Your Azure subscription.
  - Resource group: Select or create a resource group with a unique name such as DocIntelligenceResources.
  - Region: select a region near you.
  - Name: Enter a globally unique name.
  - Pricing tier: select Free F0 (if you don't have a Free tier available, select Standard S0).

- Then select Review + create, and Create. Wait while Azure creates the Azure AI Document Intelligence resource.

- When the deployment is complete, select Go to resource to view the resource's Overview page.


Gather documents for training  
You'll use the sample forms such as this one to train a test a model

- Return to Visual Studio Code. In the Explorer pane, open the Labfiles/02-custom-document-intelligence folder and expand the sample-forms folder. Notice there are files ending in .json and .jpg in the folder.

- You will use the .jpg files to train your model.
  The .json files have been generated for you and contain label information. The files will be uploaded into your blob storage container alongside the forms.
  You can view the images we are using in the sample-forms folder by selecting them on Visual Studio Code.

- Return to the Azure portal and navigate to your resource's Overview page if you're not already there. Under the Essentials section, view the Resource group in which you created the Document Intelligence resource

- On the Overview page for your resource group, note the Subscription ID and Location. 
You will need these values, along with your resource group name in subsequent steps.

- In Visual Studio Code, in the Explorer pane, browse to the Labfiles/02-custom-document-intelligence folder and expand the CSharp or Python folder depending on your language preference. Each folder contains the language-specific files for an app into which you're you're going to integrate Azure OpenAI functionality.

- Right-click the CSharp or Python folder containing your code files and select Open an integrated terminal.

- In the terminal, run the following command to login to Azure. The az login command will open the Microsoft Edge browser, login with the same account you used to create the Azure AI Document Intelligence resource. Once you are logged in, close the browser window.
```
az login
```

- Return to Visual Studio Code. In the terminal pane, run the following command to list the Azure locations.
```
az account list-locations -o table
```

- In the output, find the Name value that corresponds with the location of your resource group (for example, for East US the corresponding name is eastus). 

- In Visual Studio Code, in the Labfiles/02-custom-document-intelligence folder, select setup.cmd. You will use this script to run the Azure command line interface (CLI) commands required to create the other Azure resources you need.

- In the setup.cmd script, review the commands. 
  - The program will:
    - Create a storage account in your Azure resource group
    - Upload files from your local sampleforms folder to a container called sampleforms in the storage account
    - Print a Shared Access Signature URI

- Modify the subscription_id, resource_group, and location variable declarations with the appropriate values for the subscription, resource group, and location name where you deployed the Document Intelligence resource. Then save your changes.

- Leave the expiry_date variable as it is for the exercise. This variable is used when generating the Shared Access Signature (SAS) URI. In practice, you will want to set an appropriate expiry date for your SAS.

- In the terminal for the Labfiles/02-custom-document-intelligence folder, enter the following command to run the script:

```
$currentdir=(Get-Item .).FullName
cd ..
./setup.cmd
cd $currentdir
```

- When the script completes, review the displayed output.

- In the Azure portal, refresh your resource group and verify that it contains the Azure Storage account just created. Open the storage account and in the pane on the left, select Storage browser. Then in Storage Browser, expand Blob containers and select the sampleforms container to verify that the files have been uploaded from your local 02-custom-document-intelligence/sample-forms folder.


Train the model using Document Intelligence Studio  
Now you will train the model using the files uploaded to the storage account.

- In your browser, navigate to the Document Intelligence Studio at https://documentintelligence.ai.azure.com/studio.

- Scroll down to the Custom models section and select the Custom extraction model tile.

- If you are asked to sign into your account, use your Azure credentials.

- If you are asked which Azure AI Document Intelligence resource to use, select the subscription and resource name you used when you created the Azure AI Document Intelligence resource.

- Under My Projects, select Create a project. Use the following configurations:
  - Project name: Enter a unique name.
    - Select Continue.

  - Configure service resource: Select the subscription, resource group, and document intelligence resource you created previously in this lab. Check the Set as default box. Keep the default API version.
    - Select Continue.

  - Connect training data source: Select the subscription, resource group, and storage account that was created by the setup script. Check the Set as default box. Select the sampleforms blob container, and leave the folder path blank.
    - Select Continue.
    - Select Create project

- Once your project is created, on the top right of the screen, select Train to train your model. Use the following configurations:
  - Model ID: Provide a globally unique name (you'll need the model ID name in the next step).
  - Build Mode: Template.

- Select Go to Models.

- Training can take some time. You'll see a notification when it's complete.

- Test your custom Document Intelligence model

- Return to Visual Studio Code. In the terminal, install the Document Intelligence package by running the appropriate command for your language preference:
```
dotnet add package Azure.AI.FormRecognizer --version 4.1.0
```

- In Visual Studio Code, in the Labfiles/02-custom-document-intelligence folder, select the language you are using. Edit the configuration file (appsettings.json or .env, depending on your language preference) with the following values:
  - Your Document Intelligence endpoint.
  - Your Document Intelligence key.
  - The Model ID generated you provided when training your model. You can find this on the Models page of the Document Intelligence Studio. Save your changes.

- In Visual Studio Code, open the code file for your client application (Program.cs for C#, test-model.py for Python) and review the code it contains, particularly that the image in the URL refers to the file in this GitHub repo on the web.

- Return the terminal, and enter the following command to run the program:

```
dotnet build
dotnet run
```

Clean up resources  

- In the “search resources, services, and doc”, type and select Resource Groups

- Click on the resource group created

- Select Delete resource group and follow the directions to delete the resource group and all of the resources it contains

### Media
---
![image](https://github.com/user-attachments/assets/9b022143-093c-4572-8c9d-2c97585b3e74)
---
![image](https://github.com/user-attachments/assets/4523edd7-5f01-4f00-aaeb-644f0a328b71)
---
![image](https://github.com/user-attachments/assets/04cf7528-d168-4d28-97c6-1ecd7dbaffca)


### References
---
- [[Bacon ipsum dolor](https://#)](https://learn.microsoft.com/en-us/training/modules/work-form-recognizer/8-exercise-extract-data-from-custom-forms)
