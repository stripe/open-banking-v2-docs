# Sandbox

Our API Sandbox contains a full simulation of our APIs but without connecting to any real customer accounts. Any developer can access this Sandbox using their own self signed certificates.

## Try our API in the Sandbox

We provide a regulatory sandbox that fully reflects our production APIs.

### Regulatory Sandbox

- Authorisation Server 1: Provides both strict and permissive client profiles in headless and non headless options.
- OIDC Well Known endpoint: https://auth1.{sandbox_domain_name}/.well-known/openid-configuration
- baseUrl: https://auth1.{sandbox_domain_name}/

### Step by Step guide to connect to sandbox

#### Step 1: Pre-Requisites for TPP
Ensure that the following pre-requisites are met before onboarding onto {bank_name}.
The TPP has registered on the Directory Sandbox - https://directory.openbanking.org.uk/s/login/
The TPP has at least one software statement created on the Directory Sandbox environment
The TPP has at least one transport certificate created for each of its software statements.
The TPP has at least one redirect URI for each of its software statements.
The TPP has a copy of the OB root and issuing certificate attached.

#### Step 2: TPP should contact our Open Banking support team
We do not offer Dynamic Client Registration.

Please contact our Open Banking support team to be onboarded.

#### Step 2: Register TPP with Bank using Dynamic Client Registration
See http://openid.net/specs/openid-connect-registration-1_0-21.html
Claims required in dynamic client registration for Banks are as follows

| Field Names | Example Values | Description |
|-------------|----------------|-------------|
| token_endpoint_auth_signing_alg |	PS256 | Signature algorithm used JWK.
| grant_types | authorization_code, client_credentials |
| subject_type | public | subject_type requested for responses to this client_id.
| application_type | web | The issuer must be your software ID. This is important as it's used verify it matches the SSA software ID claim.
| redirect_uris | | All redirect Uris should be added in the claims
| token_endpoint_auth_method | client_secret_basic | Requested authentication method for the Token Endpoint.
| aud | 0015800001ZEc3hAAD | The audience must match the AS issuer ID.
| scopes | openid, accounts, payments | The scopes will depend on your role from the FCA (AISP and/or PISP)
| request_object_signing_alg | none |
| exp | timestamp |
| iat | timestamp |
| jti |  |  UUID
| response_types | code, code id_token |  UUJSON array containing a list of the OAuth 2.0 response_type values that the Client is declaring that it will restrict itself to using
| id_token_signed_response_alg | PS256 |  JWS alg algorithm
| software_statement |  |  UUID

2.1 Example Registration Request JWT (Python)
This code is compatible with python3.6 and requires the jwcrypto module (pip install jwcrypto)

```
import time, uuid
from jwcrypto import jwt
from jwcrypto import jwk

# The software statement ID (software_id) of the software statement created in software statements (MIT).
SOFTWARE_STATEMENT_ID = ""
#  Value of the kid parameter associated with the signing certificate generated in Generate a
# transport/signing certificate pair (please note that you need to use the signing certificate kid).
KID = ""
# Your private signing key. You will use this to sign your JWT.
PRIVATE_RSA_KEY = """
-----BEGIN PRIVATE KEY-----
-----END PRIVATE KEY-----
"""

SOFTWARE_STATEMENT = ""

def make_registration_jwt(software_statement_id: str, kid: str, software_statement: str) -> str:
    jwt_iat = int(time.time())
    jwt_exp = jwt_iat + 3600
    header = dict(alg='RS256', kid=kid, typ='JWT')
    claims = dict(
        token_endpoint_auth_signing_alg="PS256",
        grant_types=["authorization_code", "client_credentials"],
        subject_type="public",
        application_type="web",
        iss=software_statement_id,
        redirect_uris=["https://app.getpostman.com/oauth2/callback"],
        token_endpoint_auth_method="client_secret_basic",
        aud="0015800001ZEc3hAAD",
        scope= "openid accounts payments",   #accounts for AISP or payments for PISP or both
        request_object_signing_alg="none",
        exp=jwt_exp,
        iat=jwt_iat,
        jti=str(uuid.uuid4()),
        response_types=["code", "code id_token"],
        id_token_signed_response_alg="RS256",
        software_statement=software_statement
     )

    token = jwt.JWT(header=header, claims=claims)
    key_obj = jwk.JWK.from_pem(PRIVATE_RSA_KEY.encode('latin-1'))
    token.make_signed_token(key_obj)
    signed_token = token.serialize()

    print(signed_token)
    return signed_token

make_registration_jwt(SOFTWARE_STATEMENT_ID, KID, SOFTWARE_STATEMENT)
```

2.2 Request Registration Endpoint Example (Python):

```
headers = {'Content-Type': 'application/jwt'}
client = ('./transport.pem', './transports.key')
response = requests.post("https://ob-api1.{sandbox_domain_name}/dynamic-client-registration/v3.1/register",
                                registration_request,
                                headers=headers,
                                verify=False,
                                cert=client
                                )
print(response.content)
```
The response you get from this registration is exactly the same as the one described by the OIDC dynamic registration.
You should now have successfully onboarded your TPP with the ASPSP and received a client ID; you will need this client ID for future interactions with the ASPSP.

2.3 Environment File download

Once a TPP successfully registers to {bank_name}, they can download the environment file for their client via an {bank_name} helper api

```
Endpoint: https://ob-api1.{sandbox_domain_name}/ozone/v1.0/postman/environment

Headers: Authorization: Basic <token>
The token is the base64 encoded string of clientId:clientsecret
```

On successful response the TPP can then save the environment file on to their local system.

#### Step 3: Import Environment Files and Collections To Postman
3.1. Import Environment Files and Collections into Postman
![postmanimage](/assets/images/postmanimport1.png)

![](/assets/images/postmanimport2.png)

3.2) Check URLs and Environments are loaded successfully

3.3) Add Client Certificates
Add the following ASPSP end points into Postman;
https://rs1.{sandbox_domain_name}
https://auth1.{sandbox_domain_name}/token

The CRT should be set to the transport certificate downloaded from the open banking directory. The Key value should be set to the private key for the transport certificate.


3.4) SSL Certificate Verification (TPP)
In Postman settings ensure SSL Certificate Verification is set to off.

#### Step 4: Account Information Flow

4.1) Client Credentials Grant

![](/assets/images/ccg.png)

4.2) Account Access Consent

![](/assets/images/accountaccess.png)

4.3) PSU Consent Flow
TPPs can generate the consent flow URL by postman

![](/assets/images/consent1.png)

Once the URL is constructed, open the URL to initiate the PSU consent flow

Authenticate user

![](/assets/images/consent2.png)

Sandbox User Accounts

| user | password |
|------|----------|
| rora |	rora.   |
| mits |	mits.   |
| ivsa |	ivsa.   |

Select accounts

![](/assets/images/consent3.png)

Once the PSU consent is successful, {bank_name} will redirect back to the redirect URI. Copy the Authcode from the URL

![](/assets/images/consent4.png)

4.4) Generate the access token

![](/assets/images/accesstoken.png)

#### Step 5: Retrieve Account and Transaction Data
Retrieve Account Data

![](/assets/images/accounts.jpeg)

![](/assets/images/transactions.jpeg)
