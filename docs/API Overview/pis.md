# PISP API Overview

## Base URL
The base URL for all PIS APIs is: `https://rs1.api-v2-eu.stripeopenbanking.com/open-banking/v3.1/pisp/**`

## Supported Payment Types
The Stripe API currently only supports:
- Domestic Payments
- International Payments

The Stripe API __does not__ support:

- File & Bulk payments
- `payment-details` end-points
- Domestic Scheduled Payments
- Domestic Standing Orders

The payment request from TPP must have the creditor information.

## The following apply to all payment consents (domestic and international):

### `InstructedAmount/Amount`

- There is no MAX `InstructedAmount/Amount` mandated by Stripe API.
- Stripe suggest PISP notify the PSU that the same limits apply as in their Stripe app. It is possible from time to time that the payment consent is authorised, but the payment initiation fails due to account limits.

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

## The following apply to all domestic payment consents:

### `CreditorAccount` requirements

`CreditorAccount.SchemeName` supports `UK.OBIE.Wallet`. Providing any other value will return an error.

-  `UK.OBIE.Wallet`
   -  `CreditorAccount.Identification`: Must be the Stripe recipient account ID (e.g. `acct_test_123`)
   -  `SupplementaryData.CreditorAccount.Destination`: Optional if the recipient has a default outbound destination, otherwise the destination ID (e.g. `frba_test_123`)

> Note: Please use international payment consents for IBAN domestic payments and set the `DestinationCountryCode` to the same country you are sending from 

## The following apply to all international payment consents:

### `CreditorAccount` requirements

`CreditorAccount.SchemeName` supports `UK.OBIE.IBAN` and `UK.OBIE.BBAN`. Providing any other value will return an error.

Please ensure to send all required additional `SupplementaryData` as mentioned in the SupplementaryData section below.
 
### `CurrencyOfTransfer` requirements

`CurrencyOfTransfer` should always be a supported currency on the user's Stripe Financial Account. This is the currency in which Stripe will debit the user's balance. Users must hold money in that currency at Stripe to be able to send a payment. You may use the AIS APIs to list the user's balances.

### `DestinationCountryCode` requirements

`DestinationCountryCode` should represent the country code of the country where the recipient is based. See the tables below for acceptable values for the DestinationCountryCode.

### `InstructedAmount/Currency` requirements

`InstructedAmount/Currency` can be any currency. The user can specify the instructed amount in any currency and Stripe will do the required FX to extract money from the user's Stripe account and send the money in the recipient's destination currency (see note below).

### Note on recipient destination currency

Stripe only supports payments that land in the recipient's local currency. For instance, when sending a payment to a France based recipient, the payment will post to the user's bank account in Euros.

## `SupplementaryData` requirements

Stripe's Open Banking API requires you to send additional data in the SupplementaryData field to ensure a successful payment. The full schema of SupplementaryData is included at the end of this document.

The following fields are necessary for all payments, except those made with the `UK.OBIE.Wallet` scheme with a pre-existing Stripe recipient account ID:
-  `SupplementaryData.CreditorAccount.Email`: Must contain the recipient's email address
-  `SupplementaryData.CreditorAccount.EntityType`: Must be either `individual` or `company` depending on the type of the recipient

**Note: SupplementaryData has additional optional fields that are required for a successful payment depending on the destination country:** 

### Additional required bank account information

The following table mentions the bank account details you must send about the creditor depending on the destination country and the scheme name. 

- `Country` refers to the country of the Creditor (`DestinationCountryCode`)
- `SchemeName` refers to `Initiation.CreditorAccount.SchemeName`
- `Identification` refers to `Initiation.CreditorAccount.Identification`
- `RoutingNumber` refers to `SupplementaryData.CreditorAccount.BankAccountDetails.RoutingNumber`
- `BranchNumber` refers to `SupplementaryData.CreditorAccount.BankAccountDetails.BranchNumber`
- `SwiftCode` refers to `SupplementaryData.CreditorAccount.BankAccountDetails.SwiftCode`

