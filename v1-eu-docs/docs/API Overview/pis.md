# PISP API Overview

## Base URL
The base URL for all PIS APIs is: `https://rs1.api-v1-eu.stripeopenbanking.com/open-banking/v3.1/pisp/**`

## Supported Payment Types
The Stripe API currently only supports:
- Domestic Payments

The Stripe API __does not__ support:
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

### `CreditorAccount` requirements

`CreditorAccount` supports `UK.OBIE.Wallet` for the `Account.SchemeName` parameter. Providing any other value will return an error.
- `UK.OBIE.Wallet`
  - `CreditorAccount.Identification`: Must be the recipient account ID (e.g. `acct_test_123`)
  - `SupplementaryData.CreditorAccount.Destination`: Optional if the payout recipient has a default destination, otherwise the destination ID (e.g `ba_test_1MtIhL2eZvKYlo2CAElKwKu2`)

## Payment dates
Payments can be made on all days including Saturdays, Sundays and Bank Holidays