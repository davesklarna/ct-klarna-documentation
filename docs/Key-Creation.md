# Key Creation


  - [Commercetools](#Commercetools)
      - [Backend/Plugin API Key](#Backend)
      - [Frontend/UI API Key](#Frontend)

# <a name="Commercetools"></a>Commercetools

## <a name="Backend"></a>Backend/Plugin API Key

The plugin requires an API key
which will be used throughout the payments process. The scopes required
for this API key are

| Scope                     | Reason                                           |
| ------------------------- | ------------------------------------------------ |
| view_orders               | Used to read additional cart and order data required by Klarna when processing payment and order events received from commercetools |

## <a name="Frontend"></a>Frontend/UI API Key

This is the recommended minimum set of permissions a frontend client would need

| Scope                     | Reason                                           |
| ------------------------- | ------------------------------------------------ |
| view\_published\_products | Required to add items into the cart              |
| manage\_my\_payments      | To allow payments to be created in commercetools |
| manage\_my\_orders        | Adding Payments to an Order                      |
| manage\_my\_profile       | Required for registered checkout                 |
| create\_anonymous\_token  | Required for guest checkout                      |