| Country | SchemeName   | Identification | RoutingNumber  | BranchNumber       | SwiftCode  |
| ------- | ------------ | -------------- | -------------- | ------------------ | ---------- |
| Eurozone| UK.OBIE.IBAN | IBAN           |                |                    |            |
| AL      | UK.OBIE.IBAN | IBAN           |                |                    | Swift Code |
| AM      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| AU      | UK.OBIE.BBAN | Account Number | BSB            |                    |            |
| BA      | UK.OBIE.IBAN | IBAN           |                |                    | Swift Code |
| BG      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| BJ      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| BN      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| BS      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| BW      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| CA      | UK.OBIE.BBAN | Account Number | Transit Number | Institution Number |            |
| CH      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| CI      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| CZ      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| DK      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| DZ      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| EC      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| ET      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| GY      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| HU      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| ID      | UK.OBIE.BBAN | Account Number | Bank Code      |                    |            |
| IL      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| IN      | UK.OBIE.BBAN | Account Number | IFSC Code      |                    |            |
| IS      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| JM      | UK.OBIE.BBAN | Account Number | Bank Code      | Branch Code        |            |
| JO      | UK.OBIE.IBAN | IBAN           |                |                    | Swift Code |
| KE      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| KW      | UK.OBIE.IBAN | IBAN           |                |                    | Swift Code |
| LI      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| LK      | UK.OBIE.BBAN | Account Number | Bank Code      | Branch Code        |            |
| MA      | UK.OBIE.IBAN | IBAN           |                |                    | Swift Code |
| MN      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| MU      | UK.OBIE.IBAN | IBAN           |                |                    | Swift Code |
| MX      | UK.OBIE.BBAN | CLABE          |                |                    |            |
| NA      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| NO      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| NZ      | UK.OBIE.BBAN | Account Number |                |                    |            |
| OM      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| PA      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| PH      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| PL      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| RO      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| RS      | UK.OBIE.IBAN | IBAN           |                |                    | Swift Code |
| SE      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| SG      | UK.OBIE.BBAN | Account Number | Bank Code      | Branch Code        |            |
| SI      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| SK      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| SN      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| SV      | UK.OBIE.IBAN | IBAN           |                |                    | Swift Code |
| TN      | UK.OBIE.IBAN | IBAN           |                |                    |            |
| TR      | UK.OBIE.IBAN | IBAN           |                |                    | Swift Code |
| TZ      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |
| US      | UK.OBIE.BBAN | Account Number | Routing Number |                    |            |
| ZA      | UK.OBIE.BBAN | Account Number |                |                    | Swift Code |

### Additional required information KYC information

The following table mentions the additional KYC data you must send about the creditor depending on the destination country and entity type.

- `Individual` refers to `SupplementaryData.CreditorAccount.Individual`
- `BusinessDetails` refers to `SupplementaryData.CreditorAccount.BusinessDetails`

| Country       | Required fields                              | Required if EntityType=individual | Required if EntityType=company      |
| ------------- | -------------------------------------------- | --------------------------------- | ----------------------------------- |
| Eurozone      | SupplementaryData.CreditorAccount.Email      | Individual.Address.CountryCode    | BusinessDetails.Address.CountryCode |
|               | SupplementaryData.CreditorAccount.EntityType |                                   |                                     |
| Non-Eurozone  | SupplementaryData.CreditorAccount.Email      | Individual.DateOfBirth            | BusinessDetails.Address             |
|               | SupplementaryData.CreditorAccount.EntityType | Individual.Address                |                                     |

## SupplementaryData Object

The SupplementaryData is an Optional object that contains additional information that Stripe requires in some cases. Please read the tables above to ensure you send all the required data for a successful payment. The shape of the object is defined below:

### SupplementaryData

Properties:
- **CreditorAccount** (object, required): Information about the creditor's account.

### SupplementaryData.CreditorAccount

Properties:
- **Email** (string, optional): The email address of the creditor.
  Example: "foo@bar.com"

