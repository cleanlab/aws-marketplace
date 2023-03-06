# Label Inspector (Tabular)

Cleanlab's [Label Inspector](TODO: AWS Marketplace product url) can help you identify any potential label errors in your tabular classification dataset. All you need to do upload the labels and features for a classification task, and we will use these inputs to score the quality of each your labels and flag some of them as being potentially incorrect.

## Input

Your data should be in a CSV file where: the first column contains the class labels, and the remaining columns contain the training features for each example. The first line of the CSV file should be a header that contains the column names for your data.

Ensure that the labels are categorial (not continuous), as only multi-class (and binary) classification datasets are supported for now. The features can contain any combination of continuous, categorical, boolean, or text values.

Here is a [sample input](data/input/payload.csv) for inference.

## Output

The output will be a CSV file with 4 columns: `is_label_issue`, `label_score`, `given_label`, `predicted_label`.

- `is_label_issue` contains boolean values specifying if the label for that example has been flagged as an error
- `label_score` contains scores between 0 and 1 for each label which estimate the quality of the label (how likely it is to be correct), where lower scores indicate that the label is likely erroneous
- `given_label` contains the original class label given for each example (this column should be identical to the first column of your input data)
- `predicted_label` contains the predicted correct label for each example (the predicted label will identical the given label if the given label is identified to be correct, but a corrected label will be suggested for examples that have been flagged as erroneous)

Here is a [sample output](data/output/sample/sample_output.csv) that is returned from the label inspector.

## Common Errors

If you are running into issues when using the label inspector, here are some common issues and potential solutions:

1. **Incorrect data format**
    
    Ensure that you pass in a CSV file containing the labels and the features for your classification task. **Make sure that the labels are located in the first column of your input file.**
    
    Please also ensure that your input data contains a header row with column names. Only multi-class (and binary) classification datasets are supported for now.

    Check out this [sample input](data/input/payload.csv) for correct formatting of your data.

2. **Insufficient memory**

    You can try running the label inspector on a smaller subsample of you data (however try to preserve all the classes) to verify if memory is the issue for your task. If memory is the issue, try running your task on an instance that has a larger memory allocation.