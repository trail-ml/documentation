#Quickstart

Under this link you can find a repository with a dummy project. To get started you need to login to the [webapp](https://app.trail-ml.com), login with your credentials and create a new project. Then you can copy the project id and the parent experiment id into the config file and you are ready to go.

If you clone the repo and experiment with different data processing steps or paramater values you'll see the visual experimentation flow in the webapp. Don't forget to change the parentID if you want to open up a new branch. The main training is shown below, for the full code, visit the [github repo](https://github.com/trail-ml/Example_projects/tree/main).
```python
    with mlflow.start_run():
        with Trail("myProjectAlias") as trail:
            trail.put_hypothesis("Baseline")
            trail.put_artifact(PROFILE_PATH, "Train Data Profile", "data")

            lr = ElasticNet(alpha=alpha, l1_ratio=l1_ratio, random_state=42)
            lr.fit(train_x, train_y)

            predicted_qualities = lr.predict(test_x)

            (rmse, mae, r2) = eval_metrics(test_y, predicted_qualities)

            print("Elasticnet model (alpha={:f}, l1_ratio={:f}):".format(alpha, l1_ratio))
            print("  RMSE: %s" % rmse)
            print("  MAE: %s" % mae)
            print("  R2: %s" % r2)

            mlflow.log_param("alpha", alpha)
            mlflow.log_param("l1_ratio", l1_ratio)
            mlflow.log_metric("rmse", rmse)
            mlflow.log_metric("r2", r2)
            mlflow.log_metric("mae", mae)

```