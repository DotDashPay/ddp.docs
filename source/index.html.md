---
title: DDP API Reference

language_tabs:
- objective_c: iOS (Objective-C)
- javascript: Node.js (Javascript)

search: true
---
# Errors

### ErrorResponse

An ErrorResponse is a potential response from any request. It is
  always a completion event; i.e. no other responses will ever come
  after it.

  Every API function call allows you to specify an error callback
  which takes `ErrorResponse` as an argument. If this callback is ever
  invoked, a non-recoverable error has occurred on the
  @@ddp-hardware-module-name@@. For instance, if you call
  [Hardware.ReceivePaymentData](#receivepaymentdata), a payer swipes their
  magnetic stripe card, and the magnetic card reader fails to extract
  the payer's card information, an `ErrorResponse` will be returned so
  that you can alert the payer their card did not read and call
  [Hardware.ReceivePaymentData](#receivepaymentdata) again.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
error_code | string | No | None | a semantic code used to identify the error (see [Error      Codes](error-response-codes) for a complete list)
error_message | string | No | None | a human-readable message associated with the error


# Payment

## PreAuthorize
<a name="Payment.PreAuthorizeArgs"></a>
### Payment.PreAuthorizeArgs
```objective_c
DDPPreAuthorizeArgs* args = [[DDPPreAuthorizeArgs alloc] init];
args.paymentDataId = @"pdid-iPqy9F82v5qexbtz";
args.cents = 128;
args.currency = @"USD";

[DotDashPayAPI.payment preAuthorize:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onStartedPreAuthorizing:^(DDPStartedPreAuthorizing* response) {
    // Handle response
} onPreAuthorized:^(DDPPreAuthorized* response) {
    // Handle response
}];
```
```javascript
var args = {
    payment_data_id: "pdid-iPqy9F82v5qexbtz",
    cents: 128,
    currency: "USD",
};

dotdashpay.payment.preAuthorize(args)
    .onStartedPreAuthorizing(function (response) {
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
    })
    .onPreAuthorized(function (response) {
        var status = response.status; // status = "e:PaymentStatus.SUCCESS"
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var preauth_id = response.preauth_id; // preauth_id = "paut-Eo9YqkOTnmxt6EcL"
        var info = response.info; // info = "Human-readable description"
        var customer_id = response.customer_id; // customer_id = "2DAuJMBrMyaC"
        var merchant_account_id = response.merchant_account_id; // merchant_account_id = "merchantAccountNumber"
        var card_type = response.card_type; // card_type = "Visa"
        var last4 = response.last4; // last4 = "0000"
        var commercial = response.commercial; // commercial = false
        var debit = response.debit; // debit = false
        var created_at = response.created_at; // created_at = "915148799.75"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
        var payment_type = response.payment_type; // payment_type = "e:PaymentType.CARD"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Authorize a payment, i.e. verify that the payment_info is
          accurate and that the desired funds can be withdrawn.
          A Pre-Auth transaction places a hold on the customer’s
          account for the transaction amount for a limited
          time; depending on the cardholder’s bank, the
          reserve can be in place for 2-3 days.

          **This request does not charge a payer.** You will have to
          call [Payment.Settle](#settle) later. Alternatively, if you
          wish to void this authorization, which you should do if you
          do not plan to settle it, you should call
          [Payment.VoidPreAuthorize](#voidpreauthorize).



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_data_id | string | Yes | None | the id associated with the payment data returned from      [Hardware.ReceivePaymentData](#receivepaymentdata)
cents | uint32 | Yes | 0 | the number of cents to charge
currency | string | No | "USD" | currency of the amount to charge (cents field) (only USD currently supported)


<a name="Payment.PreAuthorize-Payment.StartedPreAuthorizing"></a>
### Payment.StartedPreAuthorizing

```objective_c
onStartedPreAuthorizing:^(DDPStartedPreAuthorizing* response) {
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
}
```
Response when the pre authorization starts via the payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_data_id | string | No | None | the payment_data_id from [Hardware.ReceivePaymentData](#receivepaymentdata)      now being used to PreAuthorize the payment
cents | uint32 | No | 0 | number of cents in the PreAuthorize
currency | string | No | "USD" | currency of the PreAuthorize (cents field)
livemode | bool | No | false | livemode is false when the request will not actually PreAuthorize funds      i.e. it will be a 'test' PreAuthorization. livemode is      true when funds will be actually PreAuthorized.


<a name="Payment.PreAuthorize-Payment.PreAuthorized"></a>
### Payment.PreAuthorized

```objective_c
onPreAuthorized:^(DDPPreAuthorized* response) {
    int32_t status = response.status;  // status = @"e:PaymentStatus.SUCCESS"
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    NSString* preauthId = response.preauthId;  // preauthId = @"paut-Eo9YqkOTnmxt6EcL"
    NSString* info = response.info;  // info = @"Human-readable description"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
    int32_t paymentType = response.paymentType;  // paymentType = @"e:PaymentType.CARD"
}
```
Response when the payment PreAuthorization returns from the payment
  processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1, UNKNOWN = 2      indicating the status of the PreAuthorize request
payment_data_id | string | No | None | the payment_data_id from [Hardware.ReceivePaymentData](#receivepaymentdata)      used to get the payment data
preauth_id | string | No | None | a unique id associated with the PreAuthorized payment, which can      be used to [Payment.Settle](#settle) or      [Payment.VoidPreAuthorize](#voidpreauthorize)
info | string | No | None | a general purpose information string, e.g. if a call to      [Payment.PreAuthorize](#preauthorize) was unsuccessful then this field is      populated with an explanation
customer_id | string | No | None | the unique customer id associated with the payment device,      e.g. the customer's credit card
merchant_account_id | string | No | None | the id of the merchant account used to process the payment
card_type | string | No | None | the type of card used (if applicable/available), e.g. "Visa"
last4 | string | No | None | the last four digits of the card used (if applicable/available)
commercial | bool | No | None | indicates whether the card is a commericial card (if      applicable/available)
debit | bool | No | None | indicates whether the card is a debit card (if      applicable/available)
created_at | string | No | None | indicates the time the PreAuthorization was created
cents | uint32 | No | 0 | number of cents PreAuthorized
currency | string | No | "USD" | currency of the transaction amount (cents field)
livemode | bool | No | false | livemode is false when the request did not actually PreAuthorize funds      with the customer's bank, i.e. it was a 'test' PreAuthorization. livemode is      true when funds were actually PreAuthorized
payment_type | PaymentType | No | CARD | payment_type specifies the type of payment object used to      PreAuthorize the payment, i.e. a credit/debit card, apple pay,      android pay, bitcoin, or a third party app/service


## Settle
<a name="Payment.SettleArgs"></a>
### Payment.SettleArgs
```objective_c
DDPSettleArgs* args = [[DDPSettleArgs alloc] init];
args.paymentDataId = @"pdid-iPqy9F82v5qexbtz";
args.preauthId = @"paut-Eo9YqkOTnmxt6EcL";
args.cents = 128;
args.currency = @"USD";

[DotDashPayAPI.payment settle:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onStartedSettling:^(DDPStartedSettling* response) {
    // Handle response
} onSettled:^(DDPSettled* response) {
    // Handle response
}];
```
```javascript
var args = {
    payment_data_id: "pdid-iPqy9F82v5qexbtz",
    preauth_id: "paut-Eo9YqkOTnmxt6EcL",
    cents: 128,
    currency: "USD",
};

dotdashpay.payment.settle(args)
    .onStartedSettling(function (response) {
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var preauth_id = response.preauth_id; // preauth_id = "paut-Eo9YqkOTnmxt6EcL"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
    })
    .onSettled(function (response) {
        var status = response.status; // status = "e:PaymentStatus.SUCCESS"
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var preauth_id = response.preauth_id; // preauth_id = "paut-Eo9YqkOTnmxt6EcL"
        var settle_id = response.settle_id; // settle_id = "txn-NG9jR86SC3hjzQHA8h5X3pwz"
        var info = response.info; // info = "Human-readable description"
        var customer_id = response.customer_id; // customer_id = "2DAuJMBrMyaC"
        var merchant_account_id = response.merchant_account_id; // merchant_account_id = "merchantAccountNumber"
        var card_type = response.card_type; // card_type = "Visa"
        var last4 = response.last4; // last4 = "0000"
        var commercial = response.commercial; // commercial = false
        var debit = response.debit; // debit = false
        var created_at = response.created_at; // created_at = "915148799.75"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
        var payment_type = response.payment_type; // payment_type = "e:PaymentType.CARD"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Settles a payment, which is equivalent to initiating a
          transfer of money from the payer's bank to your bank.

          You may call this function either after receiving data from
          calling [Hardware.ReceivePaymentData](#receivepaymentdata) or after
          receiving data from calling
          [Payment.PreAuthorize](#preauthorize).

          If called directly after receiving payment data from the
          hardware, then you must include a transaction amount and
          this request will immediately charge the payer.

          If called after authorizing the payment, then supplying the
          transaction amount is options (it defaults to the amount
          specified in [Payment.PreAuthorize](#preauthorize)); a provided
          transaction amount must be less than or equal to the amount
          in the PreAuthorization. If Settle is called
          after [Payment.PreAuthorize](#preauthorize), then this request will
          finalize the transaction and the transaction cannot be
          voided later on; it must be refunded.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
cents | uint32 | Yes | 0 | the settle amount, i.e. number of cents to settle for.  If      directly calling this request after      [Hardware.ReceivePaymentData](#receivepaymentdata), then this field is      required and can be for any amount. If calling this request      after [Payment.PreAuthorize](#preauthorize), then this field is      optional, as it defaults to the PreAuthorized amount, and if      included, must be less than or equal to the PreAuthorized      amount.
payment_data_id | string | No | None | the id from [Hardware.ReceivePaymentData](#receivepaymentdata) if you are      settling a payment directly from data read via a payment      peripheral
preauth_id | string | No | None | the id from [Payment.PreAuthorize](#preauthorize) if you are settling a      payment from a pre-authorization
currency | string | No | "USD" | currency of the amount to settle (the cents field) (only USD      currently supported)


<a name="Payment.Settle-Payment.StartedSettling"></a>
### Payment.StartedSettling

```objective_c
onStartedSettling:^(DDPStartedSettling* response) {
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    NSString* preauthId = response.preauthId;  // preauthId = @"paut-Eo9YqkOTnmxt6EcL"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
}
```
Response when the settle payment initially starts settling the
  payment with the processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_data_id | string | No | None | the id from [Hardware.ReceivePaymentData](#receivepaymentdata) if you are      settling a payment directly from data read via a payment      peripheral
preauth_id | string | No | None | the id from [Payment.PreAuthorize](#preauthorize) if you are settling a      payment from a pre-authorization
cents | uint32 | No | 0 | number of cents in the settlement
currency | string | No | "USD" | currency of the settlement (cents field)
livemode | bool | No | false | livemode is false when the request will not actually transfer funds      between accounts (settle the payment), i.e. it will be      a 'test' settlement. livemode is      true when funds will be actually settled.


<a name="Payment.Settle-Payment.Settled"></a>
### Payment.Settled

```objective_c
onSettled:^(DDPSettled* response) {
    int32_t status = response.status;  // status = @"e:PaymentStatus.SUCCESS"
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    NSString* preauthId = response.preauthId;  // preauthId = @"paut-Eo9YqkOTnmxt6EcL"
    NSString* settleId = response.settleId;  // settleId = @"txn-NG9jR86SC3hjzQHA8h5X3pwz"
    NSString* info = response.info;  // info = @"Human-readable description"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
    int32_t paymentType = response.paymentType;  // paymentType = @"e:PaymentType.CARD"
}
```
Response when the [Payment.Settle](#settle) payment request returns
  from the payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1, UNKNOWN = 2      indication the status of the Settle
payment_data_id | string | No | None | the id from [Hardware.ReceivePaymentData](#receivepaymentdata) if you are      settling a payment directly from data read via a payment      peripheral
preauth_id | string | No | None | the id from [Payment.PreAuthorize](#preauthorize) if you are settling a      payment from a pre-authorization
settle_id | string | No | None | the unique id of the settled payment (you can use this id to      [Payment.Refund](#refund) the settled payment later)
info | string | No | None | a general purpose information string, e.g. if a call to      [Payment.Settle](#settle) was unsuccessful, this field is      populated with an explanation for why it was unsuccessful,
customer_id | string | No | None | the unique customer id associated with the payment device,      e.g. the customer's credit card
merchant_account_id | string | No | None | the id of the merchant account used to settle the payment
card_type | string | No | None | the type of card used (if applicable/available), e.g. "Visa"
last4 | string | No | None | the last four digits of the card used (if applicable/available)
commercial | bool | No | None | indicates whether the card is a commericial card (if      applicable/available)
debit | bool | No | None | indicates whether the card is a debit card (if      applicable/available)
created_at | string | No | None | indicates the time the settlement was created
cents | uint32 | No | 0 | number of cents in the settlement
currency | string | No | "USD" | currency of the settlement (cents field)
livemode | bool | No | false | livemode is false when the request did not actually transfer funds      between accounts (settle the payment), i.e. it was a 'test' settlement. livemode is      true when funds were really settled.
payment_type | PaymentType | No | CARD | payment_type specifies the type of payment object used to      complete the payment, i.e. a credit/debit card, apple pay,      android pay, bitcoin, or a third party app or service


## VoidPreAuthorize
<a name="Payment.VoidPreAuthorizeArgs"></a>
### Payment.VoidPreAuthorizeArgs
```objective_c
DDPVoidPreAuthorizeArgs* args = [[DDPVoidPreAuthorizeArgs alloc] init];
args.preauthId = @"paut-Eo9YqkOTnmxt6EcL";

[DotDashPayAPI.payment voidPreAuthorize:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onVoidedPreAuthorize:^(DDPVoidedPreAuthorize* response) {
    // Handle response
}];
```
```javascript
var args = {
    preauth_id: "paut-Eo9YqkOTnmxt6EcL",
};

dotdashpay.payment.voidPreAuthorize(args)
    .onVoidedPreAuthorize(function (response) {
        var status = response.status; // status = "e:PaymentStatus.SUCCESS"
        var void_id = response.void_id; // void_id = "void-QTKL0uLZuO1gFaFm"
        var info = response.info; // info = "Human-readable description"
        var customer_id = response.customer_id; // customer_id = "2DAuJMBrMyaC"
        var merchant_account_id = response.merchant_account_id; // merchant_account_id = "merchantAccountNumber"
        var card_type = response.card_type; // card_type = "Visa"
        var last4 = response.last4; // last4 = "0000"
        var commercial = response.commercial; // commercial = false
        var debit = response.debit; // debit = false
        var created_at = response.created_at; // created_at = "915148799.75"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
        var payment_type = response.payment_type; // payment_type = "e:PaymentType.CARD"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Void an authorized payment. You can only call this function
          for a payment that was successfully authorized via
          [Payment.PreAuthorize](#preauthorize) but has not yet been
          settled.

          You should not use this function for if the payment was
          already settled. If it was settled, you should call
          [Payment.Refund](#refund) instead.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
preauth_id | string | Yes | None | the id returned from the payment processor after      [Payment.PreAuthorize](#preauthorize)


<a name="Payment.VoidPreAuthorize-Payment.VoidedPreAuthorize"></a>
### Payment.VoidedPreAuthorize

```objective_c
onVoidedPreAuthorize:^(DDPVoidedPreAuthorize* response) {
    int32_t status = response.status;  // status = @"e:PaymentStatus.SUCCESS"
    NSString* voidId = response.voidId;  // voidId = @"void-QTKL0uLZuO1gFaFm"
    NSString* info = response.info;  // info = @"Human-readable description"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
    int32_t paymentType = response.paymentType;  // paymentType = @"e:PaymentType.CARD"
}
```
Response when a void preauthorization response is returned from the
  payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1, UNKNOWN = 2      indicating the status of the void request
void_id | string | Yes | None | a unique id that can be used to identify the void
info | string | No | None | info is an optional string given by the payment processor that      provides additional information about the void
customer_id | string | No | None | the unique customer id associated with the payment device,      e.g. the customer's credit card
merchant_account_id | string | No | None | the id of the merchant account used to Void the PreAuthorization
card_type | string | No | None | the type of card used in the PreAuthorize (if applicable/available), e.g. "Visa"
last4 | string | No | None | the last four digits of the card used (if applicable/available)
commercial | bool | No | None | indicates whether the PreAuthorize card is a commericial card (if      applicable/available)
debit | bool | No | None | indicates whether the PreAuthorize card is a debit card (if      applicable/available)
created_at | string | No | None | indicates the time the Void PreAuthorize was created
cents | uint32 | No | 0 | number of cents voided in the PreAuthorize
currency | string | No | "USD" | currency of the PreAuthorize amount (cents field)
livemode | bool | No | false | livemode is false when the void did not void a real PreAuthorize      i.e. it was a 'test' void. livemode is      true when a PreAuthorize was actually voided.
payment_type | PaymentType | No | CARD | payment_type specifies the type of payment object used to      PreAuthorize the payment, i.e. a credit/debit card, apple pay,      android pay, bitcoin, or a third party app/service


## Refund
<a name="Payment.RefundArgs"></a>
### Payment.RefundArgs
```objective_c
DDPRefundArgs* args = [[DDPRefundArgs alloc] init];
args.settleId = @"txn-NG9jR86SC3hjzQHA8h5X3pwz";

[DotDashPayAPI.payment refund:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRefunded:^(DDPRefunded* response) {
    // Handle response
}];
```
```javascript
var args = {
    settle_id: "txn-NG9jR86SC3hjzQHA8h5X3pwz",
};

dotdashpay.payment.refund(args)
    .onRefunded(function (response) {
        var status = response.status; // status = "e:PaymentStatus.SUCCESS"
        var refund_id = response.refund_id; // refund_id = "rfnd-PFuJxu7hpWKcMsgs"
        var info = response.info; // info = "Human-readable description"
        var customer_id = response.customer_id; // customer_id = "2DAuJMBrMyaC"
        var merchant_account_id = response.merchant_account_id; // merchant_account_id = "merchantAccountNumber"
        var card_type = response.card_type; // card_type = "Visa"
        var last4 = response.last4; // last4 = "0000"
        var commercial = response.commercial; // commercial = false
        var debit = response.debit; // debit = false
        var created_at = response.created_at; // created_at = "915148799.75"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
        var payment_type = response.payment_type; // payment_type = "e:PaymentType.CARD"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Refund a settled payment. You can only call this function
          for a payment that was successfully settled.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
settle_id | string | Yes | None | the id returned from the payment processor after    * [Payment.Settle](#settle)


<a name="Payment.Refund-Payment.Refunded"></a>
### Payment.Refunded

```objective_c
onRefunded:^(DDPRefunded* response) {
    int32_t status = response.status;  // status = @"e:PaymentStatus.SUCCESS"
    NSString* refundId = response.refundId;  // refundId = @"rfnd-PFuJxu7hpWKcMsgs"
    NSString* info = response.info;  // info = @"Human-readable description"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
    int32_t paymentType = response.paymentType;  // paymentType = @"e:PaymentType.CARD"
}
```
Response when the payment refund returns from the payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1 indicating the status      of the refund
refund_id | string | Yes | None | a unique id that can be used to identify the refund
info | string | No | None | info is an optional string given by the payment processor that      provides additional information about the void
customer_id | string | No | None | the unique customer id associated with the payment device,      e.g. the customer's credit card
merchant_account_id | string | No | None | the id of the merchant account used to refund the payment
card_type | string | No | None | the type of card used in the associated settle (if applicable/available),      e.g. "Visa"
last4 | string | No | None | the last four digits of the card used (if applicable/available)
commercial | bool | No | None | indicates whether the card used in the associated Settle      is a commericial card (if applicable/available)
debit | bool | No | None | indicates whether the card used in the associated Settle is a      debit card (if applicable/available)
created_at | string | No | None | indicates the time the Refund was created
cents | uint32 | No | 0 | number of cents in the refunded from the associated Settle
currency | string | No | "USD" | currency of the Settle amount (cents field)
livemode | bool | No | false | livemode is false when the void did not refund a transaction      i.e. it was a 'test' refund. livemode is      true when a transaction was actually refunded.
payment_type | PaymentType | No | CARD | payment_type specifies the type of payment object used to      Settle the payment, i.e. a credit/debit card, apple pay,      android pay, bitcoin, or a third party app/service


## ConfigureProcessor
<a name="Payment.ConfigureProcessorArgs"></a>
### Payment.ConfigureProcessorArgs
```objective_c
DDPConfigureProcessorArgs* args = [[DDPConfigureProcessorArgs alloc] init];
args.livemode = NO;

[DotDashPayAPI.payment configureProcessor:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfiguredProcessor:^(DDPConfiguredProcessor* response) {
    // Handle response
}];
```
```javascript
var args = {
    livemode: false,
};

dotdashpay.payment.configureProcessor(args)
    .onConfiguredProcessor(function (response) {
        var livemode = response.livemode; // livemode = false
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```




Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
livemode | bool | No | false | livemode specifies whether to process payments in live mode with      the payment processor, i.e. livemode=false will cause the payment      to hit the processor but the processor will not acually transfer funds.


<a name="Payment.ConfigureProcessor-Payment.ConfiguredProcessor"></a>
### Payment.ConfiguredProcessor

```objective_c
onConfiguredProcessor:^(DDPConfiguredProcessor* response) {
    BOOL livemode = response.livemode;  // livemode = NO
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
livemode | bool | No | false | livemode specifies whether to process payments in live mode with      the payment processor, i.e. livemode=false will cause the payment      to hit the processor but the processor will not acually transfer funds.


## ReceivePaymentDataThenSettle
<a name="Payment.ReceivePaymentDataThenSettleArgs"></a>
### Payment.ReceivePaymentDataThenSettleArgs
```objective_c
DDPReceivePaymentDataThenSettleArgs* args = [[DDPReceivePaymentDataThenSettleArgs alloc] init];
args.cents = 128;
args.currency = @"USD";
args.useExistingData = NO;
args.useExistingDataSecondsWindow = 5;

[DotDashPayAPI.payment receivePaymentDataThenSettle:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onStartedReceivingPaymentData:^(DDPStartedReceivingPaymentData* response) {
    // Handle response
} onReceivedPaymentData:^(DDPReceivedPaymentData* response) {
    // Handle response
} onStartedSettling:^(DDPStartedSettling* response) {
    // Handle response
} onSettled:^(DDPSettled* response) {
    // Handle response
}];
```
```javascript
var args = {
    cents: 128,
    currency: "USD",
    use_existing_data: false,
    use_existing_data_seconds_window: 5,
};

dotdashpay.payment.receivePaymentDataThenSettle(args)
    .onStartedReceivingPaymentData(function (response) {
        var peripheral_name = response.peripheral_name; // peripheral_name = "MagTekSureSwipe21040145"
    })
    .onReceivedPaymentData(function (response) {
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var token = response.token; // token = "aeac1bc8f0735e4283305652ab"
        var peripheral_name = response.peripheral_name; // peripheral_name = "MagTekSureSwipe21040145"
    })
    .onStartedSettling(function (response) {
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var preauth_id = response.preauth_id; // preauth_id = "paut-Eo9YqkOTnmxt6EcL"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
    })
    .onSettled(function (response) {
        var status = response.status; // status = "e:PaymentStatus.SUCCESS"
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var preauth_id = response.preauth_id; // preauth_id = "paut-Eo9YqkOTnmxt6EcL"
        var settle_id = response.settle_id; // settle_id = "txn-NG9jR86SC3hjzQHA8h5X3pwz"
        var info = response.info; // info = "Human-readable description"
        var customer_id = response.customer_id; // customer_id = "2DAuJMBrMyaC"
        var merchant_account_id = response.merchant_account_id; // merchant_account_id = "merchantAccountNumber"
        var card_type = response.card_type; // card_type = "Visa"
        var last4 = response.last4; // last4 = "0000"
        var commercial = response.commercial; // commercial = false
        var debit = response.debit; // debit = false
        var created_at = response.created_at; // created_at = "915148799.75"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
        var payment_type = response.payment_type; // payment_type = "e:PaymentType.CARD"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
This is a convenience request that combines
          [Hardware.ReceivePaymentData](#receivepaymentdata) and
          [Payment.Settle](#settle) into a single request.

          Once a payer interacts with a payment peripheral, they are
          immediately charged for the amount specified in this
          request. Generally, this is quicker than calling
          [Hardware.ReceivePaymentData](#receivepaymentdata) followed by
          [Payment.Settle](#settle) yourself.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
cents | uint32 | Yes | 0 | number of cents to settle in the transaction
currency | string | No | "USD" | currency of the settle amount (the cents field) (only USD      currently supported)
use_existing_data | bool | No | false | use_existing_data is true when we want to use data that has      recently gone through payment peripherals, e.g. a card swipe that      occured shortly before this request, else if use_existing_data is      false, it will only return new data through the hardware      peripherals.
use_existing_data_seconds_window | uint32 | No | 20 | use_existing_data_seconds_window is used when      use_existing_data==true to determine how recently we should use      existing data going through the payments peripherals.


<a name="Payment.ReceivePaymentDataThenSettle-Hardware.StartedReceivingPaymentData"></a>
### Hardware.StartedReceivingPaymentData

```objective_c
onStartedReceivingPaymentData:^(DDPStartedReceivingPaymentData* response) {
    NSString* paymentHardware = response.paymentHardware;  // paymentHardware = @example-value(payment_hardware)
}
```
Response when any payment peripheral begins receiving payment data
  (e.g. when a magnetic-stripe reader starts reading data from a
  magnetic stripe card).

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
peripheral_name | string | No | None | denotes which payment hardware started to receive data. The      possible values are the same as those returned by      [Hardware.GetConnectedPeripherals](#getconnectedperipherals).


<a name="Payment.ReceivePaymentDataThenSettle-Hardware.ReceivedPaymentData"></a>
### Hardware.ReceivedPaymentData

```objective_c
onReceivedPaymentData:^(DDPReceivedPaymentData* response) {
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    NSString* token = response.token;  // token = @"aeac1bc8f0735e4283305652ab"
    NSString* paymentHardware = response.paymentHardware;  // paymentHardware = @example-value(payment_hardware)
}
```
Response when any payment peripheral finishes reading payment data from
  a magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_data_id | string | Yes | None | a unique per-transaction id that represents the payment data that      was received. Note that this changes for each transaction even if      the payment method is the exact same. It can be used in other API      requests to easily refer to payment data in a secure way      (e.g. [Payment.Settle](#settle))
token | string | No | None | a unique and secure token that can be used to identify the      payment method that was used. E.g. for magnetic stripe cards,      this can be used as a proxy representation of a credit card      PAN.
peripheral_name | string | No | None | denotes which payment hardware received data. The possible values      are the same as those returned by      [Hardware.GetConnectedPeripherals](#getconnectedperipherals).


<a name="Payment.ReceivePaymentDataThenSettle-Payment.StartedSettling"></a>
### Payment.StartedSettling

```objective_c
onStartedSettling:^(DDPStartedSettling* response) {
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    NSString* preauthId = response.preauthId;  // preauthId = @"paut-Eo9YqkOTnmxt6EcL"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
}
```
Response when the settle payment initially starts settling the
  payment with the processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_data_id | string | No | None | the id from [Hardware.ReceivePaymentData](#receivepaymentdata) if you are      settling a payment directly from data read via a payment      peripheral
preauth_id | string | No | None | the id from [Payment.PreAuthorize](#preauthorize) if you are settling a      payment from a pre-authorization
cents | uint32 | No | 0 | number of cents in the settlement
currency | string | No | "USD" | currency of the settlement (cents field)
livemode | bool | No | false | livemode is false when the request will not actually transfer funds      between accounts (settle the payment), i.e. it will be      a 'test' settlement. livemode is      true when funds will be actually settled.


<a name="Payment.ReceivePaymentDataThenSettle-Payment.Settled"></a>
### Payment.Settled

```objective_c
onSettled:^(DDPSettled* response) {
    int32_t status = response.status;  // status = @"e:PaymentStatus.SUCCESS"
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    NSString* preauthId = response.preauthId;  // preauthId = @"paut-Eo9YqkOTnmxt6EcL"
    NSString* settleId = response.settleId;  // settleId = @"txn-NG9jR86SC3hjzQHA8h5X3pwz"
    NSString* info = response.info;  // info = @"Human-readable description"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
    int32_t paymentType = response.paymentType;  // paymentType = @"e:PaymentType.CARD"
}
```
Response when the [Payment.Settle](#settle) payment request returns
  from the payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1, UNKNOWN = 2      indication the status of the Settle
payment_data_id | string | No | None | the id from [Hardware.ReceivePaymentData](#receivepaymentdata) if you are      settling a payment directly from data read via a payment      peripheral
preauth_id | string | No | None | the id from [Payment.PreAuthorize](#preauthorize) if you are settling a      payment from a pre-authorization
settle_id | string | No | None | the unique id of the settled payment (you can use this id to      [Payment.Refund](#refund) the settled payment later)
info | string | No | None | a general purpose information string, e.g. if a call to      [Payment.Settle](#settle) was unsuccessful, this field is      populated with an explanation for why it was unsuccessful,
customer_id | string | No | None | the unique customer id associated with the payment device,      e.g. the customer's credit card
merchant_account_id | string | No | None | the id of the merchant account used to settle the payment
card_type | string | No | None | the type of card used (if applicable/available), e.g. "Visa"
last4 | string | No | None | the last four digits of the card used (if applicable/available)
commercial | bool | No | None | indicates whether the card is a commericial card (if      applicable/available)
debit | bool | No | None | indicates whether the card is a debit card (if      applicable/available)
created_at | string | No | None | indicates the time the settlement was created
cents | uint32 | No | 0 | number of cents in the settlement
currency | string | No | "USD" | currency of the settlement (cents field)
livemode | bool | No | false | livemode is false when the request did not actually transfer funds      between accounts (settle the payment), i.e. it was a 'test' settlement. livemode is      true when funds were really settled.
payment_type | PaymentType | No | CARD | payment_type specifies the type of payment object used to      complete the payment, i.e. a credit/debit card, apple pay,      android pay, bitcoin, or a third party app or service


## ReceivePaymentDataThenPreAuthorize
<a name="Payment.ReceivePaymentDataThenPreAuthorizeArgs"></a>
### Payment.ReceivePaymentDataThenPreAuthorizeArgs
```objective_c
DDPReceivePaymentDataThenPreAuthorizeArgs* args = [[DDPReceivePaymentDataThenPreAuthorizeArgs alloc] init];
args.cents = 128;
args.currency = @"USD";
args.useExistingData = NO;
args.useExistingDataSecondsWindow = 5;

[DotDashPayAPI.payment receivePaymentDataThenPreAuthorize:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onStartedReceivingPaymentData:^(DDPStartedReceivingPaymentData* response) {
    // Handle response
} onReceivedPaymentData:^(DDPReceivedPaymentData* response) {
    // Handle response
} onStartedPreAuthorizing:^(DDPStartedPreAuthorizing* response) {
    // Handle response
} onPreAuthorized:^(DDPPreAuthorized* response) {
    // Handle response
}];
```
```javascript
var args = {
    cents: 128,
    currency: "USD",
    use_existing_data: false,
    use_existing_data_seconds_window: 5,
};

dotdashpay.payment.receivePaymentDataThenPreAuthorize(args)
    .onStartedReceivingPaymentData(function (response) {
        var peripheral_name = response.peripheral_name; // peripheral_name = "MagTekSureSwipe21040145"
    })
    .onReceivedPaymentData(function (response) {
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var token = response.token; // token = "aeac1bc8f0735e4283305652ab"
        var peripheral_name = response.peripheral_name; // peripheral_name = "MagTekSureSwipe21040145"
    })
    .onStartedPreAuthorizing(function (response) {
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
    })
    .onPreAuthorized(function (response) {
        var status = response.status; // status = "e:PaymentStatus.SUCCESS"
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var preauth_id = response.preauth_id; // preauth_id = "paut-Eo9YqkOTnmxt6EcL"
        var info = response.info; // info = "Human-readable description"
        var customer_id = response.customer_id; // customer_id = "2DAuJMBrMyaC"
        var merchant_account_id = response.merchant_account_id; // merchant_account_id = "merchantAccountNumber"
        var card_type = response.card_type; // card_type = "Visa"
        var last4 = response.last4; // last4 = "0000"
        var commercial = response.commercial; // commercial = false
        var debit = response.debit; // debit = false
        var created_at = response.created_at; // created_at = "915148799.75"
        var cents = response.cents; // cents = 128
        var currency = response.currency; // currency = "USD"
        var livemode = response.livemode; // livemode = false
        var payment_type = response.payment_type; // payment_type = "e:PaymentType.CARD"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
This is a convenience request that combines
          [Hardware.ReceivePaymentData](#receivepaymentdata) and
          [Payment.PreAuthorize](#preauthorize) into a single request.

          Once a payer interacts with a payment peripheral, they are
          immediately PreAuthorized for the amount specified in this
          request. Generally, this is quicker than calling
          [Hardware.ReceivePaymentData](#receivepaymentdata) followed by
          [Payment.PreAuthorize](#preauthorize) yourself.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
cents | uint32 | Yes | 0 | number of cents to PreAuthorize in the transaction
currency | string | No | "USD" | currency of the PreAuthorize amount (the cents field) (only USD      currently supported)
use_existing_data | bool | No | false | use_existing_data is true when we want to use data that has      recently gone through payment peripherals, e.g. a card swipe that      occured shortly before this request, else if use_existing_data is      false, it will only return new data through the hardware      peripherals.
use_existing_data_seconds_window | uint32 | No | 20 | use_existing_data_seconds_window is used when      use_existing_data==true to determine how recently we should use      existing data going through the payments peripherals.


<a name="Payment.ReceivePaymentDataThenPreAuthorize-Hardware.StartedReceivingPaymentData"></a>
### Hardware.StartedReceivingPaymentData

```objective_c
onStartedReceivingPaymentData:^(DDPStartedReceivingPaymentData* response) {
    NSString* paymentHardware = response.paymentHardware;  // paymentHardware = @example-value(payment_hardware)
}
```
Response when any payment peripheral begins receiving payment data
  (e.g. when a magnetic-stripe reader starts reading data from a
  magnetic stripe card).

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
peripheral_name | string | No | None | denotes which payment hardware started to receive data. The      possible values are the same as those returned by      [Hardware.GetConnectedPeripherals](#getconnectedperipherals).


<a name="Payment.ReceivePaymentDataThenPreAuthorize-Hardware.ReceivedPaymentData"></a>
### Hardware.ReceivedPaymentData

```objective_c
onReceivedPaymentData:^(DDPReceivedPaymentData* response) {
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    NSString* token = response.token;  // token = @"aeac1bc8f0735e4283305652ab"
    NSString* paymentHardware = response.paymentHardware;  // paymentHardware = @example-value(payment_hardware)
}
```
Response when any payment peripheral finishes reading payment data from
  a magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_data_id | string | Yes | None | a unique per-transaction id that represents the payment data that      was received. Note that this changes for each transaction even if      the payment method is the exact same. It can be used in other API      requests to easily refer to payment data in a secure way      (e.g. [Payment.Settle](#settle))
token | string | No | None | a unique and secure token that can be used to identify the      payment method that was used. E.g. for magnetic stripe cards,      this can be used as a proxy representation of a credit card      PAN.
peripheral_name | string | No | None | denotes which payment hardware received data. The possible values      are the same as those returned by      [Hardware.GetConnectedPeripherals](#getconnectedperipherals).


<a name="Payment.ReceivePaymentDataThenPreAuthorize-Payment.StartedPreAuthorizing"></a>
### Payment.StartedPreAuthorizing

```objective_c
onStartedPreAuthorizing:^(DDPStartedPreAuthorizing* response) {
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
}
```
Response when the pre authorization starts via the payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_data_id | string | No | None | the payment_data_id from [Hardware.ReceivePaymentData](#receivepaymentdata)      now being used to PreAuthorize the payment
cents | uint32 | No | 0 | number of cents in the PreAuthorize
currency | string | No | "USD" | currency of the PreAuthorize (cents field)
livemode | bool | No | false | livemode is false when the request will not actually PreAuthorize funds      i.e. it will be a 'test' PreAuthorization. livemode is      true when funds will be actually PreAuthorized.


<a name="Payment.ReceivePaymentDataThenPreAuthorize-Payment.PreAuthorized"></a>
### Payment.PreAuthorized

```objective_c
onPreAuthorized:^(DDPPreAuthorized* response) {
    int32_t status = response.status;  // status = @"e:PaymentStatus.SUCCESS"
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    NSString* preauthId = response.preauthId;  // preauthId = @"paut-Eo9YqkOTnmxt6EcL"
    NSString* info = response.info;  // info = @"Human-readable description"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
    uint32_t cents = response.cents;  // cents = 128
    NSString* currency = response.currency;  // currency = @"USD"
    BOOL livemode = response.livemode;  // livemode = NO
    int32_t paymentType = response.paymentType;  // paymentType = @"e:PaymentType.CARD"
}
```
Response when the payment PreAuthorization returns from the payment
  processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1, UNKNOWN = 2      indicating the status of the PreAuthorize request
payment_data_id | string | No | None | the payment_data_id from [Hardware.ReceivePaymentData](#receivepaymentdata)      used to get the payment data
preauth_id | string | No | None | a unique id associated with the PreAuthorized payment, which can      be used to [Payment.Settle](#settle) or      [Payment.VoidPreAuthorize](#voidpreauthorize)
info | string | No | None | a general purpose information string, e.g. if a call to      [Payment.PreAuthorize](#preauthorize) was unsuccessful then this field is      populated with an explanation
customer_id | string | No | None | the unique customer id associated with the payment device,      e.g. the customer's credit card
merchant_account_id | string | No | None | the id of the merchant account used to process the payment
card_type | string | No | None | the type of card used (if applicable/available), e.g. "Visa"
last4 | string | No | None | the last four digits of the card used (if applicable/available)
commercial | bool | No | None | indicates whether the card is a commericial card (if      applicable/available)
debit | bool | No | None | indicates whether the card is a debit card (if      applicable/available)
created_at | string | No | None | indicates the time the PreAuthorization was created
cents | uint32 | No | 0 | number of cents PreAuthorized
currency | string | No | "USD" | currency of the transaction amount (cents field)
livemode | bool | No | false | livemode is false when the request did not actually PreAuthorize funds      with the customer's bank, i.e. it was a 'test' PreAuthorization. livemode is      true when funds were actually PreAuthorized
payment_type | PaymentType | No | CARD | payment_type specifies the type of payment object used to      PreAuthorize the payment, i.e. a credit/debit card, apple pay,      android pay, bitcoin, or a third party app/service


# Hardware

## ConfigurePeripheral
<a name="Hardware.ConfigurePeripheralArgs"></a>
### Hardware.ConfigurePeripheralArgs
```objective_c
DDPConfigurePeripheralArgs* args = [[DDPConfigurePeripheralArgs alloc] init];
args.peripheralName = @"MagTekSureSwipe21040145";
args.disable = NO;

[DotDashPayAPI.hardware configurePeripheral:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfiguredPeripheral:^(DDPConfiguredPeripheral* response) {
    // Handle response
}];
```
```javascript
var args = {
    peripheral_name: "MagTekSureSwipe21040145",
    disable: false,
};

dotdashpay.hardware.configurePeripheral(args)
    .onConfiguredPeripheral(function (response) {
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Configures a peripheral that is attached to the DotDashPay
          @@ddp-hardware-module-name@@. You are not responsible for
          complicated configuration options, but can control things
          like whether the peripheral is enabled.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
peripheral_name | string | Yes | None | denotes which peripheral you want to configure. The possible      values are the same as those returned by      [Hardware.GetConnectedPeripherals](#getconnectedperipherals).
disable | bool | No | false | set to true if you want to disable the named peripheral


<a name="Hardware.ConfigurePeripheral-Hardware.ConfiguredPeripheral"></a>
### Hardware.ConfiguredPeripheral

```objective_c
onConfiguredPeripheral:^(DDPConfiguredPeripheral* response) {
}
```
An [ErrorResponse](#errorresponse) will be returned if peripheral
  configuration failed, so if this response is received, it can be
  assumed that configuration succeeded.

(There are no fields on Hardware.ConfiguredPeripheral)

## ReceivePaymentData
<a name="Hardware.ReceivePaymentDataArgs"></a>
### Hardware.ReceivePaymentDataArgs
```objective_c
DDPReceivePaymentDataArgs* args = [[DDPReceivePaymentDataArgs alloc] init];
args.useExistingData = NO;
args.useExistingDataSecondsWindow = 5;

[DotDashPayAPI.hardware receivePaymentData:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onStartedReceivingPaymentData:^(DDPStartedReceivingPaymentData* response) {
    // Handle response
} onReceivedPaymentData:^(DDPReceivedPaymentData* response) {
    // Handle response
}];
```
```javascript
var args = {
    use_existing_data: false,
    use_existing_data_seconds_window: 5,
};

dotdashpay.hardware.receivePaymentData(args)
    .onStartedReceivingPaymentData(function (response) {
        var peripheral_name = response.peripheral_name; // peripheral_name = "MagTekSureSwipe21040145"
    })
    .onReceivedPaymentData(function (response) {
        var payment_data_id = response.payment_data_id; // payment_data_id = "pdid-iPqy9F82v5qexbtz"
        var token = response.token; // token = "aeac1bc8f0735e4283305652ab"
        var peripheral_name = response.peripheral_name; // peripheral_name = "MagTekSureSwipe21040145"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Listen for payment data from the payment peripheral hardware
          connected to the @@ddp-hardware-module-name@@.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
use_existing_data | bool | No | false | use_existing_data is true when we want to use data that has      recently gone through payment peripherals, e.g. a card swipe that      occured shortly before this request, else if use_existing_data is      false, it will only return new data through the hardware      peripherals.
use_existing_data_seconds_window | uint32 | No | 20 | use_existing_data_seconds_window is used when      use_existing_data==true to determine how recently we should use      existing data going through the payments peripherals.


<a name="Hardware.ReceivePaymentData-Hardware.StartedReceivingPaymentData"></a>
### Hardware.StartedReceivingPaymentData

```objective_c
onStartedReceivingPaymentData:^(DDPStartedReceivingPaymentData* response) {
    NSString* paymentHardware = response.paymentHardware;  // paymentHardware = @example-value(payment_hardware)
}
```
Response when any payment peripheral begins receiving payment data
  (e.g. when a magnetic-stripe reader starts reading data from a
  magnetic stripe card).

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
peripheral_name | string | No | None | denotes which payment hardware started to receive data. The      possible values are the same as those returned by      [Hardware.GetConnectedPeripherals](#getconnectedperipherals).


<a name="Hardware.ReceivePaymentData-Hardware.ReceivedPaymentData"></a>
### Hardware.ReceivedPaymentData

```objective_c
onReceivedPaymentData:^(DDPReceivedPaymentData* response) {
    NSString* paymentDataId = response.paymentDataId;  // paymentDataId = @"pdid-iPqy9F82v5qexbtz"
    NSString* token = response.token;  // token = @"aeac1bc8f0735e4283305652ab"
    NSString* paymentHardware = response.paymentHardware;  // paymentHardware = @example-value(payment_hardware)
}
```
Response when any payment peripheral finishes reading payment data from
  a magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_data_id | string | Yes | None | a unique per-transaction id that represents the payment data that      was received. Note that this changes for each transaction even if      the payment method is the exact same. It can be used in other API      requests to easily refer to payment data in a secure way      (e.g. [Payment.Settle](#settle))
token | string | No | None | a unique and secure token that can be used to identify the      payment method that was used. E.g. for magnetic stripe cards,      this can be used as a proxy representation of a credit card      PAN.
peripheral_name | string | No | None | denotes which payment hardware received data. The possible values      are the same as those returned by      [Hardware.GetConnectedPeripherals](#getconnectedperipherals).


## GetDatalinkStatus
<a name="Hardware.GetDatalinkStatusArgs"></a>
### Hardware.GetDatalinkStatusArgs
```objective_c
DDPGetDatalinkStatusArgs* args = [[DDPGetDatalinkStatusArgs alloc] init];

[DotDashPayAPI.hardware getDatalinkStatus:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRetrievedDatalinkStatus:^(DDPRetrievedDatalinkStatus* response) {
    // Handle response
}];
```
```javascript
var args = {
};

dotdashpay.hardware.getDatalinkStatus(args)
    .onRetrievedDatalinkStatus(function (response) {
        var connected = response.connected; // connected = true
        var info = response.info; // info = "Human-readable description"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Checks the status of the connection between the API and the
          DotDashPay @@ddp-hardware-module-name@@.



(There are no fields on Hardware.GetDatalinkStatusArgs)

<a name="Hardware.GetDatalinkStatus-Hardware.RetrievedDatalinkStatus"></a>
### Hardware.RetrievedDatalinkStatus

```objective_c
onRetrievedDatalinkStatus:^(DDPRetrievedDatalinkStatus* response) {
    BOOL connected = response.connected;  // connected = YES
    NSString* info = response.info;  // info = @"Human-readable description"
}
```
RetrievedDatalinkStatus contains information related to the status of
  the connection between the API and the @@ddp-hardware-module-name@@.

  An [ErrorResponse](#errorresponse) will not be returned if the datalink is
  down. Rather, this resposne will indicate that the datalink is
  disconnected and will return more information about why that is in
  the `info` field.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
connected | bool | Yes | None | will be true if your machine is connected to the      @@ddp-hardware-module-name@@
info | string | No | None | provides information of the status of the connection between your      machine is connected to the @@ddp-hardware-module-name@@.


## GetInternetStatus
<a name="Hardware.GetInternetStatusArgs"></a>
### Hardware.GetInternetStatusArgs
```objective_c
DDPGetInternetStatusArgs* args = [[DDPGetInternetStatusArgs alloc] init];

[DotDashPayAPI.hardware getInternetStatus:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRetrievedInternetStatus:^(DDPRetrievedInternetStatus* response) {
    // Handle response
}];
```
```javascript
var args = {
};

dotdashpay.hardware.getInternetStatus(args)
    .onRetrievedInternetStatus(function (response) {
        var connected = response.connected; // connected = true
        var info = response.info; // info = "Human-readable description"
        var connected_interfaces = response.connected_interfaces; // connected_interfaces = [[u'wifi', u'...']]
        var ip_addresses = response.ip_addresses; // ip_addresses = [[u'192.168.1.128', u'...']]
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Returns the status of the DotDashPay
          @@ddp-hardware-module-name@@'s connection to the Internet.



(There are no fields on Hardware.GetInternetStatusArgs)

<a name="Hardware.GetInternetStatus-Hardware.RetrievedInternetStatus"></a>
### Hardware.RetrievedInternetStatus

```objective_c
onRetrievedInternetStatus:^(DDPRetrievedInternetStatus* response) {
    BOOL connected = response.connected;  // connected = YES
    NSString* info = response.info;  // info = @"Human-readable description"
    NSArray* connectedInterfaces = response.connectedInterfacesArray;  // connectedInterfaces = @[@"wifi", ...]
    NSArray* ipAddresses = response.ipAddressesArray;  // ipAddresses = @[@"192.168.1.128", ...]
}
```
RetrievedInternetStatus contains information related to the status
  of the @@ddp-hardware-module-name@@'s connection to the Internet
  (which is required to process payments in 'Online' mode).

  An [ErrorResponse](#errorresponse) will not be returned if the Internet is
  down. Rather, this resposne will indicate that the Internet is
  disconnected and will return more information about why that is in
  the `info` field.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
connected | bool | Yes | None | will be true if the @@ddp-hardware-module-name@@ is connected to      the Internet
info | string | No | None | provides information of the status of the      @@ddp-hardware-module-name@@'s connection to the Internet.
connected_interfaces | string [Array] | No | None | indicates which interfaces are currently connected to the      Internet (e.g. ethernet, wifi, etc.).
ip_addresses | string [Array] | No | None | for each connected_interface, indicates the IP address of that      interface. In some cases this will not be available for security      reasons.


## ConfigureWifi
<a name="Hardware.ConfigureWifiArgs"></a>
### Hardware.ConfigureWifiArgs
```objective_c
DDPConfigureWifiArgs* args = [[DDPConfigureWifiArgs alloc] init];
args.essid = @"Network Name";
args.password = @"wifi_password";
args.username = @"wifi_username";
args.x509Cert = @"308202123082017b02020dfa300d06092a864886f70d010105050030819b310b3009060355040613024a50310e300c06035504081305546f6b796f3110300e060355040713074368756f2d6b753111300f060355040a13084672616e6b34444431183016060355040b130f5765624365727420537570706f7274311830160603550403130f4672616e6b344444205765622043413123302106092a864886f70d0109011614737570706f7274406672616e6b3464642e636f6d301e170d3132303832323035323635345a170d3137303832313035323635345a304a310b3009060355040613024a50310e300c06035504080c05546f6b796f3111300f060355040a0c084672616e6b3444443118301606035504030c0f7777772e6578616d706c652e636f6d305c300d06092a864886f70d0101010500034b0030480241009bfc6690798442bbab13fd2b7bf8de1512e5f193e3068a7bb8b1e19e26bb9501bfe730ed648502dd1569a834b006ec3f353c1e1b2b8ffa8f001bdf07c6ac53070203010001300d06092a864886f70d01010505000381810014b64cbb817933e671a4da516fcb081d8d60ecbc18c7734759b1f22048bb61fafc4dad898dd121ebd5d8e5bad6a636fd745083b60fc71ddf7de52e817f45e09fe23e79eed73031c72072d9582e2afe125a3445a119087c89475f4a95be23214a5372da2a052f2ec970f65bfafddfb431b2c14a9c062543a1e6b41e7f869b16400a";
args.staticIp = @"192.168.1.128";
args.staticSubnetMask = @"255.255.255.0";
args.staticGateway = @"192.168.1.1";
args.staticDns = @"8.8.8.8";
args.saveConfiguration = YES;
args.autoReconnect = YES;

[DotDashPayAPI.hardware configureWifi:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfiguredWifi:^(DDPConfiguredWifi* response) {
    // Handle response
}];
```
```javascript
var args = {
    essid: "Network Name",
    password: "wifi_password",
    username: "wifi_username",
    x509cert: "308202123082017b02020dfa300d06092a864886f70d010105050030819b310b3009060355040613024a50310e300c06035504081305546f6b796f3110300e060355040713074368756f2d6b753111300f060355040a13084672616e6b34444431183016060355040b130f5765624365727420537570706f7274311830160603550403130f4672616e6b344444205765622043413123302106092a864886f70d0109011614737570706f7274406672616e6b3464642e636f6d301e170d3132303832323035323635345a170d3137303832313035323635345a304a310b3009060355040613024a50310e300c06035504080c05546f6b796f3111300f060355040a0c084672616e6b3444443118301606035504030c0f7777772e6578616d706c652e636f6d305c300d06092a864886f70d0101010500034b0030480241009bfc6690798442bbab13fd2b7bf8de1512e5f193e3068a7bb8b1e19e26bb9501bfe730ed648502dd1569a834b006ec3f353c1e1b2b8ffa8f001bdf07c6ac53070203010001300d06092a864886f70d01010505000381810014b64cbb817933e671a4da516fcb081d8d60ecbc18c7734759b1f22048bb61fafc4dad898dd121ebd5d8e5bad6a636fd745083b60fc71ddf7de52e817f45e09fe23e79eed73031c72072d9582e2afe125a3445a119087c89475f4a95be23214a5372da2a052f2ec970f65bfafddfb431b2c14a9c062543a1e6b41e7f869b16400a",
    static_ip: "192.168.1.128",
    static_subnet_mask: "255.255.255.0",
    static_gateway: "192.168.1.1",
    static_dns: "8.8.8.8",
    save_configuration: true,
    auto_reconnect: true,
};

dotdashpay.hardware.configureWifi(args)
    .onConfiguredWifi(function (response) {
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Instructs the @@ddp-hardware-module-name@@ to attempt to
          connect to a wireless network. If the connection is not
          successful, an [ErrorResponse](#errorresponse) response will be
          triggered.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
essid | string | Yes | None | the ESSID of the wireless network to connect to
password | string | No | None | the password needed to connect to the wireless network if      applicable
username | string | No | None | the username needed to connect to the wireless network if      applicable
x509cert | string | No | None | the X.509 certificate needed to connect to the wireless network    * if applicable.
static_ip | string | No | "" | if set to anything other than "", this will cause the wireless      interface to be configured with a static IP address, subnet mask,      and gateway IP address
static_subnet_mask | string | No | None | if static_ip is set, this should be set to the subnet mask of the      network you are connecting to
static_gateway | string | No | None | if static_ip is set, this should be set to the IP address of the      gateway you are connected to
static_dns | string | No | None | the IP address of a DNS server to use for resolving      hostnames. This can be set even if static_ip is set to "".
save_configuration | bool | No | true | saves the wifi configuration (if successful), so that you don't      have to configure wifi everytime your machine restarts
auto_reconnect | bool | No | true | if save_configuration is true, setting this to true will cause      the @@ddp-hardware-module-name@@ to automatically attempt to      reconnect to any previously saved wifi configurations


<a name="Hardware.ConfigureWifi-Hardware.ConfiguredWifi"></a>
### Hardware.ConfiguredWifi

```objective_c
onConfiguredWifi:^(DDPConfiguredWifi* response) {
}
```
An [ErrorResponse](#errorresponse) will be returned if wifi configuration
  failed, so if this response is received, it can be assumed that
  configuration succeeded.

(There are no fields on Hardware.ConfiguredWifi)

## ConfigureEthernet
<a name="Hardware.ConfigureEthernetArgs"></a>
### Hardware.ConfigureEthernetArgs
```objective_c
DDPConfigureEthernetArgs* args = [[DDPConfigureEthernetArgs alloc] init];
args.staticIp = @"192.168.1.128";
args.staticSubnetMask = @"255.255.255.0";
args.staticGateway = @"192.168.1.1";
args.staticDns = @"8.8.8.8";
args.saveConfiguration = YES;
args.autoReconnect = YES;

[DotDashPayAPI.hardware configureEthernet:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfiguredEthernet:^(DDPConfiguredEthernet* response) {
    // Handle response
}];
```
```javascript
var args = {
    static_ip: "192.168.1.128",
    static_subnet_mask: "255.255.255.0",
    static_gateway: "192.168.1.1",
    static_dns: "8.8.8.8",
    save_configuration: true,
    auto_reconnect: true,
};

dotdashpay.hardware.configureEthernet(args)
    .onConfiguredEthernet(function (response) {
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Instructs the @@ddp-hardware-module-name@@ to attempt to
          connect to a wired network. If the connection is not
          successful, an [ErrorResponse](#errorresponse) response will be
          triggered.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
static_ip | string | No | "" | if set to anything other than "", this will cause the wireless      interface to be configured with a static IP address, subnet mask,      and gateway IP address
static_subnet_mask | string | No | None | if static_ip is set, this should be set to the subnet mask of the      network you are connecting to
static_gateway | string | No | None | if static_ip is set, this should be set to the IP address of the      gateway you are connected to
static_dns | string | No | None | the IP address of a DNS server to use for resolving      hostnames. This can be set even if static_ip is set to "".
save_configuration | bool | No | true | saves the wifi configuration (if successful), so that you don't      have to configure wifi everytime your machine restarts
auto_reconnect | bool | No | true | if save_configuration is true, setting this to true will cause      the @@ddp-hardware-module-name@@ to automatically attempt to      reconnect to any previously saved wifi configurations


<a name="Hardware.ConfigureEthernet-Hardware.ConfiguredEthernet"></a>
### Hardware.ConfiguredEthernet

```objective_c
onConfiguredEthernet:^(DDPConfiguredEthernet* response) {
}
```
An [ErrorResponse](#errorresponse) will be returned if ethernet
  configuration failed, so if this response is received, it can be
  assumed that configuration succeeded.

(There are no fields on Hardware.ConfiguredEthernet)

## GetConnectedPeripherals
<a name="Hardware.GetConnectedPeripheralsArgs"></a>
### Hardware.GetConnectedPeripheralsArgs
```objective_c
DDPGetConnectedPeripheralsArgs* args = [[DDPGetConnectedPeripheralsArgs alloc] init];

[DotDashPayAPI.hardware getConnectedPeripherals:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRetrievedConnectedPeripherals:^(DDPRetrievedConnectedPeripherals* response) {
    // Handle response
}];
```
```javascript
var args = {
};

dotdashpay.hardware.getConnectedPeripherals(args)
    .onRetrievedConnectedPeripherals(function (response) {
        var attached_valid_periphs = response.attached_valid_periphs; // attached_valid_periphs = [[u'MagTekSureSwipe21040145', u'...']]
        var attached_error_periphs = response.attached_error_periphs; // attached_error_periphs = [[]]
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Returns a list of all attached hardware peripherals to the
          DotDashPay @@ddp-hardware-module-name@@.



(There are no fields on Hardware.GetConnectedPeripheralsArgs)

<a name="Hardware.GetConnectedPeripherals-Hardware.RetrievedConnectedPeripherals"></a>
### Hardware.RetrievedConnectedPeripherals

```objective_c
onRetrievedConnectedPeripherals:^(DDPRetrievedConnectedPeripherals* response) {
    NSArray* attachedValidPeriphs = response.attachedValidPeriphsArray;  // attachedValidPeriphs = @[@"MagTekSureSwipe21040145", ...]
    NSArray* attachedErrorPeriphs = response.attachedErrorPeriphsArray;  // attachedErrorPeriphs = @[]
}
```
Response indicating the set of hardware peripherals that are
  connected to the DotDashPay @@ddp-hardware-module-name@@.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
attached_valid_periphs | string [Array] | No | None | an array of attach peripherals that are working correctly
attached_error_periphs | string [Array] | No | None | an array of attach peripherals that are not working correctly


# Global

## Configure
<a name="Global.ConfigureArgs"></a>
### Global.ConfigureArgs
```objective_c
DDPConfigureArgs* args = [[DDPConfigureArgs alloc] init];
args.simulate = YES;
args.testing = YES;

[DotDashPayAPI.global configure:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfigured:^(DDPConfigured* response) {
    // Handle response
}];
```
```javascript
var args = {
    simulate: true,
    testing: true,
};

dotdashpay.global.configure(args)
    .onConfigured(function (response) {
        var succeeded = response.succeeded; // succeeded = true
        var info = response.info; // info = "Human-readable description"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
This function sets up the connection to the DotDashPay
          @@ddp-hardware-module-name@@ and validates all of the input
          configuration options, e.g. it makes sure the connected
          payment peripherals match the payment peripherals specified
          in the configuration, etc.



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
simulate | bool | No | true | Whether the DotDashPay simulator should be used.
testing | bool | No | true | Whether to use sandboxed payment processing.


<a name="Global.Configure-Global.Configured"></a>
### Global.Configured

```objective_c
onConfigured:^(DDPConfigured* response) {
    int32_t status = response.status;  // status = @"e:PaymentStatus.SUCCESS"
    NSString* info = response.info;  // info = @"Human-readable description"
}
```
Configured contains information about the status of
  the API and the @@ddp-hardware-module-name@@'s current configuration
  status.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
succeeded | bool | Yes | None | true if configuration was a success, false otherwise
info | string | Yes | None | info contains a human-readable description of the status


# Information

## GetCustomerId
<a name="Information.GetCustomerIdArgs"></a>
### Information.GetCustomerIdArgs
```objective_c
DDPGetCustomerIdArgs* args = [[DDPGetCustomerIdArgs alloc] init];
args.token = @"aeac1bc8f0735e4283305652ab";

[DotDashPayAPI.information getCustomerId:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRetrievedCustomerId:^(DDPRetrievedCustomerId* response) {
    // Handle response
}];
```
```javascript
var args = {
    token: "aeac1bc8f0735e4283305652ab",
};

dotdashpay.information.getCustomerId(args)
    .onRetrievedCustomerId(function (response) {
        var customer_id = response.customer_id; // customer_id = "2DAuJMBrMyaC"
    })
    .onError(function (errorData) {
        if (errorData.errorCode == "") {
            console.log("Error: " + errorData.errorMessage);
        }
    });
```
Returns the customer id associated with a tokenized payment
          method. The token (if available) is returned by
          [Hardware.ReceivedPaymentData](#receivedpaymentdata).



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
token | string | Yes | "" | a token returned by [Hardware.ReceivedPaymentData](#receivedpaymentdata)


<a name="Information.GetCustomerId-Information.RetrievedCustomerId"></a>
### Information.RetrievedCustomerId

```objective_c
onRetrievedCustomerId:^(DDPRetrievedCustomerId* response) {
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
}
```
Returns information about a customer id, if one could be found.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
customer_id | string | No | None | the unique customer id. This will be empty if no customer id      could be found.


