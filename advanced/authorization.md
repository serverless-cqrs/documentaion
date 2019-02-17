# Authorization

While **authentication** is handled by the outer API layer, **authorization** can, and indeed _should,_  be handled by our domain.

Authorization can be considered business logic, and that's what our domain is all about, business logic! Our domain describes when actions should and should not be allowed, and those decisions can depend on user attributes as well, such as `id` or `role`.

Say we have an action that creates and updates widgets. One very common bit of **authorization** is that regular users can only update their own widgets, where as admins can update all widgets.

Let's write this:

{% code-tabs %}
{% code-tabs-item title="actions.js" %}
```javascript
const actions = {
  createWidget: (state, payload) => {
    const { user } = payload
    if (!user) throw new Error('userMissing')
        
    return [{
      type: 'WidgetCreated',
      user: payload.user,
      at: Date.now(),
    }]
  },
  
  updateWidget: (state, payload) => {
    const { user } = payload
    if (!user) throw new Error('userMissing')
    if (user.role !== 'admin' && user.id !== state.userId)
      throw new Error('unauthorized')
    // ^ authorization logic
    
    return [{
      type: 'WidgetUpdated',
      user: payload.user,
      at: Date.now(),
    }]
  },  
}

module.exports = actions
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="reducer.js" %}
```javascript
const initialState = {}
​
const reducer = (state, event) => {
  const { 
    user,
    at,
  } = event
  
  switch (event.type) {
    case 'WidgetCreated':
      return {
        userId: user.id,
        updatedAt: at,
      }

    case 'WidgetUpdated':
      return {
        updatedAt: at,
      }
                  
    default:
      return state
  }
}
​
module.exports = (events, state=initialState) => events.reduce(reducer, state)
```
{% endcode-tabs-item %}
{% endcode-tabs %}



What are we doing here? 

1. Let's start with the `createWidget` action. We check to make sure the payload contains a user object and if it does, we make sure to include the user in the generated event.
2. Then in the reducer - whenever we receive a `WidgetCreated` event, we store the `userId` so we can refer back to it later.
3. Finally, whenever the `updateWidget` action is called, we check to make sure that the caller's id matches the `userId` we stored earlier \(or if caller is an admin\) and if not, we throw an `unauthorized` error.

Very simple authorization logic and it fits very neatly into our domain.

