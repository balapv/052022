### Before the start ###
Please **read aloud of instruction** and **think aloud of what you are thinking or doing during the session**. We cannot read your mind so your help is deeply appreciated!

## Assignment Brief ##
Your client Contoso Enterprise is creating an application using AI models, and as a lead data scientist on this project, you are asked to meet 3 objectives over the course of three sessions: : 

1. Train a model & move the model to the cloud
2. Fine tuning the model & build the pipeline
3. Deploy the model

In this session, you'll be refine a provided training script using ``hyperparameter sweep`` and create a 2-step pipeline afterwards.



## TO-DO LIST ##

**Hyperparameter Sweep**
1. Do the prep.
2. Create a file (.py or .ipynb) under Users/username/azureml-examples/sdk/jobs/single-step/lightgbm/iris (please do not open any other notebook file in the folder). 
3. Copy and paste the provided script below into the file you created.
4. Use the provided Hyperparameter.md as a reference to create and submit a hyperparameter sweep job.

**Pipeline**
1. Create a file (ex. .py or .ipynb) under Users/username/azureml-examples/sdk/jobs/pipelines/1c_pipeline_with_hyperparameter_sweep
2. Copy & paste the script you created from the previous task ("create and submit a hyperparameter job") and use the provided Pipeline document to create a 2-step pipeline job.

## Prep ##
1. Open the terminal of your choice and type the following:

```python
az login
```
2. Enter the login credential provided to you.

## Provided script ##

```python
from azure.ai.ml import MLClient, command, Input
from azure.ai.ml.sweep import Choice, Uniform
from azure.ai.ml.entities import AmlCompute
from azure.identity import DefaultAzureCredential

#Enter details of your AML workspace
subscription_id = '<SUBSCRIPTION_ID>'
resource_group = '<RESOURCE_GROUP>'
workspace = '<WORKSPACE>'

#connect to the workspace
ml_client = MLClient(DefaultAzureCredential(), subscription_id, resource_group, workspace)
print('Library configuration succeeded')

# specify aml compute name.
cpu_compute_target = 'cpu-cluster'

try:
    ml_client.compute.get(cpu_compute_target)
except Exception:
    print('Creating a new cpu compute target...')
    compute = AmlCompute(name=cpu_compute_target, size="STANDARD_D2_V2", min_instances=0, max_instances=4)
    ml_client.compute.begin_create_or_update(compute)

#define the command
command_job=command(
    code='./src',
    inputs={'iris_csv':Input(type='uri_file', path='https://azuremlexamples.blob.core.windows.net/datasets/iris.csv')},
    command = 'python main.py --iris-csv ${{inputs.iris_csv}}',
    environment='AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu@latest',
    compute='cpu-cluster',
)

# submit the command
returned_job = ml_client.jobs.create_or_update(command_job)
# get a URL for the status of the job
returned_job.services["Studio"].endpoint
```

## Session Survey ##
Your moderator will let you know when to take: 

[the hyperparameter survey](https://forms.office.com/r/XQYHxqykvS )

[the pipeline survey](https://forms.office.com/r/XQYHxqykvS )
