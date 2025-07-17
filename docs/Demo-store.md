# Demo store

The klarnact-demo-app project contains a ReactJS application that is an example for how you might integrate the klarnact plugin into a commercetools store. It is intended as a reference only and is not production ready.

## Setup

Follow the steps in [commercetools-Setup](commercetools-Setup.md) to set up your store

You will also need to populate the project with the [commercetools Sunrise data](https://github.com/commercetools/commercetools-sunrise-data)

Create a .env file with the following values

| Property                              | Value                                 | Notes |
| ------------------------------------- | ------------------------------------- | ----- |
| REACT\_APP\_CT\_PROJECT\_KEY          | Your commercetools project key        |       |
| REACT\_APP\_CT\_API\_URL.             | Your commercetools API URL            |       |
| REACT\_APP\_CT\_AUTH\_URL.            | Your commercetools auth URL           |       |
| REACT\_APP\_CT\_CLIENT\_ID            | Your FE commercetools API key id      | See [commercetools-Setup](commercetools-Setup.md) for required scopes |
| REACT\_APP\_CT\_CLIENT\_SECRET        | Your FE commercetools API key secret  | See [commercetools-Setup](commercetools-Setup.md) for required scopes |
| REACT\_APP\_CT\_ADMIN\_CLIENT\_ID     | API key id for admin functions        | Only required if you want to use the order admin functions. These are for demo purposes only as they require manage\_order and manage\_payment scopes. You should **never** ship a FE application with a key that has these scopes. |
| REACT\_APP\_CT\_ADMIN\_CLIENT\_SECRET | API key secret for admin functions    | Only required if you want to use the order admin functions. These are for demo purposes only as they require manage\_order and manage\_payment scopes. You should **never** ship a FE application with a key that has these scopes. |

## Run

Start the application

```npm start```

You can use yarn if you prefer

## Use

Go to [http://localhost:3000](http://localhost:3000)