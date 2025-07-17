# klarna-ct-plugin

The klarnact plugin is a Java application in a container that supports integrating [Klarna](https://www.klarna.com) payments into a [commercetools](https://commercetools.com) store. 

The plugin works by implementing commercetools API extension endpoints for payment and order events. Upon receiving these events it makes calls to the Klarna API to create and update orders there.

To use the plugin you will need to have a commercetools project and access to a Klarna merchant account. Then follow these steps

## 1. Create access keys

You will need to configure API keys in commercetools and have your Klarna MID username and password. See [Key-Creation](docs/Key-Creation.md)

## 2. Extend Commercetools

commercetools needs to be configured to support the interactions between commercetools and Klarna. See [commercetools-Setup](docs/commercetools-Setup.md)

## 3. Configure the plugin

See [Plugin-Setup](docs/Plugin-Setup.md) for the properties and secrets the plugin requires to run

## 4. Deploy the plugin

As a docker image there are many options for how you might want to deploy it. See [Deployment](docs/Deployment.md) for an example

## 5. Integrate the plugin into your store

The Klarna Javascript widget is used to display Klarna payment options in your checkout, but you will need to make sure your commercetools cart and payment is configured to allow the plugin to recognise and process Klarna payments. See [Authorize-a-Payment](docs/Authorize-a-Payment.md) for more detail.

More detail of how data is mapped from commercetools to Klarna can be found in [Data-Mapping](docs/Data-Mapping.md)

We also provide a ReactJS demonstration store if you would like a functional example. See [Demo-store](docs/Demo-store.md)

## 6. Integrate the plugin into your order management

The plugin supports cancelling, capturing and refunding Klarna payments. See [Cancel-an-Authorization](docs/Cancel-an-Authorization.md), [Capture-a-Payment](docs/Capture-a-Payment.md) and [Refund-a-Payment](docs/Refund-a-Payment.md) for details