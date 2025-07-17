# Plugin Setup

- [Configuration](#Configuration)
    - [Environment Properties](#EnvironmentProperties)
    - [Secret Properties](#SecretProperties)
    - [Application Configuration Properties](#ApplicationConfigurationProperties)
- [Deployment](#Deployment)

# <a name="Configuration"></a>Configuration

There are a number of configuration variables that need to be defined before running the application. These can be set
as environment variables.

For multiple environments you should use unique values per environment where possible

## <a name="EnvironmentProperties"></a>Environment Properties

| Environment variable                          | Value                                                                                   | Notes                                                                                                                                                                                                                                                                                                                                                                                                            |
|-----------------------------------------------|-----------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| COMMERCETOOLS\_PROJECT\_KEY                   | Your commercetools project key                                                          |                                                                                                                                                                                                                                                                                                                                                                                                                  |
| COMMERCETOOLS\_AUTH\_URL                      | commercetools auth endpoint                                                             | e.g. https://auth.europe-west1.gcp.commercetools.com/oauth/token                                                                                                                                                                                                                                                                                                                                                 |
| COMMERCETOOLS\_API\_URL                       | commercetools API endpoint                                                              | e.g. https://api.europe-west1.gcp.commercetools.com                                                                                                                                                                                                                                                                                                                                                              |
| KLARNA\_ZONE\_EU\_BASE\_URL                   | Base url for Klarna API calls in EU zone                                                | e.g. https://api.playground.klarna.com/                                                                                                                                                                                                                                                                                                                                                                          |
| KLARNA\_ZONE\_NA\_BASE\_URL                   | Base url for Klarna API calls in NA zone                                                | e.g. https://api-na.playground.klarna.com/                                                                                                                                                                                                                                                                                                                                                                       |
| KLARNA\_ZONE\_OC\_BASE\_URL                   | Base url for Klarna API calls in OC zone                                                | e.g. https://api-oc.playground.klarna.com/                                                                                                                                                                                                                                                                                                                                                                       |
| KLARNA\_ZONE\_MAPPING                         | Map of country code to Klarna zone for API calls (EU, NA or OC)                         | Default values are:<br/>AT --> EU<br/>AU --> OC<br/>BE --> EU<br/>CA --> NA<br/>CH --> EU<br/>DE --> EU<br/>DK --> EU<br/>ES --> EU<br/>FI --> EU<br/>GB --> EU<br/>IE --> EU<br/>IT --> EU<br/>NL --> EU<br/>NO --> EU<br/>SE --> EU<br/>US --> EU<br/><br/>Where Klarna supports a country not in the above list it can be added by setting KLARNA\_ZONE\_MAPPING\_code=zone e.g. KLARNA\_ZONE\_MAPPING\_IE=EU |
| COMMERCETOOLS_MAPPING_CUSTOM_LINE_IMAGE_FIELD | The custom field key used to get the image url when mapping custom line items to Klarna | Only string field types are supported for the referenced custom field. https://docs.commercetools.com/api/projects/carts#customlineitem https://docs.commercetools.com/api/projects/custom-fields#customfields                                                                                                                                                                                                   |
| COMMERCETOOLS_ORDER_CUSTOM_TYPE               | Defines a different key to be used as order custom type.                                | The default value is `orderKlarnaType`                                                                                                                                                                                                                                                                                                                                                                           | 

## <a name="SecretProperties"></a>Secret Properties

The following environment variables should not be checked in to version control and should be conifugured via a secrets
manager.

| Environment variable             | Value                                       | Notes                                                 |
| -------------------------------- | ------------------------------------------- | ----------------------------------------------------- |
| COMMERCETOOLS\_CLIENT\_ID        | Client id of your commercetools API key     | Created in <a href="Key-Creation.md">Key Creation</a> |
| COMMERCETOOLS\_CLIENT\_SECRET    | Client secret of your commercetools API key | Created in <a href="Key-Creation.md">Key Creation</a> |
| KLARNA\_ZONE\_EU\_AUTH\_USERNAME | Your Klarna EU MID username                 |                                                       |
| KLARNA\_ZONE\_EU\_AUTH\_PASSWORD | Your Klarna EU MID password                 |                                                       |
| KLARNA\_ZONE\_NA\_AUTH\_USERNAME | Your Klarna NA MID username                 |                                                       |
| KLARNA\_ZONE\_NA\_AUTH\_PASSWORD | Your Klarna NA MID password                 |                                                       |
| KLARNA\_ZONE\_OC\_AUTH\_USERNAME | Your Klarna OC MID username                 |                                                       |
| KLARNA\_ZONE\_OC\_AUTH\_PASSWORD | Your Klarna OC MID password                 |                                                       |
