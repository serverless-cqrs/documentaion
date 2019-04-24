# GraphQL

## Description

[Open in GitHub](https://github.com/serverless-cqrs/graphql-example)

{% hint style="info" %}
First, make sure you've run `npm i -g serverless` and added your [AWS credentials](https://serverless.com/framework/docs/providers/aws/guide/credentials/).
{% endhint %}

{% hint style="warning" %}
Deploying for the first time can take 10-20 minutes since it needs to create the ElasticSearch instance. If there is an error, you may get `ROLLBACK_IN_PROGRESS` the next time you try to deploy. Give it a few minutes and then try again.
{% endhint %}

```text
git clone git@github.com:serverless-cqrs/graphql-example.git
cd graphql-example
npm i
serverless deploy
```

Once deployed, navigate to the returned endpoint url and you'll see the **graphql playground**, where you can run the following queries:

```graphql
mutation {
  addTodo(id: "123", title: "Get Milk")
}
```

```graphql
query {
  getById(id: "123") {
    id
    todos {
      title
      completed
    }
  }
}
```

{% hint style="danger" %}
The AWS Free Tier covers most of these services, but running an ElasticSearch instance on AWS can be expensive. Make sure to run `serverless remove` once you're done.
{% endhint %}

