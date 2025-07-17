# Authorize a Payment

Authorisation of a payment handled within the Klarna widget, however there are actions to be performed against commercetools to link the payment to your cart/order

## Details

1.  Prepare your cart
    
    1. Ensure your cart locale and country are set.
    2. The billing and shipping addresses should be set on the cart to be passed through to Klarna. If this is not passed Klarna will request the billing address information and assume the shipping address is the same as the billing address. The mapping of commercetools address fields to Klarna fields is as follows.
        
  | commercetools field  | Klarna field    |
  | -------------------- | --------------- | 
  | firstName            | given_name      |
  | lastName             | family_name     |
  | streetName           | street_address  |
  | additionalStreetInfo | street_address2 |
  | city                 | city            | 
  | postalCode           | postal_code     |
  | country              | country         |
  | region               | region          |
  | phone                | phone           |

2.  Create a commercetools payment (<https://docs.commercetools.com/http-api-projects-payments>) and populate the following
    
  | Property                           | Value                               | Required   | Notes                                                                         |
| ------------------------------------ | ----------------------------------- | ---------- | ----------------------------------------------------------------------------- |
  | customerId                         | Reference to commercetools customer | See notes  | Required for non-guest checkout. If using MyPayments API this will automatically be set to the logged in customer. One of customerId or anonymousId must be populated |
  | anonymousId                        | Id for tracking guest checkout      | See notes  | Required for guest checkout. If using MyPayments API this will automatically be set. One of customerId or anonymousId must be populated |
  | paymentMethodInfo.paymentInterface | Klarna                              | Yes        | This marks your payment to be processed by Klarna |
  | amountPlanned                      | Amount to authorise                 | Yes        | Should match cart gross total, unless split payments are being used           |

3. The payment object returned will contain the following additional fields

  | Property                        | Value                   |
  | ------------------------------- | ----------------------- |
  | custom.fields.klarnaClientToken | The Klarna client token  |
  | custom.fields.sessionId         | The Klarna session id    |

4.  Add the payment to the cart

5.  Load and initialise the Klarna widget (<https://docs.klarna.com/klarna-payments/api-call-descriptions/load-klarna-payments/>)

  | Property  | Value                 | Notes                                 |
  | --------- | --------------------- | ------------------------------------- |
  | type      | Authorization         |                                       |
  | state     | Initial               |                                       |
  | amount    | Amount to authorise   | Should match amountPlanned on payment |

6.  Authorise the Klarna payment (<https://docs.klarna.com/klarna-payments/api-call-descriptions/authorize-the-purchase/>). If successful, this will return the authorisation token. If you support PayNow payment methods Finalize the Authorization will also be required (<https://docs.klarna.com/klarna-payments/api-call-descriptions/authorize-the-purchase/#finalize-the-authorization>)
7.  Update the commercetools payment and set the value of custom.fields.klarnaAuthToken to the above token
8.  Create an order from the cart in commercetools
9.  Update the commercetools payment and set the value of custom.fields.orderId to the id of the order. This is used later for refund and capture
