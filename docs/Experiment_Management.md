#Experiment Management


##Introduction

trail work with the open source experiment tracker MLFlow. Don't worry if you have never worked with MLflow the necessary basics are explained in the next section. 
For a more thourough read we recommend the [official documentation](https://mlflow.org/docs/latest/index.html) as a good starting point.

##Before the first experiment

Go to the webapp and create a new project (see [AI Registry](AI_Registry.md) for more information).

Run two commands in your CLI:

1.  ```pip install trailml```
2.  ```trail init```
   
The trail helper will guide you through the process of creating your config file, which will look like this:

``` yaml
username: <YOUR_USERNAME>
password: <YOUR_PASSWORD>
projects:
  id: 1234ABC
  parentExperimentId: ABCD123
```
You can find the ```ìd``` in the web app experiment view in the top left after you have created a new project in the project view, and selected that project in the top right of the experiment view:

![ID_in_Flow](/ID_in_Flow.png){: style="width: 50%;" }

The ```parentExperimentId``` can be found in bottom ot the root-node or the bottom-right of any other node. Depending on which node you choose as a parent node, the new experiment will be a child of that node.

![parentID_in_Flow](/parent_ID_in_Flow.png){: style="width: 50%;" }

The ```parentID``` will get updated automatically after every run. You only have to update the ```parentID``` if you want to open up a new branch in the tree.
E.g. if you want to start a new branch from the root node, you have to update the ```parentID``` to the root node ID.

Alternatively you can create a ```trailconfig.yaml``` file manually and fill in your ```username``` and ```password``` and update the ```id``` and ```parentExperimentId``` as described above.


## How to prepare your code


1. Import mlflow and trail
``` python
import mlflow
from trail import Trail
```

1. Start a run and wrap your code with the trail context manager. 
``` python
with mlflow.start_run() as run:
    with Trail() as trail:
      ...your training code...
```

Now every metric, parameter and artifact that you logged with mlflow will be tracked in the trail app.

## Available functions
If you want to add further information to an experiment you can do so by adding one of the following methods to the trail context manager:

### How to log parameters & metrics

mlflow provides [autologging](https://mlflow.org/docs/latest/tracking.html#automatic-logging) functionality for the following libraries:

- [scikit-learn](https://mlflow.org/docs/latest/tracking.html#automatic-logging-from-scikit-learn)
- [Keras](https://mlflow.org/docs/latest/tracking.html#keras)
- [Gluon](https://mlflow.org/docs/latest/tracking.html#gluon)
- [XGBoost](https://mlflow.org/docs/latest/tracking.html#xgboost)
- [LightGBM](https://mlflow.org/docs/latest/tracking.html#lightgbm)
- [statsmodels](https://mlflow.org/docs/latest/tracking.html#statsmodels)
- [Spark](https://mlflow.org/docs/latest/tracking.html#spark)
- [fastai](https://mlflow.org/docs/latest/tracking.html#fastai)
- [PyTorch](https://mlflow.org/docs/latest/tracking.html#pytorch)

Autologging can be enabled by calling ``` mlflow.autolog()``` before the training code and it captures all available parameters, metrics and metadata.


If you want to log parameters and metrics manually, you can do so by using the following functions:

- log a metric:
``` python
mlflow.log_metric('metric_name', metric_value)
```
- log a parameter:
``` python
mlflow.log_param('param_name', param_value)
```

### How to log artifacts
You can choose either of the following methods to log artifacts:

- log an artifact with mlflow:
``` python
mlflow.log_artifact("file_name")
```
  
- log an artifact with trail:
``` python
trail.put_artifact('path/to/artifact', "name", "tag")
```


### How to log source code

- log folder from python

``` python
trail.upload_folder(local_folder_path: str, expiration_seconds=300)
```

- log folder from CLI

``` shell
trail --upload-folder file_name
```

- log file from CLI

``` shell
trail --upload-file file_name

```
- log jupyer-notebook with artifacts

``` shell
trail --upload-notebook-artifact file_name

```

### How to log dataset statistics for tabular data

- add a dataset statistic
``` python
from ydata_profiling import ProfileReport
PROFILE_PATH = "./path/to/train_data_report.html"
# train_data is a pandas dataframe where your data is stored
profile = ProfileReport(train_data, title="train_data Profiling Report")
profile.to_file(PROFILE_PATH)
trail.put_artifact(PROFILE_PATH, "name", "tag")
```

### Further logging functions

- add a hypothesis:
``` python
trail.put_hypothesis("Try to improve the accuracy by adding more layers to the model")
```