# Commercetools Setup

- [API Extension Setup](#APIExtensionSetup)
    - [Payment Create](#PaymentCreate)
    - [Payment Update](#PaymentUpdate)
    - [Order Create](#OrderCreate)
    - [Order Update](#OrderUpdate)
- [Resource Customisations](#ResourceCustomisations)
    - [Payment](#Payment)
    - [Order](#Order)
- [Terraform scripts](#Terraform)

The plugin requires the following commercetools customisations to be made to your project. Refer to the commercetools
documentation for how to apply these customisations, or see the Terraform section below.

# <a name="APIExtensionSetup"></a>API Extension Setup

## <a name="PaymentCreate"></a>Payment Create

Used to create a Klarna session when a payment for Klarna is initialised in commercetools

| Property                   | Value                                  | Note                                 |
| -------------------------- | -------------------------------------- | ------------------------------------ |
| key                        | kp-payment-create                      |                                      |
| type                       | HTTP                                   | Only HTTP destinations are supported |
| url                        | {baseUrl}/api/v1/payments/create              | The baseUrl will be defined by where you deploy the plugin. You should front the plugin with HTTPS in production |
| actions                    | create                                 |                                      |
| resourceTypeId             | payment                                |                                      |

## <a name="PaymentUpdate"></a>Payment Update

Used to update Klarna when payment is updated in commercetools. Updates may consist of

- update session when Klarna order has not been created yet
- capture of payment when a Charge transaction is added to the commercetools payment
- cancellation of payment when a CancelAuthorization transaction is added to the commercetools payment
- refund of payment when a Refund transaction is added to the commercetools payment

| Property                   | Value                                  | Note                                 |
| -------------------------- | -------------------------------------- | ------------------------------------ |
| key                        | kp-payment-update                      |                                      |
| type                       | HTTP                                   | Only HTTP destinations are supported |
| url                        | {baseUrl}/api/v1/payments/update       | The baseUrl will be defined by where you deploy the reference application. You should front the plugin with HTTPS in production |
| actions                    | update                                 |                                      |
| resourceTypeId             | payment                                |                                      |

## <a name="OrderCreate"></a>Order Create

Used to create order in Klarna when order is created in commercetools

| Property                   | Value                                  | Note                                 |
| -------------------------- | -------------------------------------- | ------------------------------------ |
| key                        | kp-order-create                        |                                      |
| type                       | HTTP                                   | Only HTTP destinations are supported |
| url                        | {baseUrl}/api/v1/orders/create         | The baseUrl will be defined by where you deploy the plugin. You should front the plugin with HTTPS in production |
| actions                    | create                                 |                                      |
| resourceTypeId             | order                                  |                                      |

## <a name="OrderUpdate"></a>Order Update

Used to cancel order in Klarna when order is cancelled in commercetools

| Property                   | Value                                  | Note                                 |
| -------------------------- | -------------------------------------- | ------------------------------------ |
| key                        | kp-order-update                        |                                      |
| type                       | HTTP                                   | Only HTTP destinations are supported |
| url                        | {baseUrl}/api/v1/orders/update         | The baseUrl will be defined by where you deploy the reference application. ou should front the plugin with HTTPS in production |
| actions                    | update                                 |                                      |
| resourceTypeId             | order                                  |                                      |

For production use it is recommended to set the authentication.type and authentication.headerValue properties to send a
basic auth header in the requests from commercetools. Validating these headers are present must be done before the
request is passed to the plugin.

# <a name="ResourceCustomisations"></a>Resource Customisations

## <a name="Payment"></a>Payment

| Type    | Key               | Purpose                          |
| ------- | ----------------- | -------------------------------- |
| payment | paymentKlarnaType | Klarna Custom Fields for Payment |

Fields

| Field                               | Type    | Required | Description                                                | Source         |
| ----------------------------------- | ------- |----------| ---------------------------------------------------------- | -------------- |
| klarnaAuthToken                     | String  | false    | Value of authorization_token returned from Klarna.Payments.authorize call                                      | Implementation  |
| klarnaClientToken                   | String  | true     | The Klarna client token                                    | Plugin         |
| sessionId                           | String  | true     | The Klarna session id                                      | Plugin         |
| orderId                             | String  | false    | Id of commercetools order associated with this payment.    | Implementation |

## <a name="Order"></a>Order

| Type  | Key             | Description                                                                                                                                               |
| ----- |-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| order | orderKlarnaType | Can be configured a different key with the env variable `COMMERCETOOLS_ORDER_CUSTOM_TYPE`. See [Plugin-Setup](Plugin-Setup.md) for more information. |

Fields

| Field                               | Type    | Required | Description                                                | Source |
| ----------------------------------- | ------- |----------| ---------------------------------------------------------- | ------ |
| klarnaOrderId                       | String  | true     | Id of the order created in Klarna                          | Plugin |
| extraMerchantData                   | String  | false    | String containing serialised JSON data to be sent to Klarna in the attachment.body property. See the [Klarna documentation](https://docs.klarna.com/klarna-checkout/api/checkout-api/#tag/Attachment-Schema) for more information   | Implementation |

# <a name="Terraform"></a>Terraform Scripts

The klarnact-config project contains a set of Terraform scripts you can use to apply the required customisations to your
commercetools project. To use them you will need to

* Choose one of the provided environments (dev/qa) or copy from them to make your own
* Edit the backend.tf to set where your state will be stored
* Create a vars.tfvars with the values required in variables.tf

```
cd terraform/<environment>
terraform init
terraform apply -var-file="vars.tfvars"
```
