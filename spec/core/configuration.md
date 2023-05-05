# Configuration Store

## Methods

### Get Configurations

* Method Name: `getConfiguration`
* Input Type: `GetConfigurationRequest`
* Output Type: `GetConfigurationResponse`
* Since: `1.5`

#### GetConfigurationRequest
| Name | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `storeName` | `string` | Y | | The name of configuration store. |
| `keys` | array of `string` | N | `null` | The key of the configuration item to fetch. |
| `metadata` | map of `string` to `string` | N | `null` | The metadata which can be sent to configuration store components. |

* If a list of keys is provided, only query for the corresponding configuration items. 
* Empty list of keys means fetch all configuration items for this storeName.

#### GetConfigurationResponse
| Name | Type | Description |
|-----------|------|-------------|
| `items` | map of `string` to `configurationItem` | It is the response containing the list of configuration items against respective keys. |

#### ConfigurationItem
| Name | Type | Description |
|-----------|------|-------------|
| `value` | `string` | The value of configuration item. |
| `version` | `string` | The version of configuration item. |
| `metadata` | map of `string` to `string` | The metadata which will be passed to/from configuration store component. |

#### Error Handling
| Case | Error Message | Expected result |
|-----------|------|-------------|
| Invalid Argument  | `Configuration stores not configured` | Returns error without result object | 
| Error Getting Configurations | `Failed to get from Configuration store` | Returns error without result object |

#### HTTP Considerations

None.

#### gRPC Considerations

None.

### Subscribe Configuration

* Method Name: `subscribeConfiguration`
* Input Type: `SubscribeConfigurationRequest`
* Output Type: `SubscribeConfigurationStream`
* Since: `1.5`

#### SubscribeConfigurationRequest
| Name | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `storeName` | `string` | Y | | The name of configuration store. |
| `keys` | array of `string` | N | `null` | The key of the configuration item to subscribe. |
| `metadata` | map of `string` to `string` | N | `null` | The metadata which can be sent to configuration store components. |
| `callback` | user defined func / handler | N | | Callback for every incoming event. |

* If a list of keys is provided, only subscribe for the corresponding configuration items. 
* Empty list of keys means subscribing to all configuration items for this storeName.

#### SubscribeConfigurationStream
| Name | Type | Description |
|-----------|------|-------------|
| `id` | `string` | It is the id corresponding to this subscription, can be used to stop this subscription. |
| `items` | map of `string` to `configurationItem` | It is the response containing the list of updated configuration items. Instead of asking for a handler, sdk can decide to supply back events on this stream and let user process them. |

`SubscribeConfigurationStream` can just return once and an `id` contained in it, as id can be later used by App to unsubscribe. OR `SubscribeConfigurationStream` can be a stream of items / events.

`SubscribeConfigurationStream` can also optionally provide a function `stop` to let App call it when willing to unsubscribe.

#### ConfigurationItem
| Name | Type | Description |
|-----------|------|-------------|
| `value` | `string` | The value of configuration item. |
| `version` | `string` | The version of configuration item. |
| `metadata` | map of `string` to `string` | The metadata which will be passed to/from configuration store component. |

* Basically, as a result of successful subscription, a stream of SubscribeConfigurationResponse is opened.
* If subscription is successful, the subscription id is provided in response.
* With each successful update to configuration key/value items, SubscribeConfigurationResponse is streamed across.

#### Error Handling
| Case | Error Message | Expected result |
|-----------|------|-------------|
| Invalid Argument  | `Configuration stores not configured` | Returns error without result object | 
| Error Subscribing To Configurations | `Failed to subscribe from Configuration store` | Returns error without result object |

#### HTTP Considerations

A separate handler may need to be implemented, depending on sdk implementation. This handler, if required, would be responsible to process events received at `/configuration/{configStore}/{key}`.

#### gRPC Considerations

None.

### Unsubscribe Configuration

* SDKs can chose to EITHER store subscription ids in a collection and allow users to call `Unsubscribe Configuration` OR they can chose to give a handle with `Subscribe Configuration` API, where-in the handle can be used by users to invoke unsubscribe/stop, in the later case handle internally would take care of calling `Unsubscribe Configuration` with corresponding subscription id.

* Method Name: `unsubscribeConfiguration`
* Input Type: `UnsubscribeConfigurationRequest`
* Output Type: `UnsubscribeConfigurationResponse`
* Since: `1.7`

#### UnsubscribeConfigurationRequest
| Name | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `storeName` | `string` | Y | | The name of configuration store. |
| `id` | `string` | Y | | It is the id corresponding to this subscription, can be used to stop this subscription. |

#### UnsubscribeConfigurationResponse
| Name | Type | Description |
|-----------|------|-------------|
| `ok` | `bool` | Represents whether this unsubscription was successful or not. |
| `message` | `string` | Message giving information about the reason of failure, if any. |

#### Error Handling
| Case | Error Message | Expected result |
|-----------|------|-------------|
| Invalid Argument  | `Configuration stores not configured` | Returns error without result object | 
| Error Unsubscribing To Configurations | `Failed to unsubscribe from Configuration store` | Returns error without result object |

#### HTTP Considerations

None.

#### gRPC Considerations

None.

## References

* Building Block Overview: [Configuration Store](https://docs.dapr.io/developing-applications/building-blocks/configuration/configuration-api-overview/)
* Building Block's Dapr API Reference (not the SDK API): [Configuration Store Dapr API](
https://docs.dapr.io/reference/api/configuration_api/)