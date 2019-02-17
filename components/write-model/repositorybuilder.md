# repositoryBuilder

Takes a reducer and an adapter and returns a repository. A repository is the layer of our Onion Architecture \(see [Introduction](../../#event-sourcing-es)\) which handles data persistence.

The `write-model` repository has a single method called `getById`. When invoked, it uses the adapter to load all events for given ID and runs them through the reducer to calculate the current state. It returns an object that contains `state` and also a `save` method, which is used to append new events.

## Methods

### build

`build({ adapter, reducer })` 

builds a repository 

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `adapter` | `object` | Any object which implements the write-model [Adapter Interface](../../advanced/repository/). |
| `reducer` | `function` | a function which, given a list of events, knows how to calculate the current state of an object. |

#### Returns

`{ getById }` - an object with the `getById` function.

### getById

`getById(id)` 

Loads and returns the current state of an entity. Also returns a `save` function for appending new events to the data store.

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `id` | `string` | the id of the entity |

#### Returns

`{ state, save }` - an object containing an arbitrary `state` object, and a `save` function.

### save

`save(events)` 

Appends new events to the datastore

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `events` | `array` | an array of arbitrary event objects |

#### Returns

`null`

