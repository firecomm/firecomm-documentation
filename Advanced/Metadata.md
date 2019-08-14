## Class: Metadata

##### new Metadata( [options])

## 

Class for storing metadata. Keys are normalized to lowercase ASCII.

#### Parameters:
| Name | Type | Argument | Description |
| --- | --- | --- | --- |
| `options` | Object | <optional>\ | Boolean options for the beginning of the call. These options only have any effect when passed at the beginning of a client request.|

##### Properties

| Name | Type | Argument | Default | Description |
| --- | --- | --- | --- | --- |
| `idempotentRequest` | boolean | <optional>\ | false | Signal that the request is idempotent
| `waitForReady` | boolean | <optional>\ | true | Signal that the call should not return UNAVAILABLE before it has started.
| `cacheableRequest` | boolean | <optional>\ | false | Signal that the call is cacheable. GRPC is free to use GET verb.
| `corked` | boolean | <optional>\ | false | Signal that the initial metadata should be corked.

##### Example
```
var metadata = new metadata_module.Metadata();
metadata.set('key1', 'value1');
metadata.add('key1', 'value2');
metadata.get('key1') // returns ['value1', 'value2']
```

### Methods

* * * * *

#### add(key, value)

Adds the given value for the given key. Normalizes the key.

##### Parameters:
| Name | Type | Description |
| --- | --- | --- |
| `key` | String | The key to add to.|
| `value` | String/Buffer | The value to add. Must be a buffer if and only if the normalized key ends with '-bin'
* * * * *

#### clone()

Clone the metadata object.

##### Returns:

The new cloned object
***
#### get(key)

Gets a list of all values associated with the key. Normalizes the key.

##### Parameters:

| Name | Type | Description |
| --- | --- | --- |
| `key` | String | The key to get

##### Returns:

The values associated with that key

Type

Array.<(String|Buffer)>

* * * * *

#### getMap()

Get a map of each key to a single associated value. This reflects the most common way that people will want to see metadata.

##### Returns:

A key/value mapping of the metadata

Type

Object.<String, (String|Buffer)>
***
#### remove(key)

Remove the given key and any associated values. Normalizes the key.

##### Parameters:

| Name | Type | Description |
| --- | --- | --- |
| `key` | String | The key to remove |

* * * * *

#### set(key, value)

Sets the given value for the given key, replacing any other values associated with that key. Normalizes the key.

##### Parameters:

| Name | Type | Description |
| --- | --- | --- |
| `key` | String | The key to set |
| `value` | String/Buffer | The value to set. Must be a buffer if and only if the normalized key ends with '-bin' |

* * * * *

#### setOptions(options)

Set options on the metadata object

##### Parameters:

| Name | Type | Description |
| --- | --- | --- |
| `options` | Object | Boolean options for the beginning of the call. These options only have any effect when passed at the beginning of a client request.

###### Properties

| Name | Type | Argument | Default | Description |
| --- | --- | --- | --- | --- |
| `idempotentRequest` | boolean | <optional>\ |false | Signal that the request is idempotent
| `waitForReady` | boolean | <optional>\ | true | Signal that the call should not return UNAVAILABLE before it has started.
| `cacheableRequest` | boolean | <optional>\ |false |Signal that the call is cacheable. GRPC is free to use GET verb.
| `corked` | boolean | <optional>\ | false | Signal that the initial metadata should be corked.

 |