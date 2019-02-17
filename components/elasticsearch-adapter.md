# elasticsearch-adapter

A library that implements the [Read Model](https://serverless-cqrs.gitbook.io/serverless-cqrs/advanced/repository/read-model.md) Adapter interface for storing projections on AWS ElasticSearch.

## Methods

### build

`build({ entityName }, { endpoint )` 

builds a read-model adapter 

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `entityName` | `string` | the name of the entity |
| `endpoint` | `string` | the ElasticSearch instance endpoint `url` |

#### Returns

an object with [read model methods](https://serverless-cqrs.gitbook.io/serverless-cqrs/advanced/repository/read-model.md#methods)

## Example

{% code-tabs %}
{% code-tabs-item title="adapter.js" %}
```javascript
const elasticAdapterBuilder = require('serverless-cqrs.elasticsearch-adapter')
module.exports = elasticAdapterBuilder.build({ 
  entityName: 'todo'
}, {
  endpoint: 'https://xxxxxx.yyy/zzzz/1234',
})
```
{% endcode-tabs-item %}
{% endcode-tabs %}