- **Destination** (string, optional): Identifier for the recipient's Stripe OutboundDestination object.
  Example: "gbba_12135r2524542"

- **EntityType** (string, optional): The type of entity the creditor represents.
  Allowed values: "individual", "company"

- **Individual** (object, optional): Details about the individual creditor. Required if EntityType is "individual".

- **BusinessDetails** (object, optional): Details about the business. Required if EntityType is "company".

- **BankAccountDetails** (object, optional): Details about the bank account.

### SupplementaryData.CreditorAccount.Individual

Properties:
- **Name** (string, optional): The full name of the individual creditor.
  Example: "Jean Dupont"

- **DateOfBirth** (string, optional): The date of birth of the individual creditor in ISO 8601 format (YYYY-MM-DD).
  Example: "1990-05-15"

- **Address** (object, optional): The address of the individual.

### SupplementaryData.CreditorAccount.BusinessDetails

Properties:
- **RegisteredName** (string, optional): The registered name of the business.

- **Address** (object, optional): The address of the business.

### SupplementaryData.CreditorAccount.BankAccountDetails

Properties:
- **RoutingNumber** (string, optional): Refer to the table below for the expected value per country.

- **BranchNumber** (string, optional): Refer to the table below for the expected value per country.

- **SwiftCode** (string, optional): Refer to the table below for the expected value per country.

### SupplementaryData.CreditorAccount.Individual.Address and SupplementaryData.CreditorAccount.BusinessDetails.Address
If the Address is required, please provide all the properties you have in the below mentioned spec.

Properties:
- **CountryCode** (string, optional): The country code of the address.

- **PostalCode** (string, optional): The postal code of the address.

- **State** (string, optional): The state or province of the address.

- **City** (string, optional): The city of the address.

- **Town** (string, optional): The town of the address.

- **Line1** (string, optional): The first line of the address.

- **Line2** (string, optional): The second line of the address.

### Example payment request for an international payment to US
The following shows an example payment request to the US using the guidelines above:

- The `DestinationCountryCode` is "US"
- The `SchemeName` for the CreditorAccount is `UK.OBIE.BBAN` as mentioned in the Bank Account Details table
    - The Bank Account Number is set in `CreditorAccount.Identification`
    - The Bank Account Routing Number is set in `SupplementaryData.CreditorAccount.BankAccountDetails.RoutingNumber`
- The name of the creditor is set in `CreditorAccount.Name`
- Additional data about the creditor is set in `SupplementaryData.CreditorAccount`
    - Since `SupplementaryData.CreditorAccount.Email` and `SupplementaryData.CreditorAccount.EntityType` is needed for all payments, those properties are filled
    - According to the KYC table, `SupplementaryData.CreditorAccount.Individual.DateOfBirth` and `SupplementaryData.CreditorAccount.Individual.Address` is required for `individual` creditors based in the US. 

```json
{
  "Data": {
    "ConsentId": "consent_us12345",
    "Initiation": {
      "EndToEndIdentification": "e2e_id_us12345",
      "InstructionIdentification": "instr_id_us12345",
      "CreditorAccount": {
        "SchemeName": "UK.OBIE.BBAN",
        "Identification": "123456789",
        "Name": "John Smith"
      },
      "InstructedAmount": {
        "Amount": "1000.00",
        "Currency": "USD"
      },
      "CurrencyOfTransfer": "usd",
      "DestinationCountryCode": "US",
      "SupplementaryData": {
        "CreditorAccount": {
          "Email": "john.smith@example.com",
          "EntityType": "individual",
          "Individual": {
            "Name": "John Smith",
            "DateOfBirth": "1985-07-15",
            "Address": {
              "CountryCode": "US",
              "PostalCode": "10001",
              "State": "NY",
              "City": "New York",
              "Line1": "123 Broadway",
              "Line2": "Apt 4B"
            }
          },
          "BankAccountDetails": {
            "RoutingNumber": "111000025"
          }
        }
      }
    }
  }
}

