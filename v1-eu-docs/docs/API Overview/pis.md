# PISP API Overview

## Base URL
The base URL for all PIS APIs is: `https://rs1.api-v1-eu.stripeopenbanking.com/open-banking/v3.1/pisp/**`

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

### `CreditorAccount` and `DebtorAccount` requirements

`CreditorAccount` supports either `UK.OBIE.SortCodeAccountNumber` or `UK.OBIE.Wallet` for the `Account.SchemeName` parameter. Providing any other value will return an error.
- `UK.OBIE.Wallet`
  - Identification: Must be the recipient account ID (e.g. `acct_test_123`)
  - SecondaryIdentification: Optional if the recipient has a default destination, otherwise the destination ID (e.g. `usba_test_123`)
-  `UK.OBIE.SortCodeAccountNumber`
  - Identification: Must be the concatenated sort code and account number, totaling 14 digits
  - SecondaryIdentification: Must contain the recipient's email address
  - Name: Must be the full name (first & last) of the recipient

The only supported `Account.SchemeName` is `UK.OBIE.Wallet` for `DebtorAccount`. When `DebtorAccount` is passed, the `Identification` must match the Financial Account ID belonging to the PSU.

## Payment dates
Payments can be made on all days including Saturdays, Sundays and Bank Holidays

