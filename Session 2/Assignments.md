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
1. RUN the prep.
2. Create a file (.py or .ipynb) under Users/username/azureml-examples/sdk/jobs/single-step/lightgbm/iris (please do not open any other notebook file in the folder). 
3. Copy and paste the provided script in the [sweep section](#provided-script-for-sweep) into the file you created.
4. Use the provided Hyperparameter.md as a reference to create and submit a hyperparameter sweep job.

**Pipeline**
1. Go to the folder Users/username/azureml-examples/sdk/jobs/pipelines/1c_pipeline_with_hyperparameter_sweep
2. This folder has 2 script files - train.py and predict.py inside sub-folders
3. The command to train and predict using these 2 files is given in the [pipeline section](#provided-script-for-pipeline)
4. Create a file (ex. .py or .ipynb) under Users/username/azureml-examples/sdk/jobs/pipelines/1c_pipeline_with_hyperparameter_sweep
5. Copy and past the 2 commands into your file 
6. Create a pipeline which runs the train and predict steps one after the other
7. Use the Reference_pipeline.ipynb notebook for details on how to create a pipeline

## Prep ##
1. Open the terminal of your choice and type the following:

```python
az login
```
2. Enter the login credential provided to you.

## Provided script for Sweep ##

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
    inputs={
        'iris_csv':Input(
            type='uri_file', 
            path='https://azuremlexamples.blob.core.windows.net/datasets/iris.csv'
        ),
        "learning_rate": 0.9,
        "boosting": "gbdt",
    },
    command = 'python main.py --iris-csv ${{inputs.iris_csv}}',
    environment='AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu@latest',
    compute='cpu-cluster',
)

# submit the command
returned_job = ml_client.jobs.create_or_update(command_job)
# get a URL for the status of the job
returned_job.services["Studio"].endpoint
```

## Provided script for Pipeline ##
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
    
# Create the train command
from azure.ai.ml import command, Input
# define the command
train_func = command(
    code="./train-src",
    command="python train.py --data inputs.data_csv}} --fit_intercept ${{inputs.fit_intercept}} --normalize ${{inputs.normalize}} --model_output ${{outputs.model_path}} --test_data ${{outputs.test_data}}",
    environment="AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu@latest",
    inputs={
        "data_csv": Input(
            type="uri_file",
        ),
        "fit_intercept": False,
        "normalize": False,
    },
    outputs={
        "model_path": Output(
            type="uri_folder",
        ),
        "test_data":  Output(
            type="uri_folder",
        ),
    },
    compute="cpu-cluster",
)

#Create the predict command
from azure.ai.ml import command, Input, Output
# define the command
predict = command(
    code="./predict-src",
    command="python predict.py --model ${{inputs.model}} --test_data ${{inputs.test_data}} --predict_result ${{outputs.predict_result}}",
    environment="AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu@latest",
    inputs={
        "model": Input(
            type="uri_folder",
        ),
        "test_data": Input(
            type="uri_folder",
        ),
    },
    outputs={
        "predict_result": Output(
            type="uri_folder",
        ),
    },
    compute="cpu-cluster",
)
```


## Session Survey ##
Your moderator will let you know when to take: 

[the hyperparameter survey](https://forms.office.com/r/XQYHxqykvS )

[the pipeline survey](https://forms.office.com/r/XQYHxqykvS )
