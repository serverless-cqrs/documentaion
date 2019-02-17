# Testing

Because your domain is described a series of pure functions, testing becomes ultra-simple: given some input, expect a certain output. No mocking calls to external services or a database. 

### Actions

Let's look at the basic example from the Domain page:

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

Now let's write a test for this.

{% code-tabs %}
{% code-tabs-item title="\_\_test\_\_/actions.js" %}
```javascript
// stub Date.now
Date.now = () => 123

const test = require('tape')
const actions = require('../actions')

test('addTodo', assert => {
  assert.deepEquals(
    actions.addTodo({}, { title: 'foobar' }),
    [{
      type: 'TodoAdded',
      title: 'foobar',
      at: 123
    }],
    'generates a TodoAdded event'
  )
  
  assert.throws(
    () => actions.addTodo({}, { foo: 'bar' }),
    /titleMissing/,
    'throws if title is missing'
  )
})
```
{% endcode-tabs-item %}
{% endcode-tabs %}

And we can even execute it:

{% embed url="https://repl.it/@yonahforst/serverless-cqrs-testing-actions" %}

### 

### Reducer

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



{% code-tabs %}
{% code-tabs-item title="\_\_test\_\_/reducer.js" %}
```javascript
const test = require('tape')
const reducer = require('../reducer')

test('todoReducer', assert => {
  const events = [
    { type: 'TodoAdded', title: 'get milk' },
    { type: 'TodoAdded', title: 'borrow sugar' },
    { type: 'Invalid', title: 'ignore this' },
  ]
  
  const expected = {
    todos: [
      { title: 'get milk' },
      { title: 'borrow sugar' },
    ]
  }    
    
  const result = reducer(events)
  
  assert.deepEquals(result, expected, 'reduces events')
  assert.end()
})
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% embed url="https://repl.it/@yonahforst/serverless-cqrs-testing-reducer" %}

