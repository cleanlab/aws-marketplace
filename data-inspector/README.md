# Data Inspector

**Data Inspector** is a tool that uses AI to automatically identify entries in any tabular dataset (CSV file) that are likely incorrect. Simply provide any data table (including columns that are: text, numeric, or categorical — with missing values allowed), and state-of-the-art ML models will be trained to score the quality of each datapoint (row) and flag any entry (cell value) that is likely erroneous. Data Inspector returns 3 CSV files with quality assessments about your data, stating: whether an entry appears to have issues, a quality score for how likely each entry is correct, plus an alternative predicted value for each entry in the dataset.


## Input Dataset

This method works for any tabular dataset, where each row corresponds to an independent example/instance. The entries of this dataset can contain numeric, categorical, or text/arbitrary string values (missing values can also be handled by Data Inspector).

Your data should be a CSV file with a header of column names for each variable in your dataset. If your data contains an index column, it should be specified using the `index_col` hyperparameter, otherwise it is assumed that there is no index column.

By default, all categorical and numeric columns will be inspected for issues, if you want to inspect specific columns, pass those columns in as a list to the `columns_to_inspect` hyperpameter. Text columns cannot be inspected will be skipped automatically.

Here is a [sample dataset](data/input/dataset.csv) that you can run Data Inspector on.

### Optional hyperparameters

Data Inspector supports 3 hyperparameters that can be optionally specified to exert greater control during training:

- The `runtime` argument determines the speed and accuracy of our error finding algorithm, there are two options:

    1. `fast` will have a shorter execution time, but may not produce the best quality results (maximum execution time: 3 hours, will take much less time for most datasets)

    2. `high_accuracy` will be slower, but produces high quality results (maximum execution time: 15 hours, will take much less time for most datasets)

    If unspecified, Data Inspector will run on `high_accuracy` mode by default.
    
- The `index_col` argument specifies the column to use as the index of the dataset. This index column name should be passed in as a string.

- The `columns_to_inspect` argument specifies the columns that should be checked for data issues. This should be a list of column names in your dataset.


## Output

Data Inspector outputs 3 CSV files that contains the following results for each entry of the input data:

- `is_issue.csv` contains True/False values specifying whether each entry is inferred to be an issue
- `quality_scores.csv` contains scores between 0 and 1 estimating the likelihood that each entry is correct (lower scores indicate more noise)
- `imputed_values.csv` contains a value for each entry predicted by our ML models

With this returned information, you can filter the examples where `is_issue = True` to see which entries contains issues. You can then sort the datapoints by their `quality_scores` to prioritize the entries with the lower scores for manual review, and even consider replacing some of those values with the predicted `imputed_values` by our ML models. Check out the [sample notebook](data_inspector.ipynb) to see how to do this programatically.


## Choosing the Instance Type

If your dataset contains text fields (strings that are not discrete categories), we recommend a p*-instance that has GPU such that large language models can be fine-tuned on your data.

If your dataset is big (over 100k rows), we recommend an instance with lots of memory: m5.24xlarge if no text, p3.16xlarge otherwise.


## Common Errors

If you are running into issues when using the Data Inspector, here are some common issues and potential solutions:

- **Data Inspector is skipping all columns**

    Data Inspector can only inspected categorical or numeric columns. If youre dataset only contains text columns, Data Inspector will not be able to run any data quality inspections. Please try again with another dataset that contains at least one categorical or numeric column.

- **Insufficient memory**

    You can try running Data Inspector on a smaller subsample of you data (however try to preserve all the classes) to verify if memory is the issue for your task. If memory is the issue, try running your task on an instance that has a larger memory allocation.


## Additional Information and Resources

This solution trains state-of-the-art ML models on your dataset which may take some time for large datasets. You can get results faster by specifying a more powerful instance type (that also has more memory, which may be required to handle larger datasets). Even though more powerful instance types cost more per hour, the overall cost may not be much greater due to the decreased runtime.  You can further decrease the runtime by setting the hyperparameter `runtime = "fast"` (see hyperparameters section above for more details).

For further questions/support, please email: support@cleanlab.ai. Your email subject line must state that you are using Data Inspector in AWS Marketplace. 
