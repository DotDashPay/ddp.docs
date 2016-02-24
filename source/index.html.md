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
Authorize a payment, i.e. verify that the payment_info is
          accurate and that the desired funds can be withdrawn.

          **This request does not charge a payer.** You will have to
          call [Payment.Settle](#settle) later. Alternatively, if you
          wish to void this authorization, which you should do if you
          do not plan to settle it, you should call
          [Payment.VoidAuthorization](#voidauthorization).

### Payment.PreAuthorizeArgs



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id associated with the payment data e.g. returned from      WaitForPaymentDataFromHardware
dollars | uint32 | Yes | None | number of dollars to charge, the X in $X.Y
cents | uint32 | No | 0 | number of cents to charge, the Y in $X.Y

### Payment.FinishedAuthorization

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



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id associated with the payment data e.g. returned from      WaitForPaymentDataFromHardware
dollars | uint32 | Yes | None | number of dollars to charge, the X in $X.Y
cents | uint32 | No | 0 | number of cents to charge, the Y in $X.Y

### Payment.StartedSettlement

Response when the settle payment initially starts settling the
  payment with the processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | No | None | if applicable, the id from [Payment.PreAuthorize](#preauthorize) used to      settle the payment

### Payment.FinishedSettlement

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
Void an authorized payment. You can only call this function
          for a payment that was successfully authorized via
          [Payment.PreAuthorize](#preauthorize) but has not yet been
          settled.

          You should not use this function for if the payment was
          already settled. If it was settled, you should call
          [Payment.Refund](#refund) instead.

### Payment.VoidAuthorizationArgs



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
transaction_id | string | Yes | None | the transaction id returned from the payment processor

### Payment.FinishedAuthorizationVoid

Response when an authorization void request is returned from the
  payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------

## VoidRefund


### Payment.VoidRefundArgs

/

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id from [Payment.PreAuthorize](#preauthorize) used to settle the      payment
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1
transaction_id | string | Yes | None | a unique id that can be used to identify the authorization
info | string | Yes | None | a unique id that can be used to identify the authorization
customer_id | string | No | None | a unique id that can be used to identify the authorization

## Refund
Refund a settled payment. You can only call this function
          for a payment that was successfully settled.

### Payment.RefundArgs



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
transaction_id | string | Yes | None | the transaction id returned from the payment processor

### Payment.FinishedRefund

Response when the payment refund returns from the payment processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1
transaction_id | string | Yes | None | a unique id that can be used to identify the refund
info | string | Yes | None | a unique id that can be used to identify the refund

## AdjustAuthorizationAmount


### Payment.AdjustAuthorizationAmountArgs

/

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id from [Payment.PreAuthorize](#preauthorize) used to settle the      payment
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1
transaction_id | string | Yes | None | a unique id that can be used to identify the authorization
info | string | Yes | None | a unique id that can be used to identify the authorization
customer_id | string | No | None | a unique id that can be used to identify the authorization

## QueryCardBalance


### Payment.QueryCardBalanceArgs

/

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id from [Payment.PreAuthorize](#preauthorize) used to settle the      payment
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1
transaction_id | string | Yes | None | a unique id that can be used to identify the authorization
info | string | Yes | None | a unique id that can be used to identify the authorization
customer_id | string | No | None | a unique id that can be used to identify the authorization

## CreateRecurringPayment


### Payment.CreateRecurringPaymentArgs

/

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id from [Payment.PreAuthorize](#preauthorize) used to settle the      payment
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1
transaction_id | string | Yes | None | a unique id that can be used to identify the authorization
info | string | Yes | None | a unique id that can be used to identify the authorization
customer_id | string | No | None | a unique id that can be used to identify the authorization

## ConfigurePaymentProcessor


### Payment.ConfigurePaymentProcessorArgs

/

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | the id from [Payment.PreAuthorize](#preauthorize) used to settle the      payment
status | PaymentStatus | Yes | None | an enum with values: SUCCESS = 0, FAIL = 1
transaction_id | string | Yes | None | a unique id that can be used to identify the authorization
info | string | Yes | None | a unique id that can be used to identify the authorization
customer_id | string | No | None | a unique id that can be used to identify the authorization

## ListenForPaymentDataThenSettle
This is a convenience request that combines
          [Hardware.ListenForPaymentData](#listenforpaymentdata) and
          [Payment.Settle](#settle) into a single request.

          Once a payer interacts with a payment peripheral, they are
          immediately charged for the amount specified in this
          request. Generally, this is quicker than calling
          [Hardware.ListenForPaymentData](#listenforpaymentdata) followed by
          [Payment.Settle](#settle) yourself.

### Payment.ListenForPaymentDataThenSettleArgs



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
dollars | uint32 | Yes | None | number of dollars to charge, the X in $X.Y
cents | uint32 | No | 0 | number of cents to charge, the Y in $X.Y
only_new_data | bool | No | true | if true, then only return new data, e.g.  from a new magstripe      swipe or NFC touch, if false, then return new data OR recent data      from, e.g. a user swiping before being prompted.

### Hardware.PaymentHardwareStartedRead

Response when the magnetic-stripe reader starts reading data from a
  magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------

### Hardware.PaymentHardwareFinishedRead

Response when the magnetic-stripe reader finishes reading data from
  a magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | m

### Payment.StartedSettlement

Response when the settle payment initially starts settling the
  payment with the processor.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | No | None | if applicable, the id from [Payment.PreAuthorize](#preauthorize) used to      settle the payment

### Payment.FinishedSettlement

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


### Hardware.ConfigureHardwareArgs

/

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | @define-response(APIConnectionStatus)    APIConnectionStatus contains information related to the status of   the connection between the API and the @@ddp-hardware-module-name@@.
info | string | Yes | None | @define-response(APIConnectionStatus)    APIConnectionStatus contains information related to the status of   the connection between the API and the @@ddp-hardware-module-name@@.

## ListenForPaymentData
Listen for payment data from the payment peripheral hardware
          connected to the @@ddp-hardware-module-name@@.

### Hardware.ListenForPaymentDataArgs



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payment_hardware | PaymentHardwareType [Array] | No | None | payment_hardware is used to define which payment hardware we      should be listening to

### Hardware.PaymentHardwareStartedRead

Response when the magnetic-stripe reader starts reading data from a
  magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------

### Hardware.PaymentHardwareFinishedRead

Response when the magnetic-stripe reader finishes reading data from
  a magnetic stripe card.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
payid | string | Yes | None | m

## GetAPIConnectionStatus
Checks the status of the connection between the API and the
          DotDashPay @@ddp-hardware-module-name@@.

### Hardware.GetAPIConnectionStatusArgs



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------

### Hardware.APIConnectionStatus

APIConnectionStatus contains information related to the status of
  the connection between the API and the @@ddp-hardware-module-name@@.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | m
info | string | Yes | None | m

## GetInternetConnectionStatus
Returns the status of the DotDashPay
          @@ddp-hardware-module-name@@'s connection to the Internet.

### Hardware.GetInternetConnectionStatusArgs



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------

### Hardware.InternetConnectionStatus

InternetConnectionStatus contains information related to the status
  of the @@ddp-hardware-module-name@@'s connection to the Internet
  (which is required to process payments in 'Online' mode).

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | m
info | string | Yes | None | m

## ConfigureWifi


### Hardware.ConfigureWifiArgs

/

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | @define-response(APIConnectionStatus)    APIConnectionStatus contains information related to the status of   the connection between the API and the @@ddp-hardware-module-name@@.
info | string | Yes | None | @define-response(APIConnectionStatus)    APIConnectionStatus contains information related to the status of   the connection between the API and the @@ddp-hardware-module-name@@.

## ConfigureEthernet


### Hardware.ConfigureEthernetArgs

/

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | @define-response(APIConnectionStatus)    APIConnectionStatus contains information related to the status of   the connection between the API and the @@ddp-hardware-module-name@@.
info | string | Yes | None | @define-response(APIConnectionStatus)    APIConnectionStatus contains information related to the status of   the connection between the API and the @@ddp-hardware-module-name@@.

## ListConnectedPeripherals
Returns a list of all attached hardware peripherals to the
          DotDashPay @@ddp-hardware-module-name@@.

### Hardware.ListConnectedPeripheralsArgs



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------

### Hardware.ConnectedPeripheralsList

Response indicating the set of hardware peripherals that are
  connected to the DotDashPay @@ddp-hardware-module-name@@.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
attached_valid_periphs | string [Array] | No | None | m
attached_error_periphs | string [Array] | No | None | m

# Global

## Configure
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



Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
simulate | bool | No | true | Whether the DotDashPay simulator should be used.
testing | bool | No | true | Whether to use sandboxed payment processing.

### Global.GlobalConfigurationStatus

GlobalConfigurationStatus contains information about the status of
  the API and the @@ddp-hardware-module-name@@'s current configuration
  status.

Parameter   |   Type   | Required? | Default | Description
------------- | -------- | --------- | ------- | -----------
status | Status | Yes | None | status is an enum where SUCCESS = 0 and FAIL = 1
info | string | Yes | None | info contains a human-readable description of the status

# Information

