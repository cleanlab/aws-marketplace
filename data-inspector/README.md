# Data Inspector

**Data Inspector** is a tool that uses AI to automatically identify entries in any tabular dataset (CSV file) that are likely incorrect. Simply provide any data table (including columns that are: text, numeric, or categorical — with missing values allowed), and state-of-the-art ML models will be trained to score the quality of each datapoint (row) and flag any entry (cell value) that is likely erroneous. Data Inspector returns 3 CSV files with quality assessments about your data, stating: whether an entry appears to have issues, a quality score for how likely each entry is correct, plus an alternative predicted value for each entry in the dataset.

The Data Inspector audit is especially useful to catch errors in applications involving: data entry, measurement errors (surveys, sensor noise, etc), or a Quality Assurance team that spends time reviewing data. AI can inspect your data more systematically to detect issues with consistent coverage -- all in a fully automated way! 


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

- The `columns_to_inspect` argument specifies a subset of columns that should be checked for data issues (other columns will not be audited). This should be a list of column names in your dataset.


## Output

After auditing your dataset, Data Inspector outputs 3 CSV files:

- `is_issue.csv` contains True/False values specifying whether each entry value in the input dataset is inferred to be erroneous (True) or not (False). You can filter based on the True values to determine which subset of data appears corrupted/anomalous in your dataset.
- `quality_scores.csv` contains scores between 0 and 1 estimating the likelihood that each entry value is correct/uncorrupted (lower scores indicate values that appear more suspicious/noisy). You can sort a column's values by these scores (in increasing order) to prioritize which values appear most suspicious.
- `imputed_values.csv` contains a value for each entry predicted by our ML models. Computed based on all of the other information in the dataset, this prediction is the imputed value that could be used if the original value were missing.

With this returned information, you can filter the examples where `is_issue = True` to see which ones are flagged with issues (i.e. which subset of data appears corrupted/anomalous in your dataset). You can sort a column's values by their `quality_scores` to prioritize the entries with the lowest scores for manual review (or to see the least-concerning / most-typical values which have the highest `quality_scores`). Finally, consider replacing some of these corrupted values with the `imputed_values` predicted by our ML models. This can improve Analytics and Machine Learning with messy real-world datasets.

Check out the [sample notebook](data_inspector.ipynb) to see how to do this programatically.


## Choosing the Instance Type

If your dataset contains text fields (strings that are not discrete categories), we recommend a p*-instance that has GPU such that large language models can be fine-tuned on your data.

If your dataset is big (over 100k rows), we recommend an instance with lots of memory: m5.24xlarge if no text fields, p3.16xlarge otherwise.


## Common Errors

When using Data Inspector, here are some common problems and potential solutions:

- **Data Inspector is skipping all columns**

    Data Inspector will only inspect columns with numeric or categorical values. If your dataset only contains text columns, Data Inspector will not run any data quality inspection. Please try another dataset containing at least one categorical or numeric column.

- **Insufficient memory**

    If Data Inspector is failing for a large dataset (many rows or many columns), try running a smaller subsample of your data to verify if memory is a problem. If memory is the problem, try running your task on an instance type that has a larger memory allocation.


## Additional Information and Resources

Data Inspector trains state-of-the-art ML models on your dataset which takes time for large datasets. You can get results faster by specifying a more powerful instance type (that also has more memory, which may be required to handle larger datasets). Even though more powerful instance types cost more per hour, the overall cost may not be much greater due to the decreased runtime.  You can further decrease the runtime by setting the hyperparameter `runtime = "fast"` (see hyperparameters section above).

For further questions/support, please email: support@cleanlab.ai. Your email subject line must state that you are using Data Inspector in AWS Marketplace. 
