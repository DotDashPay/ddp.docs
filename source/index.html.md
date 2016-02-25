# Errors

### ErrorResponse

An ErrorResponse is a potential response from any request. It is
  always a completion event; i.e. no other responses will ever come
  after it.

  Every API function call allows you to specify an error callback
  which takes `ErrorResponse` as an argument. If this callback is ever
  invoked, a non-recoverable error has occurred on the
  @@ddp-hardware-module-name@@. For instance, if you call
  [Hardware.ListenForPaymentData](#listenforpaymentdata), a payer swipes their
  magnetic stripe card, and the magnetic card reader fails to extract
  the payer's card information, an `ErrorResponse` will be returned so
  that you can alert the payer their card did not read and call
  [Hardware.ListenForPaymentData](#listenforpaymentdata) again.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
error_code | string | No | None | a semantic code used to identify the error (see [Error      Codes](#error-codes) for a complete list)
error_message | string | No | None | a human-readable message associated with the error


# Payment

## PreAuthorize
```objc
[DotDashPayAPI.payment preAuthorize:args onError:^(ErrorResponse* error) {
    // Handle error response
} onFinishedAuthorization:^(FinishedAuthorization* response) {
    // Handle response
}];
```
Authorize a payment, i.e. verify that the payment_info is
          accurate and that the desired funds can be withdrawn.

          **This request does not charge a payer.** You will have to
          call [Payment.Settle](#settle) later. Alternatively, if you
          wish to void this authorization, which you should do if you
          do not plan to settle it, you should call
          [Payment.VoidAuthorization](#voidauthorization).

### Payment.PreAuthorizeArgs
```objc
PreAuthorizeArgs* args = [[PreAuthorizeArgs alloc] init];
args.payid = @"PaymentHardwareFinishedRead.payid";
args.dollars = 1;
args.cents = 28;
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id associated with the payment data e.g. returned from      WaitForPaymentDataFromHardware
dollars | uint32 | Yes | None | number of dollars to charge, the X in $X.Y
cents | uint32 | No | 0 | number of cents to charge, the Y in $X.Y


### Payment.FinishedAuthorization

```objc
onFinishedAuthorization:^(FinishedAuthorization* response) {
    NSString* payid = response.payid;  // payid = @"PaymentHardwareFinishedRead.payid"
    FinishedAuthorization_PaymentStatus status = response.status;  // status = FinishedAuthorization_PaymentStatus_Success (0)
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
[DotDashPayAPI.payment settle:args onError:^(ErrorResponse* error) {
    // Handle error response
} onStartedSettlement:^(StartedSettlement* response) {
    // Handle response
} onFinishedSettlement:^(FinishedSettlement* response) {
    // Handle response
}];
```
Settles a payment, which is equivalent to initiating a
          transfer of money from the payer's bank to your bank.

          You may call this function either after receiving data from
          calling [Hardware.ListenForPaymentData](#listenforpaymentdata) or after
          receiving data from calling
          [Payment.PreAuthorize](#preauthorize).

          If called directly after receiving payment data from the
          hardware, then this immediately charges the payer. If called
          after authorizing the payment, then this request will
          finalize the transaction and the transaction cannot be
          voided later on.

### Payment.SettleArgs
```objc
SettleArgs* args = [[SettleArgs alloc] init];
args.payid = @"PaymentHardwareFinishedRead.payid";
args.dollars = 1;
args.cents = 28;
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id associated with the payment data e.g. returned from      WaitForPaymentDataFromHardware
dollars | uint32 | Yes | None | number of dollars to charge, the X in $X.Y
cents | uint32 | No | 0 | number of cents to charge, the Y in $X.Y


### Payment.StartedSettlement

```objc
onStartedSettlement:^(StartedSettlement* response) {
    NSString* payid = response.payid;  // payid = @"PaymentHardwareFinishedRead.payid"
}
```
Response when the settle payment initially starts settling the
  payment with the processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | No | None | if applicable, the id from [Payment.PreAuthorize](#preauthorize) used to      settle the payment


### Payment.FinishedSettlement

```objc
onFinishedSettlement:^(FinishedSettlement* response) {
    NSString* payid = response.payid;  // payid = @"PaymentHardwareFinishedRead.payid"
    FinishedSettlement_PaymentStatus status = response.status;  // status = FinishedSettlement_PaymentStatus_Success (0)
    NSString* transactionId = response.transactionId;  // transactionId = @"txn-NG9jR86SC3hjzQHA8h5X3pwz"
    NSString* info = response.info;  // info = @"Settlement finished successfully"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
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


## VoidAuthorization
```objc
[DotDashPayAPI.payment voidAuthorization:args onError:^(ErrorResponse* error) {
    // Handle error response
} onFinishedAuthorizationVoid:^(FinishedAuthorizationVoid* response) {
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

### Payment.VoidAuthorizationArgs
```objc
VoidAuthorizationArgs* args = [[VoidAuthorizationArgs alloc] init];
args.transactionId = @"PreAuthorize.transactionId";
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
transaction_id | string | Yes | None | the transaction id returned from the payment processor


### Payment.FinishedAuthorizationVoid

```objc
onFinishedAuthorizationVoid:^(FinishedAuthorizationVoid* response) {
}
```
Response when an authorization void request is returned from the
  payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## VoidRefund
```objc
[DotDashPayAPI.payment voidRefund:args onError:^(ErrorResponse* error) {
    // Handle error response
} onFinishedVoidRefund:^(FinishedVoidRefund* response) {
    // Handle response
}];
```


### Payment.VoidRefundArgs
```objc
VoidRefundArgs* args = [[VoidRefundArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Payment.FinishedVoidRefund

```objc
onFinishedVoidRefund:^(FinishedVoidRefund* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## Refund
```objc
[DotDashPayAPI.payment refund:args onError:^(ErrorResponse* error) {
    // Handle error response
} onFinishedRefund:^(FinishedRefund* response) {
    // Handle response
}];
```
Refund a settled payment. You can only call this function
          for a payment that was successfully settled.

### Payment.RefundArgs
```objc
RefundArgs* args = [[RefundArgs alloc] init];
args.transactionId = @"Settle.transactionId";
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
transaction_id | string | Yes | None | the transaction id returned from the payment processor


### Payment.FinishedRefund

```objc
onFinishedRefund:^(FinishedRefund* response) {
    FinishedRefund_PaymentStatus status = response.status;  // status = FinishedRefund_PaymentStatus_Success (0)
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


## AdjustAuthorizationAmount
```objc
[DotDashPayAPI.payment adjustAuthorizationAmount:args onError:^(ErrorResponse* error) {
    // Handle error response
} onFinishedAdjustAuthorizationAmount:^(FinishedAdjustAuthorizationAmount* response) {
    // Handle response
}];
```


### Payment.AdjustAuthorizationAmountArgs
```objc
AdjustAuthorizationAmountArgs* args = [[AdjustAuthorizationAmountArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Payment.FinishedAdjustAuthorizationAmount

```objc
onFinishedAdjustAuthorizationAmount:^(FinishedAdjustAuthorizationAmount* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## QueryCardBalance
```objc
[DotDashPayAPI.payment queryCardBalance:args onError:^(ErrorResponse* error) {
    // Handle error response
} onCardBalance:^(CardBalance* response) {
    // Handle response
}];
```


### Payment.QueryCardBalanceArgs
```objc
QueryCardBalanceArgs* args = [[QueryCardBalanceArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Payment.CardBalance

```objc
onCardBalance:^(CardBalance* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## CreateRecurringPayment
```objc
[DotDashPayAPI.payment createRecurringPayment:args onError:^(ErrorResponse* error) {
    // Handle error response
} onFinishedCreateRecurringPayment:^(FinishedCreateRecurringPayment* response) {
    // Handle response
}];
```


### Payment.CreateRecurringPaymentArgs
```objc
CreateRecurringPaymentArgs* args = [[CreateRecurringPaymentArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Payment.FinishedCreateRecurringPayment

```objc
onFinishedCreateRecurringPayment:^(FinishedCreateRecurringPayment* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## ConfigurePaymentProcessor
```objc
[DotDashPayAPI.payment configurePaymentProcessor:args onError:^(ErrorResponse* error) {
    // Handle error response
} onFinishedConfigurePaymentProcessor:^(FinishedConfigurePaymentProcessor* response) {
    // Handle response
}];
```


### Payment.ConfigurePaymentProcessorArgs
```objc
ConfigurePaymentProcessorArgs* args = [[ConfigurePaymentProcessorArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Payment.FinishedConfigurePaymentProcessor

```objc
onFinishedConfigurePaymentProcessor:^(FinishedConfigurePaymentProcessor* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## ListenForPaymentDataThenSettle
```objc
[DotDashPayAPI.payment listenForPaymentDataThenSettle:args onError:^(ErrorResponse* error) {
    // Handle error response
} onPaymentHardwareStartedRead:^(PaymentHardwareStartedRead* response) {
    // Handle response
} onPaymentHardwareFinishedRead:^(PaymentHardwareFinishedRead* response) {
    // Handle response
} onStartedSettlement:^(StartedSettlement* response) {
    // Handle response
} onFinishedSettlement:^(FinishedSettlement* response) {
    // Handle response
}];
```
This is a convenience request that combines
          [Hardware.ListenForPaymentData](#listenforpaymentdata) and
          [Payment.Settle](#settle) into a single request.

          Once a payer interacts with a payment peripheral, they are
          immediately charged for the amount specified in this
          request. Generally, this is quicker than calling
          [Hardware.ListenForPaymentData](#listenforpaymentdata) followed by
          [Payment.Settle](#settle) yourself.

### Payment.ListenForPaymentDataThenSettleArgs
```objc
ListenForPaymentDataThenSettleArgs* args = [[ListenForPaymentDataThenSettleArgs alloc] init];
args.dollars = 1;
args.cents = 28;
args.onlyNewData = NO;
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
dollars | uint32 | Yes | None | number of dollars to charge, the X in $X.Y
cents | uint32 | No | 0 | number of cents to charge, the Y in $X.Y
only_new_data | bool | No | true | if true, then only return new data, e.g.  from a new magstripe      swipe or NFC touch, if false, then return new data OR recent data      from, e.g. a user swiping before being prompted.


### Hardware.PaymentHardwareStartedRead

```objc
onPaymentHardwareStartedRead:^(PaymentHardwareStartedRead* response) {
}
```
Response when the magnetic-stripe reader starts reading data from a
  magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Hardware.PaymentHardwareFinishedRead

```objc
onPaymentHardwareFinishedRead:^(PaymentHardwareFinishedRead* response) {
    NSString* payid = response.payid;  // payid = @"PaymentHardwareFinishedRead.payid"
}
```
Response when the magnetic-stripe reader finishes reading data from
  a magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | m


### Payment.StartedSettlement

```objc
onStartedSettlement:^(StartedSettlement* response) {
    NSString* payid = response.payid;  // payid = @"PaymentHardwareFinishedRead.payid"
}
```
Response when the settle payment initially starts settling the
  payment with the processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | No | None | if applicable, the id from [Payment.PreAuthorize](#preauthorize) used to      settle the payment


### Payment.FinishedSettlement

```objc
onFinishedSettlement:^(FinishedSettlement* response) {
    NSString* payid = response.payid;  // payid = @"PaymentHardwareFinishedRead.payid"
    FinishedSettlement_PaymentStatus status = response.status;  // status = FinishedSettlement_PaymentStatus_Success (0)
    NSString* transactionId = response.transactionId;  // transactionId = @"txn-NG9jR86SC3hjzQHA8h5X3pwz"
    NSString* info = response.info;  // info = @"Settlement finished successfully"
    NSString* customerId = response.customerId;  // customerId = @"2DAuJMBrMyaC"
    NSString* merchantAccountId = response.merchantAccountId;  // merchantAccountId = @"merchantAccountNumber"
    NSString* cardType = response.cardType;  // cardType = @"Visa"
    NSString* last4 = response.last4;  // last4 = @"0000"
    BOOL commercial = response.commercial;  // commercial = NO
    BOOL debit = response.debit;  // debit = NO
    NSString* createdAt = response.createdAt;  // createdAt = @"915148799.75"
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


# Hardware

## ConfigureHardware
```objc
[DotDashPayAPI.hardware configureHardware:args onError:^(ErrorResponse* error) {
    // Handle error response
} onConfigureHardwareFinished:^(ConfigureHardwareFinished* response) {
    // Handle response
}];
```


### Hardware.ConfigureHardwareArgs
```objc
ConfigureHardwareArgs* args = [[ConfigureHardwareArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Hardware.ConfigureHardwareFinished

```objc
onConfigureHardwareFinished:^(ConfigureHardwareFinished* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## ListenForPaymentData
```objc
[DotDashPayAPI.hardware listenForPaymentData:args onError:^(ErrorResponse* error) {
    // Handle error response
} onPaymentHardwareStartedRead:^(PaymentHardwareStartedRead* response) {
    // Handle response
} onPaymentHardwareFinishedRead:^(PaymentHardwareFinishedRead* response) {
    // Handle response
}];
```
Listen for payment data from the payment peripheral hardware
          connected to the @@ddp-hardware-module-name@@.

### Hardware.ListenForPaymentDataArgs
```objc
ListenForPaymentDataArgs* args = [[ListenForPaymentDataArgs alloc] init];
[args.paymentHardwareArray addValue:ListenForPaymentDataArgs_PaymentHardwareType_Msr];
args.useExistingData = NO;
args.useExistingDataSecondsWindow = 0;
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_hardware | PaymentHardwareType [Array] | No | None | payment_hardware is used to define which payment hardware we      should be listening to


### Hardware.PaymentHardwareStartedRead

```objc
onPaymentHardwareStartedRead:^(PaymentHardwareStartedRead* response) {
}
```
Response when the magnetic-stripe reader starts reading data from a
  magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Hardware.PaymentHardwareFinishedRead

```objc
onPaymentHardwareFinishedRead:^(PaymentHardwareFinishedRead* response) {
    NSString* payid = response.payid;  // payid = @"PaymentHardwareFinishedRead.payid"
}
```
Response when the magnetic-stripe reader finishes reading data from
  a magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | m


## GetAPIConnectionStatus
```objc
[DotDashPayAPI.hardware getAPIConnectionStatus:args onError:^(ErrorResponse* error) {
    // Handle error response
} onAPIConnectionStatus:^(APIConnectionStatus* response) {
    // Handle response
}];
```
Checks the status of the connection between the API and the
          DotDashPay @@ddp-hardware-module-name@@.

### Hardware.GetAPIConnectionStatusArgs
```objc
GetAPIConnectionStatusArgs* args = [[GetAPIConnectionStatusArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Hardware.APIConnectionStatus

```objc
onAPIConnectionStatus:^(APIConnectionStatus* response) {
    APIConnectionStatus_Status status = response.status;  // status = APIConnectionStatus_Status_Success (0)
    NSString* info = response.info;  // info = @"API is connected"
}
```
APIConnectionStatus contains information related to the status of
  the connection between the API and the @@ddp-hardware-module-name@@.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | m
info | string | Yes | None | m


## GetInternetConnectionStatus
```objc
[DotDashPayAPI.hardware getInternetConnectionStatus:args onError:^(ErrorResponse* error) {
    // Handle error response
} onInternetConnectionStatus:^(InternetConnectionStatus* response) {
    // Handle response
}];
```
Returns the status of the DotDashPay
          @@ddp-hardware-module-name@@'s connection to the Internet.

### Hardware.GetInternetConnectionStatusArgs
```objc
GetInternetConnectionStatusArgs* args = [[GetInternetConnectionStatusArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Hardware.InternetConnectionStatus

```objc
onInternetConnectionStatus:^(InternetConnectionStatus* response) {
    InternetConnectionStatus_Status status = response.status;  // status = InternetConnectionStatus_Status_Success (0)
    NSString* info = response.info;  // info = @"Internet is connected"
}
```
InternetConnectionStatus contains information related to the status
  of the @@ddp-hardware-module-name@@'s connection to the Internet
  (which is required to process payments in 'Online' mode).

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | m
info | string | Yes | None | m


## ConfigureWifi
```objc
[DotDashPayAPI.hardware configureWifi:args onError:^(ErrorResponse* error) {
    // Handle error response
} onConfigureWifiFinished:^(ConfigureWifiFinished* response) {
    // Handle response
}];
```


### Hardware.ConfigureWifiArgs
```objc
ConfigureWifiArgs* args = [[ConfigureWifiArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Hardware.ConfigureWifiFinished

```objc
onConfigureWifiFinished:^(ConfigureWifiFinished* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## ConfigureEthernet
```objc
[DotDashPayAPI.hardware configureEthernet:args onError:^(ErrorResponse* error) {
    // Handle error response
} onConfigureEthernetFinished:^(ConfigureEthernetFinished* response) {
    // Handle response
}];
```


### Hardware.ConfigureEthernetArgs
```objc
ConfigureEthernetArgs* args = [[ConfigureEthernetArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Hardware.ConfigureEthernetFinished

```objc
onConfigureEthernetFinished:^(ConfigureEthernetFinished* response) {
}
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


## ListConnectedPeripherals
```objc
[DotDashPayAPI.hardware listConnectedPeripherals:args onError:^(ErrorResponse* error) {
    // Handle error response
} onConnectedPeripheralsList:^(ConnectedPeripheralsList* response) {
    // Handle response
}];
```
Returns a list of all attached hardware peripherals to the
          DotDashPay @@ddp-hardware-module-name@@.

### Hardware.ListConnectedPeripheralsArgs
```objc
ListConnectedPeripheralsArgs* args = [[ListConnectedPeripheralsArgs alloc] init];
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------


### Hardware.ConnectedPeripheralsList

```objc
onConnectedPeripheralsList:^(ConnectedPeripheralsList* response) {
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
[DotDashPayAPI.global configure:args onError:^(ErrorResponse* error) {
    // Handle error response
} onGlobalConfigurationStatus:^(GlobalConfigurationStatus* response) {
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

### Global.ConfigureArgs
```objc
ConfigureArgs* args = [[ConfigureArgs alloc] init];
args.simulate = YES;
args.testing = YES;
```


Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
simulate | bool | No | true | Whether the DotDashPay simulator should be used.
testing | bool | No | true | Whether to use sandboxed payment processing.


### Global.GlobalConfigurationStatus

```objc
onGlobalConfigurationStatus:^(GlobalConfigurationStatus* response) {
    GlobalConfigurationStatus_Status status = response.status;  // status = GlobalConfigurationStatus_Status_Success (0)
    NSString* info = response.info;  // info = @"Global configuration succeeded"
}
```
GlobalConfigurationStatus contains information about the status of
  the API and the @@ddp-hardware-module-name@@'s current configuration
  status.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | status is an enum where SUCCESS = 0 and FAIL = 1
info | string | Yes | None | info contains a human-readable description of the status


# Information

