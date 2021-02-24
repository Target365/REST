## ![Strex](https://github.com/Target365/sdk-for-php/raw/master/strex.png "Strex")
Strex AS is a Norwegian payment and SMS gateway (Strex Connect) provider. Strex withholds an e-money license and processes more than 70 million transactions every year. Strex has more than 4.2 mill customers in Norway and are owned by the Norwegian mobile network operators (Telenor, Telia and Ice). Strex Connect is based on the Target365 marketing and communication platform. 

## Target365 REST API

### Getting started
To get started, please click here: https://strex.no/strex-connect#Prispakker and register your organisation. 
To get your API key, log in to your account at StrexConnect at https://www.strexconnect.no. 
See our [REST User Guide](USERGUIDE.md) on how to authorize and use the API.

### Swagger
* Swagger for the API is located here: https://test.target365.io/api/swagger.json
    * https://generator.swagger.io/?url=https%3A%2F%2Ftest.target365.io%2Fapi%2Fswagger.json

### Test Environment
Our test-environment acts as a sandbox that simulates the real API as closely as possible. This can be used to get familiar with the service before going to production. Please be ware that the simulation isn't perfect and must not be taken to have 100% fidelity.

#### Url: https://test.target365.io/

### Production Environment
Our production environment is a mix of per-tenant isolated environments and a shared common environment. Contact <sdk@strex.no> if you're interested in an isolated per-tenant environment.

#### Url: https://shared.target365.io/

### Authors and maintainers
Target365 (<sdk@strex.no>)

### Issues / Bugs / Questions
Please feel free to raise an issue against this repository if you have any questions or problems.

### Contributing
New contributors to this project are welcome. If you are interested in contributing please
send an email to sdk@strex.no.

### License
This library is released under the MIT license.
