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
error_code | string | No | None | a semantic code used to identify the error (see [Error      Codes](#error-codes) for a complete list)
error_message | string | No | None | a human-readable message associated with the error


# Payment

## PreAuthorize
```objc
[DotDashPayAPI.payment preAuthorize:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onPreAuthorized:^(DDPPreAuthorized* response) {
    // Handle response
}];
```
Authorize a payment, i.e. verify that the payment_info is
          accurate and that the desired funds can be withdrawn.

          **This request does not charge a payer.** You will have to
          call [Payment.Settle](#settle) later. Alternatively, if you
          wish to void this authorization, which you should do if you
          do not plan to settle it, you should call
          [Payment.VoidPreAuthorize](#voidpreauthorize).

<a name="Payment.PreAuthorizeArgs"></a>
### Payment.PreAuthorizeArgs
```objc
DDPPreAuthorizeArgs* args = [[DDPPreAuthorizeArgs alloc] init];
args.payid = @"ReceivedPaymentData.payid";
args.dollars = 1;
args.cents = 28;
args.currency = @"USD";
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id associated with the payment data e.g. returned from      WaitForPaymentDataFromHardware
dollars | uint32 | Yes | None | number of dollars to charge, the X in $X.Y
cents | uint32 | No | 0 | number of cents to charge, the Y in $X.Y
currency | string | No | "USD" | currency of the dollars and cents (only USD currently supported)


<a name="Payment.PreAuthorize-Payment.PreAuthorized"></a>
### Payment.PreAuthorized

```objc
onPreAuthorized:^(DDPPreAuthorized* response) {
    NSString* payid = response.payid;  // payid = @"ReceivedPaymentData.payid"
    DDPPreAuthorized_PaymentStatus status = response.status;  // status = DDPPreAuthorized_PaymentStatus_Success (0)
    NSString* transactionId = response.transactionId;  // transactionId = @"txn-NG9jR86SC3hjzQHA8h5X3pwz"
    NSString* info = response.info;  // info = @"Authorization succeeded"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
}
```
Response when the payment authorization returns from the payment
  processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id from [Payment.PreAuthorize](#preauthorize) used to settle the      payment
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1
transaction_id | string | Yes | None | a unique id that can be used to identify the authorization
info | string | Yes | None | a unique id that can be used to identify the authorization
customer_id | string | No | None | a unique id that can be used to identify the authorization


## Settle
```objc
[DotDashPayAPI.payment settle:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onStartedSettling:^(DDPStartedSettling* response) {
    // Handle response
} onSettled:^(DDPSettled* response) {
    // Handle response
}];
```
Settles a payment, which is equivalent to initiating a
          transfer of money from the payer's bank to your bank.

          You may call this function either after receiving data from
          calling [Hardware.ReceivePaymentData](#receivepaymentdata) or after
          receiving data from calling
          [Payment.PreAuthorize](#preauthorize).

          If called directly after receiving payment data from the
          hardware, then this immediately charges the payer. If called
          after authorizing the payment, then this request will
          finalize the transaction and the transaction cannot be
          voided later on.

<a name="Payment.SettleArgs"></a>
### Payment.SettleArgs
```objc
DDPSettleArgs* args = [[DDPSettleArgs alloc] init];
args.payid = @"ReceivedPaymentData.payid";
args.dollars = 1;
args.cents = 28;
args.currency = @"USD";
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id associated with the payment data e.g. returned from      WaitForPaymentDataFromHardware
dollars | uint32 | Yes | None | number of dollars to charge, the X in $X.Y
cents | uint32 | No | 0 | number of cents to charge, the Y in $X.Y
currency | string | No | "USD" | currency of the dollars and cents (only USD currently supported)


<a name="Payment.Settle-Payment.StartedSettling"></a>
### Payment.StartedSettling

```objc
onStartedSettling:^(DDPStartedSettling* response) {
    NSString* payid = response.payid;  // payid = @"ReceivedPaymentData.payid"
}
```
Response when the settle payment initially starts settling the
  payment with the processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | No | None | if applicable, the id from [Payment.PreAuthorize](#preauthorize) used to      settle the payment


<a name="Payment.Settle-Payment.Settled"></a>
### Payment.Settled

```objc
onSettled:^(DDPSettled* response) {
    NSString* payid = response.payid;  // payid = @"ReceivedPaymentData.payid"
    DDPSettled_PaymentStatus status = response.status;  // status = DDPSettled_PaymentStatus_Success (0)
    NSString* transactionId = response.transactionId;  // transactionId = @"txn-NG9jR86SC3hjzQHA8h5X3pwz"
    NSString* info = response.info;  // info = @"Settlement finished successfully"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
    uint32_t dollars = response.dollars;  // dollars = 1
    uint32_t cents = response.cents;  // cents = 28
    NSString* currency = response.currency;  // currency = @"USD"
}
```
Response when the settle payment request returns from the payment
  processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1, UNKNOWN = 2
payid | string | No | None | if applicable, the id from [Payment.PreAuthorize](#preauthorize) used to      settle the payment
transaction_id | string | No | None | the id of the settled payment (if settle is unsuccessful)
info | string | No | None | a general purpose information string, e.g. if a call to      [Payment.Settle](#settle) was unsuccessful, this field is      populated with an explanation for it was unsuccessful,
customer_id | string | No | None | the unique customer id
merchant_account_id | string | No | None | the id of the merchant account used to process the payment
card_type | string | No | None | the type of card used (if applicable/available), e.g. "Visa"
last4 | string | No | None | the last four digits of the card used (if applicable/available)
commercial | bool | No | None | indicates whether the card is a commericial card (if      applicable/available)
debit | bool | No | None | indicates whether the card is a debit card (if      applicable/available)
created_at | string | No | None | indicates the time the settlement was created
dollars | uint32 | No | None | number of dollars in the settlement, the X in $X.Y
cents | uint32 | No | 0 | number of cents in the settlement, the Y in $X.Y
currency | string | No | "USD" | currency of the dollars and cents


## VoidPreAuthorize
```objc
[DotDashPayAPI.payment voidPreAuthorize:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onVoidedPreAuthorize:^(DDPVoidedPreAuthorize* response) {
    // Handle response
}];
```
Void an authorized payment. You can only call this function
          for a payment that was successfully authorized via
          [Payment.PreAuthorize](#preauthorize) but has not yet been
          settled.

          You should not use this function for if the payment was
          already settled. If it was settled, you should call
          [Payment.Refund](#refund) instead.

<a name="Payment.VoidPreAuthorizeArgs"></a>
### Payment.VoidPreAuthorizeArgs
```objc
DDPVoidPreAuthorizeArgs* args = [[DDPVoidPreAuthorizeArgs alloc] init];
args.transactionId = nil;
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
transaction_id | string | Yes | None | the transaction id returned from the payment processor


<a name="Payment.VoidPreAuthorize-Payment.VoidedPreAuthorize"></a>
### Payment.VoidedPreAuthorize

```objc
onVoidedPreAuthorize:^(DDPVoidedPreAuthorize* response) {
}
```
Response when an authorization void request is returned from the
  payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## VoidRefund
```objc
[DotDashPayAPI.payment voidRefund:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onVoidedRefund:^(DDPVoidedRefund* response) {
    // Handle response
}];
```


<a name="Payment.VoidRefundArgs"></a>
### Payment.VoidRefundArgs
```objc
DDPVoidRefundArgs* args = [[DDPVoidRefundArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Payment.VoidRefund-Payment.VoidedRefund"></a>
### Payment.VoidedRefund

```objc
onVoidedRefund:^(DDPVoidedRefund* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## Refund
```objc
[DotDashPayAPI.payment refund:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRefunded:^(DDPRefunded* response) {
    // Handle response
}];
```
Refund a settled payment. You can only call this function
          for a payment that was successfully settled.

<a name="Payment.RefundArgs"></a>
### Payment.RefundArgs
```objc
DDPRefundArgs* args = [[DDPRefundArgs alloc] init];
args.transactionId = @"Settled.transactionId";
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
transaction_id | string | Yes | None | the transaction id returned from the payment processor


<a name="Payment.Refund-Payment.Refunded"></a>
### Payment.Refunded

```objc
onRefunded:^(DDPRefunded* response) {
    DDPRefunded_PaymentStatus status = response.status;  // status = DDPRefunded_PaymentStatus_Success (0)
    NSString* transactionId = response.transactionId;  // transactionId = @"txn-NG9jR86SC3hjzQHA8h5X3pwz"
    NSString* info = response.info;  // info = @"Refund was successful"
}
```
Response when the payment refund returns from the payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1
transaction_id | string | Yes | None | a unique id that can be used to identify the refund
info | string | Yes | None | a unique id that can be used to identify the refund


## AdjustPreAuthorize
```objc
[DotDashPayAPI.payment adjustPreAuthorize:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onAdjustedPreAuthorize:^(DDPAdjustedPreAuthorize* response) {
    // Handle response
}];
```


<a name="Payment.AdjustPreAuthorizeArgs"></a>
### Payment.AdjustPreAuthorizeArgs
```objc
DDPAdjustPreAuthorizeArgs* args = [[DDPAdjustPreAuthorizeArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Payment.AdjustPreAuthorize-Payment.AdjustedPreAuthorize"></a>
### Payment.AdjustedPreAuthorize

```objc
onAdjustedPreAuthorize:^(DDPAdjustedPreAuthorize* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## GetCardBalance
```objc
[DotDashPayAPI.payment getCardBalance:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRetrievedCardBalance:^(DDPRetrievedCardBalance* response) {
    // Handle response
}];
```


<a name="Payment.GetCardBalanceArgs"></a>
### Payment.GetCardBalanceArgs
```objc
DDPGetCardBalanceArgs* args = [[DDPGetCardBalanceArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Payment.GetCardBalance-Payment.RetrievedCardBalance"></a>
### Payment.RetrievedCardBalance

```objc
onRetrievedCardBalance:^(DDPRetrievedCardBalance* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## CreateRecurring
```objc
[DotDashPayAPI.payment createRecurring:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onCreatedRecurring:^(DDPCreatedRecurring* response) {
    // Handle response
}];
```


<a name="Payment.CreateRecurringArgs"></a>
### Payment.CreateRecurringArgs
```objc
DDPCreateRecurringArgs* args = [[DDPCreateRecurringArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Payment.CreateRecurring-Payment.CreatedRecurring"></a>
### Payment.CreatedRecurring

```objc
onCreatedRecurring:^(DDPCreatedRecurring* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## ConfigureProcessor
```objc
[DotDashPayAPI.payment configureProcessor:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfiguredProcessor:^(DDPConfiguredProcessor* response) {
    // Handle response
}];
```


<a name="Payment.ConfigureProcessorArgs"></a>
### Payment.ConfigureProcessorArgs
```objc
DDPConfigureProcessorArgs* args = [[DDPConfigureProcessorArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Payment.ConfigureProcessor-Payment.ConfiguredProcessor"></a>
### Payment.ConfiguredProcessor

```objc
onConfiguredProcessor:^(DDPConfiguredProcessor* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## ReceivePaymentDataThenSettle
```objc
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
This is a convenience request that combines
          [Hardware.ReceivePaymentData](#receivepaymentdata) and
          [Payment.Settle](#settle) into a single request.

          Once a payer interacts with a payment peripheral, they are
          immediately charged for the amount specified in this
          request. Generally, this is quicker than calling
          [Hardware.ReceivePaymentData](#receivepaymentdata) followed by
          [Payment.Settle](#settle) yourself.

<a name="Payment.ReceivePaymentDataThenSettleArgs"></a>
### Payment.ReceivePaymentDataThenSettleArgs
```objc
DDPReceivePaymentDataThenSettleArgs* args = [[DDPReceivePaymentDataThenSettleArgs alloc] init];
args.dollars = 1;
args.cents = 28;
args.currency = @"USD";
args.onlyNewData = NO;
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
dollars | uint32 | Yes | None | number of dollars to charge, the X in $X.Y
cents | uint32 | No | 0 | number of cents to charge, the Y in $X.Y
currency | string | No | "USD" | currency of the dollars and cents (only USD currently supported)
only_new_data | bool | No | true | if true, then only return new data, e.g.  from a new magstripe      swipe or NFC touch, if false, then return new data OR recent data      from, e.g. a user swiping before being prompted.


<a name="Payment.ReceivePaymentDataThenSettle-Hardware.StartedReceivingPaymentData"></a>
### Hardware.StartedReceivingPaymentData

```objc
onStartedReceivingPaymentData:^(DDPStartedReceivingPaymentData* response) {
}
```
Response when any payment peripheral begins receiving payment data
  (e.g. when a magnetic-stripe reader starts reading data from a
  magnetic stripe card).

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Payment.ReceivePaymentDataThenSettle-Hardware.ReceivedPaymentData"></a>
### Hardware.ReceivedPaymentData

```objc
onReceivedPaymentData:^(DDPReceivedPaymentData* response) {
    NSString* payid = response.payid;  // payid = @"ReceivedPaymentData.payid"
}
```
Response when any payment peripheral finishes reading payment data from
  a magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | m


<a name="Payment.ReceivePaymentDataThenSettle-Payment.StartedSettling"></a>
### Payment.StartedSettling

```objc
onStartedSettling:^(DDPStartedSettling* response) {
    NSString* payid = response.payid;  // payid = @"ReceivedPaymentData.payid"
}
```
Response when the settle payment initially starts settling the
  payment with the processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | No | None | if applicable, the id from [Payment.PreAuthorize](#preauthorize) used to      settle the payment


<a name="Payment.ReceivePaymentDataThenSettle-Payment.Settled"></a>
### Payment.Settled

```objc
onSettled:^(DDPSettled* response) {
    NSString* payid = response.payid;  // payid = @"ReceivedPaymentData.payid"
    DDPSettled_PaymentStatus status = response.status;  // status = DDPSettled_PaymentStatus_Success (0)
    NSString* transactionId = response.transactionId;  // transactionId = @"txn-NG9jR86SC3hjzQHA8h5X3pwz"
    NSString* info = response.info;  // info = @"Settlement finished successfully"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
    uint32_t dollars = response.dollars;  // dollars = 1
    uint32_t cents = response.cents;  // cents = 28
    NSString* currency = response.currency;  // currency = @"USD"
}
```
Response when the settle payment request returns from the payment
  processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1, UNKNOWN = 2
payid | string | No | None | if applicable, the id from [Payment.PreAuthorize](#preauthorize) used to      settle the payment
transaction_id | string | No | None | the id of the settled payment (if settle is unsuccessful)
info | string | No | None | a general purpose information string, e.g. if a call to      [Payment.Settle](#settle) was unsuccessful, this field is      populated with an explanation for it was unsuccessful,
customer_id | string | No | None | the unique customer id
merchant_account_id | string | No | None | the id of the merchant account used to process the payment
card_type | string | No | None | the type of card used (if applicable/available), e.g. "Visa"
last4 | string | No | None | the last four digits of the card used (if applicable/available)
commercial | bool | No | None | indicates whether the card is a commericial card (if      applicable/available)
debit | bool | No | None | indicates whether the card is a debit card (if      applicable/available)
created_at | string | No | None | indicates the time the settlement was created
dollars | uint32 | No | None | number of dollars in the settlement, the X in $X.Y
cents | uint32 | No | 0 | number of cents in the settlement, the Y in $X.Y
currency | string | No | "USD" | currency of the dollars and cents


# Hardware

## ConfigureHardware
```objc
[DotDashPayAPI.hardware configureHardware:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfiguredHardware:^(DDPConfiguredHardware* response) {
    // Handle response
}];
```


<a name="Hardware.ConfigureHardwareArgs"></a>
### Hardware.ConfigureHardwareArgs
```objc
DDPConfigureHardwareArgs* args = [[DDPConfigureHardwareArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Hardware.ConfigureHardware-Hardware.ConfiguredHardware"></a>
### Hardware.ConfiguredHardware

```objc
onConfiguredHardware:^(DDPConfiguredHardware* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## ReceivePaymentData
```objc
[DotDashPayAPI.hardware receivePaymentData:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onStartedReceivingPaymentData:^(DDPStartedReceivingPaymentData* response) {
    // Handle response
} onReceivedPaymentData:^(DDPReceivedPaymentData* response) {
    // Handle response
}];
```
Listen for payment data from the payment peripheral hardware
          connected to the @@ddp-hardware-module-name@@.

<a name="Hardware.ReceivePaymentDataArgs"></a>
### Hardware.ReceivePaymentDataArgs
```objc
DDPReceivePaymentDataArgs* args = [[DDPReceivePaymentDataArgs alloc] init];
[args.paymentHardwareArray addValue:DDPReceivePaymentDataArgs_PaymentHardwareType_Msr];
args.useExistingData = NO;
args.useExistingDataSecondsWindow = 0;
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_hardware | PaymentHardwareType [Array] | No | None | payment_hardware is used to define which payment hardware we      should be receiving from


<a name="Hardware.ReceivePaymentData-Hardware.StartedReceivingPaymentData"></a>
### Hardware.StartedReceivingPaymentData

```objc
onStartedReceivingPaymentData:^(DDPStartedReceivingPaymentData* response) {
}
```
Response when any payment peripheral begins receiving payment data
  (e.g. when a magnetic-stripe reader starts reading data from a
  magnetic stripe card).

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Hardware.ReceivePaymentData-Hardware.ReceivedPaymentData"></a>
### Hardware.ReceivedPaymentData

```objc
onReceivedPaymentData:^(DDPReceivedPaymentData* response) {
    NSString* payid = response.payid;  // payid = @"ReceivedPaymentData.payid"
}
```
Response when any payment peripheral finishes reading payment data from
  a magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | m


## GetDatalinkStatus
```objc
[DotDashPayAPI.hardware getDatalinkStatus:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRetrievedDatalinkStatus:^(DDPRetrievedDatalinkStatus* response) {
    // Handle response
}];
```
Checks the status of the connection between the API and the
          DotDashPay @@ddp-hardware-module-name@@.

<a name="Hardware.GetDatalinkStatusArgs"></a>
### Hardware.GetDatalinkStatusArgs
```objc
DDPGetDatalinkStatusArgs* args = [[DDPGetDatalinkStatusArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Hardware.GetDatalinkStatus-Hardware.RetrievedDatalinkStatus"></a>
### Hardware.RetrievedDatalinkStatus

```objc
onRetrievedDatalinkStatus:^(DDPRetrievedDatalinkStatus* response) {
    DDPRetrievedDatalinkStatus_Status status = response.status;  // status = DDPRetrievedDatalinkStatus_Status_Success (0)
    NSString* info = response.info;  // info = @"API is connected"
}
```
RetrievedDatalinkStatus contains information related to the status of
  the connection between the API and the @@ddp-hardware-module-name@@.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | m
info | string | Yes | None | m


## GetInternetStatus
```objc
[DotDashPayAPI.hardware getInternetStatus:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRetrievedInternetStatus:^(DDPRetrievedInternetStatus* response) {
    // Handle response
}];
```
Returns the status of the DotDashPay
          @@ddp-hardware-module-name@@'s connection to the Internet.

<a name="Hardware.GetInternetStatusArgs"></a>
### Hardware.GetInternetStatusArgs
```objc
DDPGetInternetStatusArgs* args = [[DDPGetInternetStatusArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Hardware.GetInternetStatus-Hardware.RetrievedInternetStatus"></a>
### Hardware.RetrievedInternetStatus

```objc
onRetrievedInternetStatus:^(DDPRetrievedInternetStatus* response) {
    DDPRetrievedInternetStatus_Status status = response.status;  // status = DDPRetrievedInternetStatus_Status_Success (0)
    NSString* info = response.info;  // info = @"Internet is connected"
}
```
RetrievedInternetStatus contains information related to the status
  of the @@ddp-hardware-module-name@@'s connection to the Internet
  (which is required to process payments in 'Online' mode).

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | m
info | string | Yes | None | m


## ConfigureWifi
```objc
[DotDashPayAPI.hardware configureWifi:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfiguredWifi:^(DDPConfiguredWifi* response) {
    // Handle response
}];
```


<a name="Hardware.ConfigureWifiArgs"></a>
### Hardware.ConfigureWifiArgs
```objc
DDPConfigureWifiArgs* args = [[DDPConfigureWifiArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Hardware.ConfigureWifi-Hardware.ConfiguredWifi"></a>
### Hardware.ConfiguredWifi

```objc
onConfiguredWifi:^(DDPConfiguredWifi* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## ConfigureEthernet
```objc
[DotDashPayAPI.hardware configureEthernet:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfiguredEthernet:^(DDPConfiguredEthernet* response) {
    // Handle response
}];
```


<a name="Hardware.ConfigureEthernetArgs"></a>
### Hardware.ConfigureEthernetArgs
```objc
DDPConfigureEthernetArgs* args = [[DDPConfigureEthernetArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Hardware.ConfigureEthernet-Hardware.ConfiguredEthernet"></a>
### Hardware.ConfiguredEthernet

```objc
onConfiguredEthernet:^(DDPConfiguredEthernet* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## GetConnectedPeripherals
```objc
[DotDashPayAPI.hardware getConnectedPeripherals:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onRetrievedConnectedPeripherals:^(DDPRetrievedConnectedPeripherals* response) {
    // Handle response
}];
```
Returns a list of all attached hardware peripherals to the
          DotDashPay @@ddp-hardware-module-name@@.

<a name="Hardware.GetConnectedPeripheralsArgs"></a>
### Hardware.GetConnectedPeripheralsArgs
```objc
DDPGetConnectedPeripheralsArgs* args = [[DDPGetConnectedPeripheralsArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


<a name="Hardware.GetConnectedPeripherals-Hardware.RetrievedConnectedPeripherals"></a>
### Hardware.RetrievedConnectedPeripherals

```objc
onRetrievedConnectedPeripherals:^(DDPRetrievedConnectedPeripherals* response) {
    NSArray* attachedValidPeriphs = response.attachedValidPeriphsArray;  // attachedValidPeriphs = @[@"MagTekSureSwipe21040145", ...]
    NSArray* attachedErrorPeriphs = response.attachedErrorPeriphsArray;  // attachedErrorPeriphs = @[@""]
}
```
Response indicating the set of hardware peripherals that are
  connected to the DotDashPay @@ddp-hardware-module-name@@.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
attached_valid_periphs | string [Array] | No | None | m
attached_error_periphs | string [Array] | No | None | m


# Global

## Configure
```objc
[DotDashPayAPI.global configure:args onError:^(DDPErrorResponse* error) {
    // Handle error response
} onConfigured:^(DDPConfigured* response) {
    // Handle response
}];
```
This function sets up the connection to the DotDashPay
          @@ddp-hardware-module-name@@ and validates all of the input
          configuration options, e.g. it makes sure the connected
          payment peripherals match the payment peripherals specified
          in the configuration, etc.

          <private>
          Use DotDashPay.addSetupOption("option", "value") for adding
          general setup options and use the DotDashPay.add*(...)
          methods, e.g. addPeripheral for adding specific setup
          options.

          Note that all setup options are optional when simulate is
          set to true. When simulate is false you must specify the set
          of attached peripherals as well as the payment
          processor. The default values are provided below for each
          attribute.
          </private>

<a name="Global.ConfigureArgs"></a>
### Global.ConfigureArgs
```objc
DDPConfigureArgs* args = [[DDPConfigureArgs alloc] init];
args.simulate = YES;
args.testing = YES;
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
simulate | bool | No | true | Whether the DotDashPay simulator should be used.
testing | bool | No | true | Whether to use sandboxed payment processing.


<a name="Global.Configure-Global.Configured"></a>
### Global.Configured

```objc
onConfigured:^(DDPConfigured* response) {
    DDPConfigured_Status status = response.status;  // status = DDPConfigured_Status_Success (0)
    NSString* info = response.info;  // info = @"Global configuration succeeded"
}
```
Configured contains information about the status of
  the API and the @@ddp-hardware-module-name@@'s current configuration
  status.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | status is an enum where SUCCESS = 0 and FAIL = 1
info | string | Yes | None | info contains a human-readable description of the status


# Information

