# Label Inspector

Mislabeled data is a common issue in classification tasks, but we invented [Confident Learning algorithms](https://playground.cleanlab.ai/) that automatically detect label errors in your dataset.

<p align="center">
  <img src="https://raw.githubusercontent.com/cleanlab/assets/master/cleanlab/label-inspector-text.png" width=80% height=80%>
</p>

**[Label Inspector](https://aws.amazon.com/marketplace/pp/prodview-en3c45ncm5som)** runs these algorithms to estimate which examples are likely mislabeled in any classification dataset. Simply provide the data (labels + features) for a classification task in S3, and state-of-the-art ML models will be trained to score the quality of your labels and flag which ones are likely incorrect. Label Inspector can identify mislabeled examples in any standard multi-class classification dataset (including features that are: numeric, categorical, or text — with missing values allowed). It returns a CSV file with a row for each example in your dataset, stating: whether it appears mislabeled, how likely the label is correct, plus an alternative suggested label. 


<p align="center">
  <img src="https://raw.githubusercontent.com/cleanlab/assets/master/cleanlab/label-inspector-tabular.png" width=80% height=80%>
</p>

## Input

This method works for any standard multi-class classification dataset where the data are stored in a tabular format (as CSV file).  More specifically datasets where: each row corresponds to one example (i.e. datapoint), entries in the label column are discrete categories (i.e. classes), and other columns of data table contain: numeric, categorical, or text (aka arbitrary string) values. These other columns are the features to be used as predictors of the label for ML modeling, and are allowed to contain missing values. This method can work on datasets with multiple text columns (unlike other text classification solutions that only work with a single text field per example).

The first line of your CSV file should be a header with the column names for your data. The first column of your data must be the containing the class labels (remaining columns will be treated as predictive features). Ensure that the labels are categorical strings (e.g. not continuous numbers but discrete integers are ok), as only multi-class (and binary) classification datasets are supported.

Here is a [sample input](data/input/dataset.csv) that you can run Label Inspector on.

## Output

After the training job is complete, Label Inspector will output a CSV file with one row for each row in your original dataset and 4 columns: 

- `is_label_issue` contains boolean True/False values specifying whether or not this example is inferred to be mislabeled.
- `label_score` contains quality scores between 0 and 1 estimating the likelihood that each example is correctly labeled (lower scores indicate more noisy labels).
- `given_label` contains the original label for each example in your dataset (identical to the first column of your input data).
- `predicted_label` contains a label for each example predicted by our trained ML model.

Things you can do with this returned information:

- Filter the examples where `is_label_issue` is True to avoid training models / running analyses on bad data. The number of such examples reflects the overall labeling quality of your dataset.
- Sort the examples by `label_score` to prioritize them for manual review, and consider replacing some of their labels with the `predicted_label`.

Note that we do not simply flag all examples where `predicted_label` does not match `given_label` as label issues, instead only flagging the examples that our Confident Learning algorithms identify as mislabeled with higher certainty.

Here is a [sample output](data/output/sample_cleanset.csv) that is returned from Label Inspector.

## Hyperparameters

Label Inspector has one hyperparameter that can be specified during training, `runtime`. This `runtime` hyperparameter will determine the speed and accuracy of our label finding algorithm. There are two options available:

1. `fast` : this mode will have a shorter execution time, but many not produce the best quality results (maximum execution time is 2 hours, will take much less time for most datasets)
2. `high_accuracy` : this mode will take longer to execute, but will produce higher quality results (maximum execution time is 13 hours, will take much less time for most datasets)

If unspecified, Label Inspector will run on `high_accuracy` mode by default.

## Predict the labels of new data and find potential label issues(optional)

Label Inspector trains a robust ML model to detect label errors in your original dataset. After the training is complete, you can not only inspect the errors detected in your dataset but also deploy this trained model to classify new data. If your new data has accompanying labels, Label Inspector will also identify any potential label errors in the new data.

You can perform either real-time or batch inference on future data. Real-time is better if you will get datapoints one (or few) at a time and need to immediately classify them, whereas batch is better if you just want to get predictions for large test datasets cheaply and there is less urgency. Check out the [sample notebook](label_inspector.ipynb) to see how to deploy a real-time endpoint or perform batch inference.

The input data used to conduct inference on must have the same feature columns as the data used to train the model (this data is not required to have a label column).

Both the real-time and batch inferences job will a CSV with two main pieces of information:

- the first column of the output results will contain the model predicted labels for each example
- the remaining columns of the output will contain the predicted class probabilities for each example, theses columns will be named `[class_name]_probability`, where `class_name` is each unique class in your dataset (ie. the number of columns will be equivalent to the number of classes available in your training dataset)

Furthermore, if your inference data contains a label column, Label Inspector will also identify any potential label errors. In this case, the return CSV will have two additional columns:

- `is_label_issue`, a boolean value specifying whether a label is identified as an error
- `label_score`, a quality scores estimating the likelihood that each example is correctly labeled (lower scores indicate noisier labels)

Please note that the identification of label issues in your new data is done using models and statistics fitted to the training data.  If the distribution of your data is evolving and you have enough time to re-fit models, then you may better identify label errors by merging your new data with your original training dataset and feeding this into a new Label Inspector training job.


## Choosing the Instance Type

If your dataset contains text fields (strings that are not discrete categories), we recommend a p*-instance that has GPU such that large language models can be fine-tuned on your data.

If your dataset is big (over 100k rows), we recommend an instance with lots of memory: m5.24xlarge if no text, p3.16xlarge otherwise.

## Common Errors

If you are running into issues when using the label inspector, here are some common issues and potential solutions:

1. **Incorrect data format**
    
    Ensure that you pass in a CSV file containing the labels and the features for your classification task. **Make sure that the labels are located in the first column of your input file.** Please also ensure that your input data contains a header row with column names. 
    
    Ensure that the labels are categorical strings (e.g. not continuous numbers, but discrete integers are okay), as only multi-class (and binary) classification datasets are supported. 

    Check out this [sample input](data/input/dataset.csv) for correct formatting of your data.

2. **Insufficient memory**

    You can try running the label inspector on a smaller subsample of you data (however try to preserve all the classes) to verify if memory is the issue for your task. If memory is the issue, try running your task on an instance that has a larger memory allocation.


## Additional Information and Resources

This solution trains state-of-the-art ML models on your dataset which may take some time for large datasets. You can get results faster by specifying a more powerful instance type (that also has more memory, which may be required to handle larger datasets). Even though more powerful instance types cost more per hour, the overall cost may not be much greater due to the decreased runtime.  You can further decrease the runtime by setting the hyperparameter `runtime = "fast"` (see hyperparameters section above for more details).

For further questions/support, please email: support@cleanlab.ai. Your email subject line must state that you are using Label Inspector in AWS Marketplace. 

We will soon be allowing you to deploy the same ML models used to identify label issues via AWS Marketplace, as well as more robust models trained on a dataset with label issues auto-fixed. Please email us to get early access!
