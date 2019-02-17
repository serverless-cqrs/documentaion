# Eventual Consistency

**Eventual Consistency** is a fact of life when dealing with distributed systems. It's not fun, but it's the nature of the beast, so to speak.

In short, when you have your data spread across multiple stores, as we do with the `read-model` and `write-model`, there needs to be some process for syncing the stores. In our case, making sure that changes made in the `write-model` are reflected in the state of the `read-model`.

As mentioned previously, there are two methods for handling this, **push** and **pull**. In the case of **push**, our `read-model` subscribes to updates from the `write-model` so anytime changes are made, they are synced in near-real-time.

However, near-real-time is not the same as real-time. For example, if we run this:

```javascript
const { addTodo } = require('./writeModel')
const { getById } = require('./readModel')

addTodo('123', { title: 'foobar' })
.then( () => getById({ id: '123' }))
```

Chances are that we'll get a `404 not found` error. This is because the `TodoAdded` event didn't have time to propagate to the `read-model`. If we wait a second and then run `getById`, it should work.

**Pull**, on the other hand, doesn't have this problem since we always check for new events before returning results. This is called **Strong Consistency**. However, this comes with the overhead of querying the event store for every single query, even though nothing may have changed.

Some distributed databases, such as DynamoDB, use a combination of both where most queries are **eventually consistent** but you can optionally specify a request to be **strongly consistent**.

Here's how you would make the example above strongly consistent:

```javascript
const { addTodo } = require('./writeModel')
const { getById, refresh } = require('./readModel')
​
addTodo('123', { title: 'foobar' })
.then(() => refresh()) // <- PULL the latest changes from the write model
.then(() => getById({ id: '123' }))
```



Let's take the example from deploy/express. Here's how we could add optional **strong consistency.**

```javascript
//...
app.get('/:id', async (req, res, next) => {
  try {
    if (req.params.strongConsistency)
      await readModel.refresh()
    
    const obj = await readModel.getById({ 
      id: req.params.id, 
    })
    
    if (!obj) throw new Error('Not Found')
    
    res.json(obj)
  } catch(e) {
    next(e)
  }
})
//...
```

or from deploy/graphql

```javascript
const typeDefs = `
  //....
  type Query {
    getById(id: ID!, strongConsistency: Boolean): TodoList
  }
  //...
`

const resolvers = {
  //....
  Query: {
    getById: async (_, { id, strongConsistency }) => {
      if (strongConsistency)
        await readModel.refresh()
        
      return await readModel.getById({ id })
    },
  },
}

```

