## Improve the model using hyperparameter sweep

Now that we have run a job on Azure, let us make it better using Hyperparameter tuning. Also called hyperparameter optimization, this is the process of finding the configuration of hyperparameters that results in the best performance. Azure Machine Learning provides a `sweep` function on the `command` to do hyperparameter tuning. 

To perform a sweep, there needs to be input(s) against which the sweep needs to be performed. These inputs can have a discrete or continuos value. The `sweep` function will run the `command` multiple times using different combination of input values specified. Each input is a dictionary of name value pairs. The key is the name of the hyperparameter and the value is the parameter expression. 

Let us improve our model by sweeping on `learning_rate` and `boosting` inputs to the script. In the previous step, we used a specific value for these, but now we will use a range or  choice of values.

<!--[!notebook-python[] (~/azureml-examples/blob/sdk-preview/sdk/jobs/single-step/lightgbm/iris/lightgbm-iris-sweep.ipynb?name=search-space)]-->

```python
# we will reuse the command_job created before. we call it as a function so that we can apply inputs
# we do not apply the 'iris_csv' input again -- we will just use what was already defined earlier
from azure.ai.ml.sweep import Choice, Uniform
command_job_for_sweep = command_job(
    learning_rate=Uniform(min_value=0.01, max_value=0.9),
    boosting=Choice(values=["gbdt", "dart"]),
)
```

Now that we have defined the parameters, let us run the sweep

<!--[!notebook-python[] (~/azureml-examples/blob/sdk-preview/sdk/jobs/single-step/lightgbm/iris/lightgbm-iris-sweep.ipynb?name=configure-sweep)]-->

```python
# apply the sweep parameter to obtain the sweep_job
sweep_job = command_job_for_sweep.sweep(
    compute='cpu-cluster',
    sampling_algorithm='random',
    primary_metric='test-multi_logloss',
    goal='Minimize'
)

#define the limits for this sweep
sweep_job.set_limits(max_total_trials=20, max_concurrent_trials=10, timeout=7200)
```

<!--[!notebook-python[] (~/azureml-examples/blob/sdk-preview/sdk/jobs/single-step/lightgbm/iris/lightgbm-iris-sweep.ipynb?name=run-sweep)]-->

```python
# submit the sweep
returned_sweep_job = ml_client.create_or_update(sweep_job)
# get a URL for the status of the job
returned_sweep_job.services["Studio"].endpoint
```

As seen above, the `sweep` function allows user to configure the following key aspects:

* `sampling_algorithm`- The hyperparameter sampling algorithm to use over the search_space. Allowed values are `random`, `grid` and `bayesian`.
* `objective` - the objective of the sweep
  * `primary_metric` - The name of the primary metric reported by each trial job. The metric must be logged in the user's training script using `mlflow.log_metric()` with the same corresponding metric name.
  * `goal` - The optimization goal of the objective.primary_metric. The allowed values are `maximize` and `minimize`.
* `compute` - Name of the compute target to execute the job on.
* `limits` - Limits for the sweep job

Once this job completes, you can look at the metrics and the job details in the [Azure ML Portal](https://ml.azure.com/). The job details page will identify the best performing child run.
