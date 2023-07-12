#Getting started



##Requirements

trail work with the open source experiment tracker MLFlow. If you have never worked with MLflow the [official documentation](https://mlflow.org/docs/latest/index.html) is a good starting point.

##Before the first experiment

1. Create a user config file under 
Primary path: ```trailconfig.yml``` or
Secondary path: ```~/.config/trail.yml```

``` yaml
username: <YOUR_USERNAME>
password: <YOUR_PASSWORD>
projects:
  myProjectAlias:
    id: 1234ABC
    parentExperimentId: ABCD123
```
Fill in the ```username``` and ```password``` and update the ```id``` and ```parentExperimentId```

You can find the ```ìd``` in the web app experiment view in the top right after you have created a new project in the project view, and selected that project in the top right of the experiment view:

![ID_in_Flow](/ID_in_Flow.png){: style="width: 50%;" }

The ```parentExperimentId``` can be found in bottom ot the root-node or the bottom-right of any other node. Depending on which node you choose as a parent node, the new experiment will be a child of that node.

![parentID_in_Flow](/parent_ID_in_Flow.png){: style="width: 50%;" }

The parentID will get updated automatically after every run. You only have to update the parentID if you want to open up a new branch in the tree.
E.g. if you want to start a new branch from the root node, you have to update the parentID to the root node ID.

## Getting your code ready

1. Install the trail python package
``` shell
pip install trailml
```

2. Import the mlflow and trail package
``` python
import mlflow
from trail import Trail
```

3. Start a run and wrap your code with the trail context manager. The identifier "myProjectAlias" has to match the identifier in the config file (but is independent of any experiment names and the like).
``` python
with mlflow.start_run() as run:
    with Trail('myProjectAlias') as trail:
      ...your training code...
```

Now every metric, parameter and artifact that you logged with mlflow will be tracked in the trail app.

If you want to add further information to an experiment you can do so by adding one of the following methods to the trail context manager:

- logging a metric:
``` python
mlflow.log_metric('metric_name', metric_value)
```
- logging a parameter:
``` python
mlflow.log_param('param_name', param_value)
```
- logging an artifact:
``` python
trail.put_artifact('path/to/artifact', "name", "tag")
```

- add a hypothesis:
``` python
trail.put_hypothesis("Try to improve the accuracy by adding more layers to the model")
```

- add a dataset statistic
``` python
from ydata_profiling import ProfileReport
PROFILE_PATH = "./path/to/train_data_report.html"
# train_data is a pandas dataframe where your data is stored
profile = ProfileReport(train_data, title="train_data Profiling Report")
profile.to_file(PROFILE_PATH)
trail.put_artifact(PROFILE_PATH, "name", "tag")
```