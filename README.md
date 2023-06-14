# AWS Marketplace Solutions from Cleanlab

[Cleanlab](https://cleanlab.ai/) invents novel solutions to assess and improve data quality for machine learning applications with messy real-world data.
Algorithms invented by the Cleanlab team have been used to discover millions of label errors in the most famous ML datasets (https://labelerrors.com/).
We’ve published much of the research behind our novel algorithms for detecting data and label issues (https://cleanlab.ai/research/), and also taught the first-ever courses on Data-Centric AI (https://dcai.csail.mit.edu/). See why tons of companies use Cleanlab to improve their data [here](https://cleanlab.ai/love/).

This repository contains notebooks demonstrating how to use [AWS Marketplace](https://aws.amazon.com/marketplace/seller-profile?id=87d98359-d6ba-4fca-bdd6-b45ad46110dd) solutions from Cleanlab, as well as detailed documentation for each solution. 
Always use the latest version offered in the Marketplace -- these examples are designed for that version only. 

## Support

If you get stuck on AWS setup or providing credentials/ARNs, our handy [guide](GUIDE.md) may help!

For further questions, please email: [support@cleanlab.ai](mailto:support@cleanlab.ai). Your email subject line must state which AWS Marketplace solution you are using.

## Security

If you already have your data stored in AWS (e.g. S3), the data are never transferred outside of your AWS account. You can choose to save the outputs from Cleanlab in the same location or another S3 bucket in your AWS account, all without granting external access to any of these resources.

When using our [Sagemaker Machine Learning products from AWS Marketplace](https://docs.aws.amazon.com/marketplace/latest/userguide/machine-learning-products.html): "SageMaker deploys images in an environment with no access to the network or AWS service endpoints. For example, a container image can't make outbound API calls to services on the internet, VPC endpoints, or any other AWS services."

In short: if you are already using AWS, your data remain protected at the same level of security under your current AWS setup when using any of our AWS Marketplace solutions. No external organization (including Cleanlab) will be able to see any of the data nor access any of your AWS resources, unless you have explicitly configured these permissions in AWS yourself.


## Table of Contents

|   | Marketplace Listing | Description |
|---|---------------------|-------------|
| 1 | [Label Inspector](https://aws.amazon.com/marketplace/pp/prodview-rlbhc2lxttdio) | Find label errors in any classification dataset. |
| 2 | [Data Inspector](TODO:link) | Find erroneous values within any tabular dataset. |
