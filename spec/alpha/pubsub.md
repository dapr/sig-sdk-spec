# Pub/Sub

## Methods

### Publish bulk events

* Method Name: `publishBulk`
* Input Type: `PublishBulkRequest`
* Input Name: `request`
* Output Type: `PublishBulkResponse`
* Since: `1.10`

#### PublishBulkRequest

| Name | Type | Required | Default Value | Description |
|------|------|----------|---------------|-------------|
| `pubSubName` | `string` | Y | | The name of the pub/sub component |
| `topic` | `string` | Y | | The topic to publish to. |
| `messages` | array of `BulkMessage` | Y | | The messages to publish. |
| `metadata` | map of `string` to `string` | N | `null` | Metadata for the publish request. |

##### BulkMessage

| Name | Type | Required | Default Value | Description |
|------|------|----------|---------------|-------------|
| `entryID` | `string` | N | `""` | The ID of the message. |
| `event` | `object` or `string` | Y | | The event to publish. |
| `metadata` | map of `string` to `string` | N | `null` | Metadata for the bulk message. |
| `contentType` | `string` | N | `""` | The content type of the message. |

* If the `entryID` is not provided, a unique UUID should be generated.
* If the `contentType` is not provided, the content type should be inferred from the `event` type. 
    * If the `event` is a `string`, the content type should be `text/plain`.
    * If the `event` is an `object` and conforms to the cloudevent specification<sup>*</sup>, the content type should be `application/cloudevents+json`.
    * Else, the content type should be `application/json`.

<sup>*</sup> The required cloudevent fields are `id`, `source`, `specversion`, and `type`. See [CloudEvents](https://github.com/cloudevents/spec/blob/main/cloudevents/spec.md) for more information.

#### PublishBulkResponse

| Name | Type | Description |
|------|------|-------------|
| `failedMessages` | array of `BulkResponseEntry` | The messages that failed to publish. |


##### BulkResponseEntry

| Name | Type | Description |
|------|------|-------------|
| `message` | `BulkMessage` | The message that failed to publish. |
| `error` | `error` | The error that occurred when publishing the message. |


#### Error Handling

* In case of errors with the request (e.g., invalid pub/sub name, invalid topic) or network errors, all the messages should be returned in the `failedMessages` with the error.
* Otherwise, the messages that failed to publish should be returned in the `failedMessages` with their respective errors from the API response.


#### HTTP Considerations

None.

#### gRPC Considerations

None.

## References

* Building Block Overview: [Pub/Sub](https://docs.dapr.io/developing-applications/building-blocks/pubsub/pubsub-overview/)
* Building Block's Dapr API Reference (not the SDK API): [Pub/Sub API](https://docs.dapr.io/reference/api/pubsub_api/