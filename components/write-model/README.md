# write-model

The `write-model` exports two functions [`repositoryBuilder`](repositorybuilder.md) and [`commandServiceBuilder`](commandservicebuilder.md).

To initialize the model, you pass an `adapter` and `reducer` to the `repositoryBuilder` to generate a `repository`. Then you pass the `repository` and `actions` to the `commandServiceBuilder` to generate a list of commands 

## Example

{% code-tabs %}
{% code-tabs-item title="readModel.js" %}
```javascript
const {
  repositoryBuilder,
  commandServiceBuilder,
} = require('serverless-cqrs.write-mode')

const adapter = require('./adapter')
const reducer = require('./reducer')
const actions = require('./actions')

const repository = repositoryBuilder.build({
  adapter,
  reducer,
})

module.exports = commandServiceBuilder.build({
  repository,
  actions,
})
```
{% endcode-tabs-item %}
{% endcode-tabs %}





