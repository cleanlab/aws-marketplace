# Label Inspector (Tabular)

[cleanlab](https://cleanlab.ai/) invents novel solutions to assess and improve data quality for machine learning applications with messy real-world data. Mislabeled data is a common issue in classification tasks, but we invented Confident Learning algorithms that automatically detect label errors in your dataset.

Label Inspector estimates which examples are likely mislabeled in a classification dataset. All you need to do upload the labels and features for a classification task, and we will use these inputs to  train ML models that score the quality of each your labels and flag some of them as being potentially incorrect. ML training may take some time for larger datasets.

## Input

This method works for any standard multi-class classification dataset where the data are stored in a tabular format (as CSV file).  More specifically datasets where: each row corresponds to one example (i.e. datapoint), entries in the label column are discrete categories (i.e. classes), and other columns of data table contain: numeric, categorical, or text (aka arbitrary string) values. These other columns are the features to be used as predictors of the label for ML modeling, and are allowed to contain missing values. This method can work on datasets with multiple text columns (unlike other text classification solutions that only work with a single text field per example).

The first line of your CSV file should be a header with the column names for your data. The first column of your data must be the containing the class labels (remaining columns will be treated as predictive features). Ensure that the labels are categorical strings (e.g. not continuous numbers but discrete integers are ok), as only multi-class (and binary) classification datasets are supported.

Here is a [sample input](data/input/dataset.csv) for inference.

## Output

Label Inspector will output a CSV file with one row for each row in your original dataset and 4 columns: 

- `is_label_issue` contains boolean True/False values specifying whether or not this example is inferred to be mislabeled.
- `label_score` contains quality scores between 0 and 1 estimating the likelihood that each example is correctly labeled (lower scores indicate more noisy labels).
- `given_label` contains the original label for each example in your dataset (identical to the first column of your input data).
- `predicted_label` contains a label for each example predicted by our trained ML model.

Things you can do with this returned information:

- Filter the examples where `is_label_issue` is True to avoid training models / running analyses on bad data. The number of such examples reflects the overall labeling quality of your dataset.
- Sort the examples by `label_score` to prioritize them for manual review, and consider replacing some of their labels with the `predicted_label`.

Note that we do not simply flag all examples where `predicted_label` does not match `given_label` as label issues, instead only flagging the examples that our Confident Learning algorithms identify as mislabeled with higher certainty.

Here is a [sample output](data/output/sample/sample_output.csv) that is returned from the label inspector.

## Common Errors

If you are running into issues when using the label inspector, here are some common issues and potential solutions:

1. **Incorrect data format**
    
    Ensure that you pass in a CSV file containing the labels and the features for your classification task. **Make sure that the labels are located in the first column of your input file.** Please also ensure that your input data contains a header row with column names. 
    
    Ensure that the labels are categorical strings (e.g. not continuous numbers, but discrete integers are okay), as only multi-class (and binary) classification datasets are supported. 

    Check out this [sample input](data/input/dataset.csv) for correct formatting of your data.

2. **Insufficient memory**

    You can try running the label inspector on a smaller subsample of you data (however try to preserve all the classes) to verify if memory is the issue for your task. If memory is the issue, try running your task on an instance that has a larger memory allocation.