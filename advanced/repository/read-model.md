# Read Model

## Types

### projection

The read model's cache of an entity's state

| attribute | type | description |
| :--- | :--- | :--- |
| `id` | `string` | ID of the entity |
| `version` | `integer` | Version number of the projection |
| `state` | `object` | An arbitrary object containing the entity's state |

## Methods

### get

`get(id)`

Retrieve the stored projection for a given entity.

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `id` | `string` | \(required\) the ID of the entity |

####  Returns

`projection`

### set

`set(id, { version, state })`

Set the  projection state for a given entity.

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `id` | `string` | \(required\) the ID of the entity |
| `version` | `integer` | \(required\) the version of the projection |
| `state` | `object` | \(required\) an arbitrary object containing the entity state |

####  Returns

`null` 

### batchGet

`batchGet([ ...ids ])`

Fetch multiple projections at once

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `ids` | `array` | \(required\) the IDs of entities to fetch |

#### Returns

`[ projections ]` \(array of projections\) - the projections \(if they exist\)

### batchWrite

`batchWrite({ id: { version, state }, id: { version, state }, ... })`

Set multiple projections at once

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `id` | `string` | \(required\) the ID of the entity |
| `version` | `integer` | \(required\) the version of the projection |
| `state` | `object` | \(required\) an arbitrary object containing the entity state |

### search

`search(params)`

Search for projections

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `id` | `object` | \(required\) an arbitrary object of search params |

#### Returns

`{ total, data }`

| attribute | type | description |
| :--- | :--- | :--- |
| `total` | integer | \(required\) the total number of records found |
| `data` | `array` | \(required\) an array of projection objects |

