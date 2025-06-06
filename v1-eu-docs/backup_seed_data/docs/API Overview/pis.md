# PISP API Overview

## Base URL
The base URL for all PIS APIs is: `https://rs1.api.stripe.uk-hub-prod.ozoneapi.co.uk/open-banking/v3.1/pisp/**`

## Supported Payment Types
The Stripe API currently only supports:
- Domestic Payments

The Stripe API __does not__ support:
- International payments
- File & Bulk payments
- `payment-details` end-points
- Domestic Scheduled Payments
- Domestic Standing Orders

The payment request from TPP must have the creditor information.
## Overview
The following apply to all domestic payment consents:

### `InstructedAmount/Amount`
- There is no MAX `InstructedAmount/Amount` mandated by Stripe API.
-  Stripe suggest PISP notify the PSU that the same limits apply as in their Stripe app. It is possible from time to time that `domestic-payment-consents` is authorised, but the payment initiation fails due to account limits.

### `InstructedAmount/Currency`
- `InstructedAmount/Currency` must be `GBP`

### `RemittanceInformation/Reference`
`RemittanceInformation/Reference` is a mandatory field and must adhere to the following:
- Valid characters:
  - A-Z
  - a-z
  - 0-9
  - "space"
  - The characters "&", "-", ".", "/"

- Contiguous characters – user enters 6 or more valid characters but without contiguous string of at least 6 alphanumeric characters
- Must contain a contiguous string of at least 6 alphanumeric characters
- Homogeneous string – user enters 6 or more valid characters (including valid non-alphanumeric characters) - After stripping out non-alphanumeric characters the resulting string cannot consist of all the same character

The PISP may also opt to populate reference field on behalf of the PSU

### `CreditorAccount`
<!-- theme: info -->
> ### Note
>
> Domestic-payment-consents will only be authorised if the `CreditorAccount` details are sent received
> If a consent contains recipient info that does not meet this criteria then error will be returned to the front end, the consent will remain in status `AwaitingAuthorisation` and the PSU will not be redirected.

### `Account.SchemeName`
The only supported `Account.SchemeName` is `UK.OBIE.SortCodeAccountNumber` for both `DebtorAccount` and `CreditorAccount`. Any other enum provided will return error.

## Payment dates
Payments can be made on all days including Saturdays, Sundays and Bank Holidays

