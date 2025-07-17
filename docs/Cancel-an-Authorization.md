# Cancel an Authorization

## Cancel uncaptured order

### Prerequisites
The order must not have any capture.

### Steps

Change the order status to CANCELLED.

## Cancel remaining authorisation

### Prerequisites

To cancel the remaining authorisation, an Authorisation must have been completed. 
It is possible to cancel the remaining authorisation if any amount of the payment has not yet been captured.

### Steps

Add a CancelAuthorization transaction to the Klarna payment. This will then cancel the remaining authorised amount that has not yet been captured.
