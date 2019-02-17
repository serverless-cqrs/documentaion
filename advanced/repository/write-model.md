# Write Model

## Types

### event

An event can have an arbitrary data structure, as long as the reducer understand how to parse it.

### commit

Commits contain one or more events. Adapters must return commits in the following format:

| attribute | type | description |
| :--- | :--- | :--- |
| `id` | `string` | ID of the entity |
| `version` | `integer` | Version number of the commit |
| `entity` | `string` | Entity name |
| `commitId` | `string` | Unique ID for this commit |
| `events` | `array` | Array of event objects included in the commit |

## Methods

### listCommits

`listCommits(commitId='0')` 

Loads all commits for all entities, \(optionally\) starting from a specific `commitId`

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `commitId` | `string` | A commit ID to start from \(inclusive\). Default `'0'` |

#### Returns

`[ commits ]` - an array of commit objects.

### loadEvents

`loadEvents(id, version=0)`

Loads all events for a given entity, \(optionally\) starting from a commit `version`.

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `id` | `string` |  \(required\) the ID of the entity |
| `version` | `integer` | a commit version number to start from \(inclusive\). Defaults to `0` |

#### Returns

`[ events ]` -  a flat array of event objects.

### append

`append(id, version, events)` 

Creates a new commit and persists it to the database. Must throw if a commit with the same `id` + `version` already exists.

#### Parameters

| attribute | type | description |
| :--- | :--- | :--- |
| `id` | `string` | \(required\) the ID of the entity |
| `version` | `integer` | \(required\) the commit version number  |
| `events` | `array` | \(required\) an array of `event` objects |

#### Returns

`null` 

