# Examples

Here are a few examples of services you can clone and deploy to AWS using the awesome [serverless framework](https://serverless.com/framework/).

{% page-ref page="express.md" %}

{% page-ref page="graphql.md" %}

A quick heads up: you'll need to make sure you have **serverless** installed on your machine. You can installing it by running `npm i -g serverless`.

Also,  you need to have AWS credentials available. Here's a helpful guide: [AWS credentials](https://serverless.com/framework/docs/providers/aws/guide/credentials/)

Finally, almost all the AWS services used by these examples are well within the limits of their Free Tier. However, they create an AWS hosted ElasticSearch instance which is outside the Free Tier and can get expensive. Once you're done, be sure to run `serverless remove` to cleanup!



