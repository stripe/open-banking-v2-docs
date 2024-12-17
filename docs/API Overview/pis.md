# PISP API Overview

## Base URL
The base URL for all PIS APIs is: `https://rs1.api.stripeopenbanking.com/open-banking/v3.1/pisp/**`

## Supported Payment Types
The Stripe API currently only supports:
- Domestic Payments
- International payments 

The Stripe API __does not__ support:
- File & Bulk payments
- `payment-details` end-points
- Domestic Scheduled Payments
- Domestic Standing Orders

The payment request from TPP must have the creditor information.
## Overview

### The following apply to **all payment consents (domestic and international):**

### `InstructedAmount/Amount`
- There is no MAX `InstructedAmount/Amount` mandated by Stripe API.
-  Stripe suggest PISP notify the PSU that the same limits apply as in their Stripe app. It is possible from time to time that the payment consent is authorised, but the payment initiation fails due to account limits.

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

### `SupplementaryData` requirements
Stripe's Open Banking API requires you to send additional data in the SupplementaryData field to ensure a successful payment:
- `SupplementaryData.CreditorAccount.Email`: Must contain the recipient's email address
- `SupplementaryData.CreditorAccount.EntityType`: Must be either `individual` or `company` depending on the type of the recipient

**Required** if `SupplementaryData.CreditorAccount.EntityType` is `individual`:
-  `SupplementaryData.CreditorAccount.Individual.DateOfBirth`: The date of birth of the recipient in "YYYY-MM-DD" format

**Required** if `SupplementaryData.CreditorAccount.EntityType` is `company`:
-  `SupplementaryData.CreditorAccount.BusinessDetails.RegisteredName`: The registered name of the company

### The following apply to **all domestic payment consents:**

### `CreditorAccount` requirements

`CreditorAccount` supports either `UK.OBIE.SortCodeAccountNumber` or `UK.OBIE.Wallet` or `UK.OBIE.IBAN` (only for international payments to French recipients) for the `Account.SchemeName` parameter. Providing any other value will return an error.
- `UK.OBIE.Wallet`
  - `CreditorAccount.Identification`: Must be the recipient account ID (e.g. `acct_test_123`)
  - `SupplementaryData.CreditorAccount.Destination`: Optional if the recipient has a default destination, otherwise the destination ID (e.g. `usba_test_123`)
-  `UK.OBIE.SortCodeAccountNumber`
    - `CreditorAccount.Identification`: Must be the concatenated sort code and account number, totaling 14 digits
    - `CreditorAccount.Name`: Must be the full name (first & last) of the recipient
    - `SupplementaryData.CreditorAccount.Email`: Must contain the recipient's email address

### The following apply to **all international payment consents:**

### `CurrencyOfTransfer` requirements
`CurrencyOfTransfer` should always be a supported currency on the user's Stripe Financial Account. Users must hold money in that currency at Stripe to be able to send a payment. You may use the AIS APIs to list the user's balances.

### `DestinationCountryCode` requirements
`DestinationCountryCode` may only be `FR`. Stripe's Open Banking API supports international payments to France.

### `CreditorAccount` requirements

`CreditorAccount` supports only `UK.OBIE.IBAN` 
  - `CreditorAccount.Identification`: Must be the IBAN of the recipient
  - `CreditorAccount.Name`: Must be the full name (first & last) of the recipient
 
### `InstructedAmount/Currency` requirements
 
 `InstructedAmount/Currency` can be any currency. The user can specify the instructed amount in any currency and Stripe will do the required FX to extract money from the user's Stripe account and send the money in the recipient's destination currency (see note below).

### Note on recipient destination currency
Stripe only supports payments that land in the recipient's local currency. For instance, when sending a payment to a France based recipient, the payment will post to the user's bank account in Euros.



## Payment dates
Payments can be made on all days including Saturdays, Sundays and Bank Holidays

