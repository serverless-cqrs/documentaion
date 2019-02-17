# serverless-cqrs

The `serverless-cqrs` component is really just a thin wrapper around the [read-model](read-model.md) and [write-model](write-model/) components. It simplifies their API by allowing you to build the entire service with a single command.

To install, first run:

`npm i --save serverless-cqrs`

Then in your code:

```javascript
const {
  readModelBuilder,
  writeModelBuilder,
} = require('serverless-cqrs')

const readModel = readModelBuilder.build({
  reducer,
  adapter,
  eventAdapter,
})

const writeModel = writeModelBuilder.build({
  actions,
  reducer,
  adapter,
})
```

\(bring your own adapters and friends ;\)

If you would like to have a look at [the source](https://github.com/serverless-cqrs/serverless-cqrs/tree/master/src), you'll see that it really doesn't do much on it's own, it just simplifies a bit the initialization of your models.



