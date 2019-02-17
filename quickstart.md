# Quickstart

## Install

```text
npm i --save serverless-cqrs
npm i --save serverless-cqrs.memory-adapter
```

## Usage

To start, you need **Actions** and a **Reducer**. So let's write simple ones:

{% code-tabs %}
{% code-tabs-item title="actions.js" %}
```javascript
const actions = {
  addTodo: (state, payload) => {
    if (!payload.title) throw new Error('titleMissing')
    
    return [{
      type: 'TodoAdded',
      title: payload.title,
      at: Date.now(),
    }]
  }
}

module.exports = actions
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="reducer.js" %}
```javascript
const initialState = {
  todos: []
}

const reducer = (state, event) => {
  switch (event.type) {
    case 'TodoAdded':
      return {
        todos: [
          ...state.todos,
          { title: event.title },
        ]
      }
      
    default:
      return state
  }
}

module.exports = (events, state=initialState) => events.reduce(reducer, state)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Above we have a basic **action** and **reducer**. 

* The **action** ,`addTodo`, does some basic validation to check the presence of a title and if it succeeds, returns a new event with the type `TodoAdded`. 
* When that event is run through the **reducer**, a new todo is appended to the list.

Next, we build an adapter to help us persist the events.

{% code-tabs %}
{% code-tabs-item title="adapter.js" %}
```javascript
const memoryAdapterBuilder = require('serverless-cqrs.memory-adapter')
module.exports = memoryAdapterBuilder.build({ 
  entityName: 'todo'
})
```
{% endcode-tabs-item %}
{% endcode-tabs %}

This adapter will let us persist events and read-model projections in memory.

Finally, we use these to build our read and write model.

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
const {
  writeModelBuilder,
  readModelBuilder,
} = require('serverless-cqrs')

const actions = require('./actions')
const reducer = require('./reducer')
const adapter = require('./adapter')

module.exports.writeModel = writeModelBuilder.build({
  actions,
  reducer,
  adapter,
})

module.exports.readModel = readModelBuilder.build({
  reducer,
  adapter,
  eventAdapter: adapter,
})
```
{% endcode-tabs-item %}
{% endcode-tabs %}

That's it!

## Try it live

{% embed url="https://repl.it/@yonahforst/serverless-cqrs-quickstart" %}

