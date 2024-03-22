
# Domain Enumerations

This documentation outlines the enumerations and their corresponding values utilized within the Local-Retail API Specification. Enumerations such as FulfillmentType, OrderStatus, PaymentStatus, TrackingStatus are defined alongside their respective states, providing clarity on the status of delivery, orders, payment, tracking and much more within the system. Developers can refer to this document to ensure consistent interpretation and usage of these enumerations throughout their integration within the Local-Retail usecase.

Note: The below list is NOT a recommended or required standard. This list will be standardized on the basis of adoption by implementers. 

## Examples:
| Attribute Name | Description | Enumerations                                                                                            |
|-----------------------------------------|---------------------------------------|--------------------------------------------------------------------------------------------------------|
| Ack.status   | The status of the acknowledgement. If the request passes the validation criteria of the BPP, then this is set to ACK. If the request fails the validation criteria, then this is set to NACK.| ACK,NACK |
| Cancellation.cancelled_by                             | Represent the user who has cancelled the order| CONSUMER,PROVIDER|
| Descriptor.additional_desc.content_type| Different mime-type of the content in additional description | text/plain,text/html,application/json|
| Error.type                       | Type of errors a NP can send                | CONTEXT-ERROR,CORE-ERROR,DOMAIN-ERROR,POLICY-ERROR,JSON_SCHEMA-ERROR|
| Form.mime_type | This field indicates the nature and format of the form received by querying the url. MIME types are defined and standardized in IETF's RFC 6838.| text/html, application/xml|
| Fulfillment.type| A code that describes the mode of fulfillment. This is typically set when there are multiple ways an order can be fulfilled(delivered)| Home-Delivery,Store-Pickup,Store-Pickup-And-Home-Delivery|
| Image.size_type|The size of the image. The network policy can define the default dimensions of each type| xs,sm,md,lg,xl,custom|
| Order.status|Status of the order. Allowed values can be defined by the network policy| Initiated,Acknowledged,Packed,Shipped,Delivered,Cancelled|
| Order.type| This is used to indicate the type of order being created to BPPs. Sometimes orders can be linked to previous orders, like a replacement order.| Default,Pre-Order,Backorder,Replacement-Order|
| Payment.collected_by|This field indicates who is the collector of payment. The BAP can set this value to 'bap' if it wants to collect the payment first and settle it to the BPP. If the BPP agrees to those terms, the BPP should not send the payment url. Alternatively, the BPP can set this field with the value 'bpp' if it wants the payment to be made directly.|BAP,BPP|
| Payment.status| Status of the Payment transaction| PAID,NOT-PAID|
| Payment.type| This field indicates the type of the payment. BPP can set this value depending on the phase when they need the order payment to be paid by the buyer| On-Order,Pre-Fulfillment,On-Fulfillment,Post-Fulfillment|
| Quotation.breakup.breakup_category| This field indicates the category of the individual breakup object.| Item,Delivery,Packaging,Tax,Misc,Discount|
| Rating.rating_category| Category of the entity being rated| Item,Order,Provider,Fulfillment,Agent,Support|
| Region.dimentions| The number of dimensions that are used to describe any point inside that region. The most common dimensionality of a region is 2, that represents an area on a map.| 1,2,3|
| ReturnTerm.fulfillment_managed_by| The entity that will perform the return|CONSUMER,PROVIDER|
| Scalar.type| NA |CONSTANT,VARIABLE|
| Support.type| This field indicates the type of entity on which support is needed| order,billing,fulfillment|
| Tracking.status|   This value indicates if the tracking is currently active or not. If this value is  `active`, then the BAP can begin tracking the order. If this value is  `inactive`, the tracking URL is considered to be expired and the BAP should stop tracking the order. | ACTIVE,INACTIVE|

## Contributing to the Enums:
To Contribute to Enums, contributor are requested to submit a PR with updated Enums.md file to the `draft` branch.
