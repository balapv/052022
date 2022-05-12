### Before the start ###
Please **read aloud of this doc** and **think aloud of what you are thinking or doing during the session**. We cannot read your mind so your help is deeply appreciated!

## Assignment Brief ##
Your client Contoso Enterprise is creating an application using AI models, and as a lead data scientist on this project, you are asked to meet 3 objectives over the course of three sessions: 

1. Train a model & move the model to the cloud
2. Fine tuning the model & build the pipeline
3. Deploy the model

In this study, you are already provided with a sample code and a dataset. Think of this as your coworker left you some code for you to build upon. Your goal today is to use the reference document and the provided script to train the model on the cloud and review the log.

## TO-DO LIST ##

You will be using the diabetes data and a half-filled script to complete the assignment. This is a different dataset from the one used in the reference document ("Iris").

Your data location: https://azuremlexamples.blob.core.windows.net/datasets/diabetes.csv
Your training code location: [./src/main.py](./src/main.py)

1. Do the prep.
2. Create a file (.py or .ipynb) in this folder. 
3. Copy and paste the provided script below into the file you created.
4. Use the provided diabetes dataset, training code, and [attached reference](./Reference.md) to create and submit an online training job.
5. Monitor the job and fix any issues that occurred.

## Prep ##
1. Open the terminal of your choice and type the following:

```python
az login
```
2. Enter the login credential provided to you.
3. Open the web browser and type ml.azure.com
4. Enter the same login credential provided to you.
5. Minimize the web browser window after you sign in. 
This is to show you there is a web app UI version of Azure ML. For your assignment, you'll be using Azure ML SDK.

## Provided script ##

```python
#Enter details of imported libraries
from 'INSERT CODE' import 'IMPORTED LIBRARIES GO HERE'

#Enter details of your AzureML workspace (you need to look them up)
subscription_id = 'SUBSCRIPTION_ID'
resource_group = 'RESOURCE_GROUP'
workspace = 'WORKSPACE_NAME'

#connect to the workspace
<CODE GOES HERE>

# specify compute name.
<CODE GOES HERE>

#define the command
command_job=command(
    code='./src', #local path where the code is stored
    inputs={'<CODE GOES HERE>'},
    command = '<CODE GOES HERE>',
    environment='AzureML-sklearn-0.24-ubuntu18.04-py37-cpu@latest',
    compute='cpu-cluster'
)

#submit the command job
<CODE GOES HERE>
#get a URL for the status of the job
<CODE GOES HERE>
```

## Session Survey ##
Your moderator will let you know when to take [the survey](https://forms.office.com/r/9Yg4uxzqrs )
