### Before the start ###
Please **read aloud of instruction** and **think aloud of what you are thinking or doing during the session**. We cannot read your mind so your help is deeply appreciated!

## Assignment Brief ##
Your client Contoso Enterprise is creating an application using AI models, and as a lead data scientist on this project, you are asked to meet 3 objectives over the course of three sessions: 

1. Train a model & move the model to the cloud
2. Fine tuning the model & build the pipeline
3. Deploy the model

In this session, your goal is to deploy the model ("model-2") that is already packaged, using the deployment.md as a reference.

You can find the model-2 package under C:\Users\username\azureml-examples\cli\endpoints\online\model-2 (please do not open other notebook files in the folder)

## TO-DO LIST ##

1. Do the prep.
2. Create a new folder under Users/username/azureml-examples/cli/endpoints/online/ 
3. Create a file (.py or .ipynb) under Users/username/azureml-examples/cli/endpoints/online/folder_name_of_your_choice and copy & paste the deployment script below.
4. Use the Deployment.md as a reference to complete the missing information in the script to deploy "model-2" via local endpoint and test it.
5. Make further changes to the script to create an online endpoint and test it.
6. (if time permits) Get details of the endpoint
7. (if time permits) Get the logs for the new deployment
8. (if time permits) Delete the endpoint

## Prep ##
1. Open the terminal of your choice and type the following:

```python
az login
```
2. Enter the login credential provided to you.

## Provided local deployment script ##

```python
#import required libraries
from azure.ai.ml import MLClient
from azure.ai.ml.entities import ManagedOnlineEndpoint, ManagedOnlineDeployment, Model, Environment, CodeConfiguration
from azure.identity import InteractiveBrowserCredential, DefaultAzureCredential

#Enter details of your Azure Machine Learning workspace
subscription_id = 'SUBSCRIPTION_ID'
resource_group = 'RESOURCE_GROUP'
workspace = 'WORKSPACE'

#connect to the workspace
ml_client = MLClient(DefaultAzureCredential(), subscription_id, resource_group, workspace)

#running docker
!docker ps
```

```python
# Creating a local endpoint 
import datetime

local_endpoint_name = "local-" + datetime.datetime.now().strftime("%m%d%H%M%f")
```
```python
# create an online endpoint
endpoint = ManagedOnlineEndpoint(
    name=local_endpoint_name, description="this is a sample local endpoint"
)

ml_client.online_endpoints.begin_create_or_update(endpoint, local=True)

model = Model(path="<INSERT PATH>")
env = Environment(
    conda_file="<INSERT PATH>",
    image="mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210727.v1",
)

blue_deployment = ManagedOnlineDeployment(
    name="blue",
    endpoint_name=local_endpoint_name,
    model=model,
    environment=env,
    code_configuration=CodeConfiguration(
        code="<INSERT SCORING PATH>", 
        scoring_script="<INSERT SCORING SCRIPT>"
    ),
    instance_type="Standard_F2s_v2",
    instance_count=1,
)

ml_client.online_deployments.begin_create_or_update(
    deployment=blue_deployment, local=True
)
```

## Session Survey ##
Your moderator will let you know when to take [the survey] (https://forms.office.com/r/XQYHxqykvS )
