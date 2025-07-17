# Data mapping

This document describes how data is mapped from commercetools resources to Klarna resources

## Session

When a commercetools payment for Klarna is created/updated Klarna session is created/updated using data from the commercetools cart and payment

| commercetools field                | Klarna session field | Notes           |
| ---------------------------------- | -------------------- | --------------- |
| cart.locale                        | locale               |                 |
| cart.country                       | purchase_country     |                 |
| payment.amountPlanned.currencyCode | purchase_currency    |                 |
| payment.amountPlanned.centValue    | order_amount         |                 |
| cart.taxedPrice                    | order_tax_amount     |                 |
| cart.id                            | merchant_reference2  |                 |
| cart.custom.extraMerchantData      | attachment.body      | Where available. If used this should be a string containing serialised JSON data to be sent to Klarna in the attachment.body property. See the [Klarna documentation](https://docs.klarna.com/klarna-checkout/api/checkout-api/#tag/Attachment-Schema) for more information |
| cart.lineItems                     | order_lines          | See below       |
| cart.customLineItems               | order_lines          | See below       |

Each lineItem is mapped to a Klarna older_line as follows

| commercetools cart lineItem field           | Klarna order field             | Notes                                          |
| ------------------------------------------- | ------------------------------ | ---------------------------------------------- |
| name                                        | name                           | Locale is used to look up localised value.<br/> If none is found "Item $index" will be used |
| quantity                                    | quantity                       |                                                |
| price.centAmount                            | unit_price                     | Used when taxRate = null, taxedPrice = null or taxRate.includedInPrice is true |
| taxedPrice.totalGross.centAmount / quantity | unit_price                     | Used otherwise                                 |
| totalPrice.centAmount                       | total_amount                   | Used when taxRate = null, taxedPrice = null or taxRate.includedInPrice is true |
| taxedPrice.totalGross.centAmount            | total_amount                   | Used otherwise                                 |
| See notes                                   | total_discount_amount          | Sum of apportioned cart discount and item discount where: <br/> cart discount is derived from data in DiscountedPricePerQuantity <br/> item discount is (price.value.centAmount - price.discounted.value.centAmount) * quantity |
| taxRate.amount * 10000                      | tax_rate                       | Where available, otherwise null                |
| taxedPrice.gross.centAmount - taxedPrice.net.centAmount | total_tax_amount   | Where available, otherwise null                |
| variant.images[0].url                       | image_url                      | Where available, otherwise null                |

Where there are customLineItems they are mapped to a Klarna older_line as follows

| commercetools cart lineItem field           | Klarna order field             | Notes                                          |
| ------------------------------------------- | ------------------------------ | ---------------------------------------------- |
| name                                        | name                           | Locale is used to look up localised value.<br/> If none is found "Item $index" will be used |
| quantity                                    | quantity                       |                                                |
| taxedPrice.totalPrice.centAmount / quantity | unit_price                     |                                                |
| totalPrice.centAmount                       | total_amount                   |                                                |
| taxRate.amount * 10000                      | tax_rate                       | Where available, otherwise null                |
| taxedPrice.gross.centAmount - taxedPrice.net.centAmount | total_tax_amount   | Where available, otherwise null                |
| custom.fields.\<imageUrl\>                  | image_url                      | Where available, otherwise null <br> The \<imageUrl\> field is configurable via the environment variable: COMMERCETOOLS_MAPPING_CUSTOM_LINE_IMAGE_FIELD|

When the cart.totalPrice exceeds the payment.amountPlanned (i.e. payment is split between Klarna and some other payment method(s)) another order_line is added with the difference so that in Klarna the order line amounts sum correctly. It has the following values

| Klarna order field             | Value                 |
| ------------------------------ | --------------------- |
| name                           | Other Payment(s)      |
| type                           | store_credit          |
| quantity                       | 1                     |
| unit_price                     | Difference in amounts |
| total_amount                   | Difference in amounts |

When the cart.shippingInfo.price is defined a line is added for shipping with the following values

| Klarna order field             | Value                                                      |
| ------------------------------ | ---------------------------------------------------------- |
| name                           | Shipping Charge                                            |
| type                           | shipping_fee                                               |
| quantity                       | 1                                                          |
| unit_price                     | cart.shippingInfo.price.centAmount                        |
| total_amount                   | cart.shippingInfo.price                                   |
| tax_rate                       | cart.shippingInfo.taxRate.amount * 10000 (where available) |
| total_tax_amount               | cart.shippingInfo.taxedPrice.gross.centAmount - cart.shippingInfo.taxedPrice.net.centAmount (where available) |

## Order

When an order is created in commercetools a corresponding order is created in Klarna by mapping from the commercetools order and its associated cart and payment
    
        
| commercetools field                        | Klarna order field              | Notes                                          |
| ------------------------------------------ | ------------------------------- | ---------------------------------------------- |
| order.locale                               | locale                          |                                                |
| order.country                              | purchase_country                | Used when not null                             |
| order.billingAddress.country               | purchase_country                | Used when not null and order.country is null   |
| payment.amountPlanned.currencyCode         | purchase_currency               |                                                |
| payment.amountPlanned.centValue.           | order_amount                    |                                                |
| cart.taxedPrice                            | order_tax_amount                |                                                |
| order.orderNumber                          | merchant_reference1             | Used when not null                             |
| order.id                                   | merchant_reference1             | Used when order.orderNumber is null            |
| order.billingAddress.firstName             | billingAddress.given_name       |                                                |
| order.billingAddress.lastName              | billingAddress.family_name      |                                                |
| order.billingAddress.streetName            | billingAddress.street_address   |                                                |
| order.billingAddress.additionalStreetInfo  | billingAddress.street_address2  |                                                |
| order.billingAddress.city                  | billingAddress.city             |                                                |
| order.billingAddress.postalCode            | billingAddress.postal_code      |                                                |
| order.billingAddress.region                | billingAddress.region           | Used when not null                             |
| order.billingAddress.state                 | billingAddress.region           | Used when order.shippingAddress.region is null |
| order.billingAddress.country               | billingAddress.country          |                                                |
| order.billingAddress.email                 | billingAddress.email            |                                                |
| order.billingAddress.phone                 | billingAddress.phone            |                                                |
| order.shippingAddress.firstName            | shippingAddress.given_name      |                                                |
| order.shippingAddress.lastName             | shippingAddress.family_name     |                                                |
| order.shippingAddress.streetName           | shippingAddress.street_address  |                                                |
| order.shippingAddress.additionalStreetInfo | shippingAddress.street_address2 |                                                |
| order.shippingAddress.city                 | shippingAddress.city            |                                                |
| order.shippingAddress.postalCode           | shippingAddress.postal_code     |                                                |
| order.shippingAddress.region               | shippingAddress.region          | Used when not null                             |
| order.shippingAddress.state                | shippingAddress.region          | Used when order.shippingAddress.region is null |
| order.shippingAddress.country              | shippingAddress.country         |                                                |
| order.shippingAddress.email                | shippingAddress.email           |                                                |
| order.shippingAddress.phone                | shippingAddress.phone           |                                                |
| order.custom.extraMerchantData             | attachment.body                 | Extra merchant data, if available. If used this should be a string containing serialised JSON data to be sent to Klarna in the attachment.body property. See the [Klarna documentation](https://docs.klarna.com/klarna-checkout/api/checkout-api/#tag/Attachment-Schema) for more information              |
| cart.custom.extraMerchantData              | attachment.body                 | Used if available and not set on order. If used this should be a string containing serialised JSON data to be sent to Klarna in the attachment.body property. See the [Klarna documentation](https://docs.klarna.com/klarna-checkout/api/checkout-api/#tag/Attachment-Schema) for more information         |
| cart.lineItems                             | order_lines                     | See below                                      |
| cart.customLineItems                       | order_lines                     | See below                                      |

Each item from the commercetools cart is mapped to a Klarna older_line as follows

| commercetools cart lineItem field           | Klarna order field             | Notes                                          |
| ------------------------------------------- | ------------------------------ | ---------------------------------------------- |
| name                                        | name                           | Locale is used to look up localised value.<br/> If none is found "Item $index" will be used |
| quantity                                    | quantity                       |                                                |
| price.centAmount                            | unit_price                     | Used when taxRate = null, taxedPrice = null or taxRate.includedInPrice is true |
| taxedPrice.totalGross.centAmount / quantity | unit_price                     | Used otherwise                                 |
| totalPrice.centAmount                       | total_amount                   | Used when taxRate = null, taxedPrice = null or taxRate.includedInPrice is true |
| taxedPrice.totalGross.centAmount            | total_amount                   | Used otherwise                                 |
| See notes                                   | total_discount_amount          | Sum of apportioned cart discount and item discount where: <br/> cart discount is derived from data in DiscountedPricePerQuantity <br/> item discount is (price.value.centAmount - price.discounted.value.centAmount) * quantity |
| taxRate.amount * 10000                      | tax_rate                       | Where available, otherwise null                |
| taxedPrice.gross.centAmount - taxedPrice.net.centAmount | total_tax_amount     | Where available, otherwise null                |
| variant.images[0].url                       | image_url                       | Where available, otherwise null                |

Where there are customLineItems on the commercetools cart they are mapped to a Klarna olderLine as follows

| commercetools cart lineItem field           | Klarna order field             | Notes                                          |
| ------------------------------------------- | ------------------------------ | ---------------------------------------------- |
| name                                        | name                           | Locale is used to look up localised value.<br/> If none is found "Item $index" will be used |
| quantity                                    | quantity                       |                                                |
| taxedPrice.totalPrice.centAmount / quantity | unit_price                     |                                                |
| totalPrice.centAmount                       | total_amount                   |                                                |
| taxRate.amount * 10000                      | tax_rate                       | Where available, otherwise null                |
| taxedPrice.gross.centAmount - taxedPrice.net.centAmount | total_tax_amount   | Where available, otherwise null                |
| custom.fields.\<imageUrl\>                  | image_url                      | Where available, otherwise null <br> The \<imageUrl\> field is configurable via the environment variable: COMMERCETOOLS_MAPPING_CUSTOM_LINE_IMAGE_FIELD |

When the commercetools order.totalPrice exceeds the payment.amountPlanned (i.e. payment is split between Klarna and some other payment method(s)) another orderLine is added with the difference so that in Klarna the order line amounts sum correctly. It has the following values

| Klarna order field             | Value                 |
| ------------------------------ | --------------------- |
| name                           | Other Payment(s)      |
| type                           | store_credit          |
| quantity                       | 1                     |
| unit_price                     | Difference in amounts |
| total_amount                   | Difference in amounts |

When the commercetools order.shippingInfo.price is defined a line is added for shipping with the following values

| Klarna order field             | Value                                                      |
| ------------------------------ | ---------------------------------------------------------- |
| name                           | Shipping Charge                                            |
| type                           | shipping_fee                                               |
| quantity                       | 1                                                          |
| unit_price                     | order.shippingInfo.price.centAmount                        |
| total_amount                   | order.shippingInfo.price                                   |
| tax_rate                       | cart.shippingInfo.taxRate.amount * 10000 (where available) |
| total_tax_amount               | cart.shippingInfo.taxedPrice.gross.centAmount - cart.shippingInfo.taxedPrice.net.centAmount (where available) |

## Capture

When a capture transaction is added to a commercetools payment the Klarna payment is captured using data from the commercetools order and transaction

| commercetools field                        | Klarna capture field            | Notes                                          |
| ------------------------------------------ | ------------------------------- | ---------------------------------------------- |
| transaction.id                             | reference                       |                                                |
| transaction.amount.centAmount              | captured_amount                 |                                                |
| order.billingAddress.firstName             | billingAddress.given_name       |                                                |
| order.billingAddress.lastName              | billingAddress.family_name      |                                                |
| order.billingAddress.streetName            | billingAddress.street_address   |                                                |
| order.billingAddress.additionalStreetInfo  | billingAddress.street_address2  |                                                |
| order.billingAddress.city                  | billingAddress.city             |                                                |
| order.billingAddress.postalCode            | billingAddress.postal_code      |                                                |
| order.billingAddress.region                | billingAddress.region           | Used when not null                             |
| order.billingAddress.state                 | billingAddress.region           | Used when order.shippingAddress.region is null |
| order.billingAddress.country               | billingAddress.country          |                                                |
| order.billingAddress.email                 | billingAddress.email            |                                                |
| order.billingAddress.phone                 | billingAddress.phone            |                                                |
| order.shippingAddress.firstName            | shippingAddress.given_name      |                                                |
| order.shippingAddress.lastName             | shippingAddress.family_name     |                                                |
| order.shippingAddress.streetName           | shippingAddress.street_address  |                                                |
| order.shippingAddress.additionalStreetInfo | shippingAddress.street_address2 |                                                |
| order.shippingAddress.city                 | shippingAddress.city            |                                                |
| order.shippingAddress.postalCode           | shippingAddress.postal_code     |                                                |
| order.shippingAddress.region               | shippingAddress.region          | Used when not null                             |
| order.shippingAddress.state                | shippingAddress.region          | Used when order.shippingAddress.region is null |
| order.shippingAddress.country              | shippingAddress.country         |                                                |
| order.shippingAddress.email                | shippingAddress.email           |                                                |
| order.shippingAddress.phone                | shippingAddress.phone           |                                                |
| cart.lineItems                             | order_lines                      | Only when capture amount equals order amount. See below |
| cart.customLineItems                       | order_lines                      | Only when capture amount equals order amount. See below |

Each item from the order is mapped to a Klarna older_line as follows

| commercetools cart lineItem field           | Klarna order field             | Notes                                          |
| ------------------------------------------- | ------------------------------ | ---------------------------------------------- |
| name                                        | name                           | Locale is used to look up localised value.<br/> If none is found "Item $index" will be used |
| quantity                                    | quantity                       |                                                |
| price.centAmount                            | unit_price                     | Used when taxRate = null, taxedPrice = null or taxRate.includedInPrice is true |
| taxedPrice.totalGross.centAmount / quantity | unit_price                     | Used otherwise                                 |
| totalPrice.centAmount                       | total_amount                   | Used when taxRate = null, taxedPrice = null or taxRate.includedInPrice is true |
| taxedPrice.totalGross.centAmount            | total_amount                   | Used otherwise                                 |
| See notes                                   | total_discount_amount          | Sum of apportioned cart discount and item discount where: <br/> cart discount is derived from data in DiscountedPricePerQuantity <br/> item discount is (price.value.centAmount - price.discounted.value.centAmount) * quantity |
| taxRate.amount * 10000                      | tax_rate                       | Where available, otherwise null                |
| taxedPrice.gross.centAmount - taxedPrice.net.centAmount | total_tax_amount   | Where available, otherwise null                |
| variant.images[0].url                       | image_url                      | Where available, otherwise null                |

Where there are customLineItems on the order they are mapped to a Klarna older_line as follows

| commercetools cart lineItem field           | Klarna order field             | Notes                                          |
| ------------------------------------------- | ------------------------------ | ---------------------------------------------- |
| name                                        | name                           | Locale is used to look up localised value.<br/> If none is found "Item $index" will be used |
| quantity                                    | quantity                       |                                                |
| taxedPrice.totalPrice.centAmount / quantity | unit_price                     |                                                |
| totalPrice.centAmount                       | total_amount                   |                                                |
| taxRate.amount * 10000                      | tax_rate                       | Where available, otherwise null                |
| taxedPrice.gross.centAmount - taxedPrice.net.centAmount | total_tax_amount   | Where available, otherwise null                |
| custom.fields.\<imageUrl\>                  | image_url                      | Where available, otherwise null <br> The \<imageUrl\> field is configurable via the environment variable: COMMERCETOOLS_MAPPING_CUSTOM_LINE_IMAGE_FIELD|

## Refund

When a refund transaction is added to a commercetools payment the Klarna payment is refunded using data from the commercetools order and transaction

| commercetools field                        | Klarna capture field           | Notes                                          |
| ------------------------------------------ | ------------------------------ | ---------------------------------------------- |
| transaction.id                             | reference                      |                                                |
| transaction.amount.centAmount              | captured_amount                |                                                |
| cart.lineItems                             | order_lines                    | Only when refund amount equals order amount. See below |
| cart.customLineItems                       | order_lines                    | Only when refund amount equals order amount. See below |

Each item from the order is mapped to a Klarna older_line as follows

| commercetools cart lineItem field           | Klarna order field             | Notes                                          |
| ------------------------------------------- | ------------------------------ | ---------------------------------------------- |
| name                                        | name                           | Locale is used to look up localised value.<br/> If none is found "Item $index" will be used |
| quantity                                    | quantity                       |                                                |
| price.centAmount                            | unit_price                     | Used when taxRate = null, taxedPrice = null or taxRate.includedInPrice is true |
| taxedPrice.totalGross.centAmount / quantity | unit_price                     | Used otherwise                                 |
| totalPrice.centAmount                       | total_amount                   | Used when taxRate = null, taxedPrice = null or taxRate.includedInPrice is true |
| taxedPrice.totalGross.centAmount            | total_amount                   | Used otherwise                                 |
| See notes                                   | total_discount_amount          | Sum of apportioned cart discount and item discount where: <br/> cart discount is derived from data in DiscountedPricePerQuantity <br/> item discount is (price.value.centAmount - price.discounted.value.centAmount) * quantity |
| taxRate.amount * 10000                      | tax_rate                       | Where available, otherwise null                |
| taxedPrice.gross.centAmount - taxedPrice.net.centAmount | total_tax_amount   | Where available, otherwise null                |
| variant.images[0].url                       | image_url                      | Where available, otherwise null                |

Where there are customLineItems on the order they are mapped to a Klarna older_line as follows

| commercetools cart lineItem field           | Klarna order field             | Notes                                          |
| ------------------------------------------- | ------------------------------ | ---------------------------------------------- |
| name                                        | name                           | Locale is used to look up localised value.<br/> If none is found "Item $index" will be used |
| quantity                                    | quantity                       |                                                |
| taxedPrice.totalPrice.centAmount / quantity | unit_price                     |                                                |
| totalPrice.centAmount                       | total_amount                   |                                                |
| taxRate.amount * 10000                      | tax_rate                       | Where available, otherwise null                |
| taxedPrice.gross.centAmount - taxedPrice.net.centAmount | total_tax_amount   | Where available, otherwise null                |
| custom.fields.\<imageUrl\>                  | image_url                      | Where available, otherwise null <br> The \<imageUrl\> field is configurable via the environment variable: COMMERCETOOLS_MAPPING_CUSTOM_LINE_IMAGE_FIELD|
