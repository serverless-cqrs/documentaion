# Deploy

## Description

One of the great things about this library is that it can be deployed to serverless environments. It's super small footprint makes it lightning fast to load, even on cold starts.

The basic use case is simple: we simply need to forward request from some API to our read/write models. 

Let's give it a shot using Express:

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
const {
  readModel,
  writeModel,
} = require('./src')

const express = require('express')
const app = express()

app.use(express.json())

app.get('/:id', (req, res, next) => {
  readModel.getById({ 
    id: req.params.id, 
  })
  .then(obj => {
    if (!obj) throw new Error('Not Found')
    res.json(obj)
  })
  .catch(next)
})

app.post('/', (req, res, next) => {
  writeModel.addTodo(req.body.id, { 
    title: req.body.title,
  })
  .then(res.json.bind(res))
  .catch(next)
})

app.use(function(error, req, res, next) {
  console.log(error)
  res.status(500).json({ message: error.message });
});

app.listen(port, () => console.log(`Example app listening on port ${port}!`))
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Or we can use GraphQL, which works really nicely with our setup. Mutations map directly to the write model and Queries to the read model.

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
const { GraphQLServer } = require('graphql-yoga')
const {
  readModel,
  writeModel,
} = require('./src')

const typeDefs = `
  type Todo {
    title: String!
    completed: Boolean
  }

  type TodoList {
    id: ID!
    todos: [Todo]
  }
  
  type Query {
    getById(id: ID!): TodoList
  }

  type Mutation {
    addTodo(id: ID!, title: String!): Boolean
  }
`

const resolvers = {
  Mutation: {
    addTodo: (_, { id, title }) => {
      return writeModel.addTodo(id, { title })
    },
  },
  Query: {
    getById: (_, { id }) => {
      return readModel.getById({ id })
    },
  },
}

const server = new GraphQLServer({ typeDefs, resolvers })
server.start(() => console.log('Server is running on localhost:4000'))

```
{% endcode-tabs-item %}
{% endcode-tabs %}

We're really not doing very much in either of these examples, we simply forward requests from the API to our read or write model. In the case of GraphQL, it's a bit more explicit.

## Servereless Framwork

Now let's look at deploying using the awesome [serverless framework](https://serverless.com/framework/).

Following [this quickstart](https://serverless.com/framework/docs/providers/aws/guide/quick-start/), let’s set up our serverless app:

```text
$ npm install -g serverless
$ serverless create --template aws-nodejs --path my-service
$ cd my-service
```

\(you’ll also need [AWS credentials](https://serverless.com/framework/docs/providers/aws/guide/credentials/) in your local environment\)

Now replace the contents of `serverless.yml` with this:

{% code-tabs %}
{% code-tabs-item title="serverless.yml" %}
```yaml
service: my-serverless-cqrs-service

## let's define some constants to use later in this file
custom:
  writeModelTableName: commitsByEntityIdAndVersion
  writeModelIndexName: indexByEntityNameAndCommitId
  readModelDomainName: readmodel

provider:
  name: aws
  runtime: nodejs8.10
  ## make these values available in process.env
  environment:
    WRITE_MODEL_TABLENAME: ${self:custom.writeModelTableName}
    WRITE_MODEL_INDEXNAME: ${self:custom.writeModelIndexName}
    ELASTIC_READ_MODEL_ENDPOINT:
      Fn::GetAtt:
        - ReadModelProjections
        - DomainEndpoint 

  ## allow our lambda functions to access to following resources
  iamRoleStatements:
    - Effect: Allow
      Action:
        - "dynamodb:*"
      Resource: "arn:aws:dynamodb:*:*:table/${self:custom.writeModelTableName}*"
    - Effect: "Allow"
      Action:
        - "es:*"
      Resource:
        - "arn:aws:es:*:*:domain/${self:custom.readModelDomainName}/*"  
    - Effect: "Allow"
      Action:
        - "es:ESHttpGet"
      Resource:
        - "*"   

## Create an API Gateway and connect it to our handler function
functions:    
  router:
    handler: app.handler
    events:
      - http: ANY /
      - http: ANY {proxy+}

resources:
  Resources:
  
    ## create a DynamoDB table for storing events
    EventStoreTable:
      Type: 'AWS::DynamoDB::Table'
      Properties:
        TableName: ${self:custom.writeModelTableName}
        AttributeDefinitions: 
          - AttributeName: entityId
            AttributeType: S
          - AttributeName: version
            AttributeType: N
          - AttributeName: entityName
            AttributeType: S
          - AttributeName: commitId
            AttributeType: S
        KeySchema:
          - AttributeName: entityId
            KeyType: HASH
          - AttributeName: version
            KeyType: RANGE
        ProvisionedThroughput:
          ReadCapacityUnits: 5
          WriteCapacityUnits: 5
        GlobalSecondaryIndexes:
        - IndexName: ${self:custom.writeModelIndexName}
          KeySchema:
          - AttributeName: entityName
            KeyType: HASH
          - AttributeName: commitId
            KeyType: RANGE
          Projection:
            ProjectionType: ALL
          ProvisionedThroughput:
            ReadCapacityUnits: 5
            WriteCapacityUnits: 5

    ## create an ElasticSearch instance for storing read model projections
    ReadModelProjections:
        Type: 'AWS::Elasticsearch::Domain'
        Properties:
          DomainName: ${self:custom.readModelDomainName}
          ElasticsearchVersion: 6.2
          EBSOptions:
            EBSEnabled: true
            VolumeSize: 10
            VolumeType: gp2
          ElasticsearchClusterConfig:
            InstanceType: t2.small.elasticsearch
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="warning" %}
The AWS Free Tier covers most of these services, but running an ElasticSearch instance on AWS can be expensive. Make sure to run `serverless remove` once you're done.
{% endhint %}

Now let's modify the example above to have it work on AWS Lambda

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
const express = require('express')
const serverless = require('serverless-http')
// ^ add this

/// ...same contents as above

module.exports.handler = serverless(app)
// ^ add this

// app.listen(port, () => console.log(`Example app listening on port ${port}!`))
// ^ remove this


```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Examples

{% page-ref page="../examples/express.md" %}

{% page-ref page="../examples/graphql.md" %}

{% hint style="info" %}
The domain in these examples also contain the commands `completeTodo` and `removeTodo`.
{% endhint %}



