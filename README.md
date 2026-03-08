# 💳 M-Pesa Payment Gateway - NestJS

> ⚠️ **Important Notice**: The official Daraja API documentation has recently changed significantly. Some links and functionality may be outdated. Looking for contributors/maintainers who can help keep everything updated.

## 🚀 About

A comprehensive **M-Pesa Daraja API** integration built with **NestJS** for seamless mobile payment processing in Kenya.

💡 This implementation provides a robust backend solution for handling M-Pesa payments, transaction queries, and account management.

---

## ✨ Available API Methods

- 🔄 **B2B** - Business to Business (DEPRECATED)
- 💰 **C2B** - Customer to Business
- 💸 **B2C** - Business to Customer
- 🔍 **Transaction Status** - Check payment status
- 💼 **Account Balance** - Query account balance
- ↩️ **Reversal** - Reverse transactions
- 📱 **STK Push** - Lipa Na M-Pesa Online
- ❓ **STK Query** - Query STK Push status

---

## 📋 Prerequisites

- 🟢 **Node.js** 6+ (14+ recommended)
- 📦 **NPM** (comes with Node) or **Yarn**
- 🏗️ **NestJS** framework
- 🔐 Safaricom Developer account

---

## 📦 Installation

### Installing M-Pesa API Package

```bash
# Using NPM
npm install mpesa-api

# Using Yarn
yarn add mpesa-api

# Install NestJS dependencies
npm install @nestjs/common @nestjs/core @nestjs/config
```

---

## 🔑 Requisites

### What You'll Need from Safaricom:

- 🔑 **Consumer Key**
- 🔐 **Consumer Secret**
- 🧪 **Test Credentials** (for Sandbox environment)
- 🌐 **Callback Server** (with M-Pesa IPs whitelisted)

### 📝 Setup Steps:

1. 👤 [Login or Register](https://developer.safaricom.co.ke/) as a Safaricom developer
2. ➕ [Create a new App](https://developer.safaricom.co.ke/MyApps)
3. 📋 Copy your **Consumer Key** and **Consumer Secret**
4. 🧪 Obtain [Test Credentials](https://developer.safaricom.co.ke/test_credentials) for sandbox
5. ⚠️ Note: Test credentials are only valid in **Sandbox/Development** environment
6. 🚀 To go live, follow the [Going Live Guide](https://developer.safaricom.co.ke/docs#going-live)

---

## 🏁 Getting Started

### 🔧 Basic Setup

```typescript
// Import the package
import { Mpesa } from "mpesa-api";
// OR using CommonJS
const Mpesa = require("mpesa-api").Mpesa;

// Create a new instance
const mpesa = new Mpesa(credentials, environment);
```

### 🎯 NestJS Integration Example

```typescript
// mpesa.service.ts
import { Injectable } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import { Mpesa } from 'mpesa-api';

@Injectable()
export class MpesaService {
  private mpesa: Mpesa;

  constructor(private configService: ConfigService) {
    const credentials = {
      clientKey: this.configService.get('MPESA_CONSUMER_KEY'),
      clientSecret: this.configService.get('MPESA_CONSUMER_SECRET'),
      initiatorPassword: this.configService.get('MPESA_INITIATOR_PASSWORD'),
      securityCredential: this.configService.get('MPESA_SECURITY_CREDENTIAL'),
      certificatePath: null // Using security credential instead
    };
    
    const environment = this.configService.get('MPESA_ENVIRONMENT') || 'sandbox';
    this.mpesa = new Mpesa(credentials, environment);
  }

  async initiateSTKPush(phoneNumber: string, amount: number, accountRef: string) {
    return await this.mpesa.lipaNaMpesaOnline({
      BusinessShortCode: this.configService.get('MPESA_SHORTCODE'),
      Amount: amount,
      PartyA: phoneNumber,
      PhoneNumber: phoneNumber,
      CallBackURL: this.configService.get('MPESA_CALLBACK_URL'),
      AccountReference: accountRef,
      passKey: this.configService.get('MPESA_PASSKEY'),
      TransactionDesc: 'Payment for services'
    });
  }
}
```

### 🔑 Credentials Configuration

The `credentials` object should contain the following properties:

```typescript
const credentials = {
    clientKey: 'YOUR_CONSUMER_KEY_HERE',
    clientSecret: 'YOUR_CONSUMER_SECRET_HERE',
    initiatorPassword: 'YOUR_INITIATOR_PASSWORD_HERE',
    securityCredential: 'YOUR_SECURITY_CREDENTIAL', // Optional
    certificatePath: 'keys/example.cert' // Optional
};
```

#### 📝 Important Notes:

- 🔐 **initiatorPassword**: Use the security credential from the [test credentials page](https://developer.safaricom.co.ke/test_credentials)
- 🛡️ **securityCredential** (Optional): Set this if you're getting "Initiator Name is invalid" errors
- 📜 **certificatePath** (Optional): Default certificates are provided for sandbox and production. Pass `null` if using `securityCredential`

```typescript
// If using securityCredential
const credentials = {
    // ... other fields
    certificatePath: null
};
```

### 🌍 Environment Configuration

```typescript
const environment = "sandbox";  // For testing
// OR
const environment = "production";  // For live transactions
```

#### 💡 Environment Setup (.env file)

```env
MPESA_CONSUMER_KEY=your_consumer_key
MPESA_CONSUMER_SECRET=your_consumer_secret
MPESA_INITIATOR_PASSWORD=your_initiator_password
MPESA_SECURITY_CREDENTIAL=your_security_credential
MPESA_ENVIRONMENT=sandbox
MPESA_SHORTCODE=174379
MPESA_PASSKEY=your_passkey
MPESA_CALLBACK_URL=https://yourdomain.com/api/mpesa/callback
```

---

## 🔌 API Methods & Usage

### 🏢 Business to Business (B2B)

**This Has Been Disabled as of January 2019 and I have therefore removed it for now.**

This API enables Business to Business (B2B) transactions between a business and another business. Use of this API requires a valid and verified B2B M-Pesa short code for the business initiating the transaction and the both businesses involved in the transaction.

```javascript
mpesa
  .b2b({
    InitiatorName: "Initiator Name",
    Amount: 1000 /* 1000 is an example amount */,
    PartyA: "Party A",
    PartyB: "Party B",
    AccountReference: "Account Reference",
    QueueTimeOutURL: "Queue Timeout URL",
    ResultURL: "Result URL",
    CommandID: "Command ID" /* OPTIONAL */,
    SenderIdentifierType: 4 /* OPTIONAL */,
    RecieverIdentifierType: 4 /* OPTIONAL */,
    Remarks: "Remarks" /* OPTIONAL */,
  })
  .then((response) => {
    //Do something with the response
    //eg
    console.log(response);
  })
  .catch((error) => {
    //Do something with the error;
    //eg
    console.error(error);
  });
```

- **Initiator** - This is the credential/username used to authenticate the transaction request.
- **CommandID** - Unique command for each transaction type, default is `MerchantToMerchantTransfer` possible values are: `BusinessPayBill`, `MerchantToMerchantTransfer`, `MerchantTransferFromMerchantToWorking`, `MerchantServicesMMFAccountTransfer`, `AgencyFloatAdvance`
- **Amount** - The amount being transacted.
- **PartyA** - Organization's short code initiating the transaction.
- **SenderIdentifier** - Type of organization sending the transaction. Default is 4
- **PartyB** - Organization's short code receiving the funds being transacted.
- **RecieverIdentifierType** - Type of organization receiving the funds being transacted. Default is 4
- **Remarks** - Comments that are sent along with the transaction.
- **QueueTimeOutURL** - The path that stores information of time out transactions. It should be properly validated to make sure that it contains the port, URI and domain name or publicly available IP.
- **ResultURL** - The path that receives results from M-Pesa. It should be properly validated to make sure that it contains the port, URI and domain name or publicly available IP.
- **AccountReference** - Account Reference mandatory for "BusinessPaybill" CommandID.

### 💸 Business to Customer (B2C)

This API enables Business to Customer (B2C) transactions between a company and customers who are the end-users of its products or services. Use of this API requires a valid and verified B2C M-Pesa Short code.

```javascript
mpesa
  .b2c({
    Initiator: "Initiator Name",
    Amount: 1000 /* 1000 is an example amount */,
    PartyA: "Party A",
    PartyB: "Party B",
    QueueTimeOutURL: "Queue Timeout URL",
    ResultURL: "Result URL",
    CommandID: "Command ID" /* OPTIONAL */,
    Occasion: "Occasion" /* OPTIONAL */,
    Remarks: "Remarks" /* OPTIONAL */,
  })
  .then((response) => {
    //Do something with the response
    //eg
    console.log(response);
  })
  .catch((error) => {
    //Do something with the error;
    //eg
    console.error(error);
  });
```

- **Initiator** - This is the credential/username used to authenticate the transaction request.
- **CommandID** - Unique command for each transaction type e.g. `SalaryPayment`, `BusinessPayment`, `PromotionPayment`
- **Amount** - The amount being transacted
- **PartyA** - Organization's shortcode initiating the transaction.
- **PartyB** - Phone number receiving the transaction
- **Remarks** - Comments that are sent along with the transaction.
- **QueueTimeOutURL** - The timeout end-point that receives a timeout response.
- **ResultURL** - The end-point that receives the response of the transaction
- **Occasion** - Optional

### 💰 Customer to Business (C2B)

This API enables Paybill and Buy Goods merchants to integrate to M-Pesa and receive real-time payment notifications.

#### 📝 Register URL

The C2B Register URL API registers the 3rd party's confirmation and validation URLs to M-Pesa; which then maps these URLs to the 3rd party shortcode. Whenever M-Pesa receives a transaction on the shortcode, M-Pesa triggers a validation request against the validation URL and the 3rd party system responds to M-Pesa with a validation response (either a success or an error code).

M-Pesa completes or cancels the transaction depending on the validation response it receives from the 3rd party system. A confirmation request of the transaction is then sent by M-Pesa through the confirmation URL back to the 3rd party which then should respond with a success acknowledging the confirmation.

```javascript
mpesa
  .c2bregister({
    ShortCode: "Short Code",
    ConfirmationURL: "Confirmation URL",
    ValidationURL: "Validation URL",
    ResponseType: "Response Type",
  })
  .then((response) => {
    //Do something with the response
    //eg
    console.log(response);
  })
  .catch((error) => {
    //Do something with the error;
    //eg
    console.error(error);
  });
```

- **ShortCode** - The short code of the organization.
- **ResponseType** - Default response type for timeout.
- **ConfirmationURL** - Confirmation URL for the client.
- **ValidationURL** - Validation URL for the client.

#### 🎮 Simulate Transaction

```javascript
mpesa
  .c2bsimulate({
    ShortCode: 123456,
    Amount: 1000 /* 1000 is an example amount */,
    Msisdn: 254792123456,
    CommandID: "Command ID" /* OPTIONAL */,
    BillRefNumber: "Bill Reference Number" /* OPTIONAL */,
  })
  .then((response) => {
    //Do something with the response
    //eg
    console.log(response);
  })
  .catch((error) => {
    //Do something with the error;
    //eg
    console.error(error);
  });
```

- **ShortCode** - 6 digit M-Pesa Till Number or PayBill Number
- **CommandID** - Unique command for each transaction type. Default is `CustomerPayBillOnline`
- **Amount** - The amount been transacted.
- **MSISDN** - MSISDN (phone number) sending the transaction, start with country code without the plus(+) sign.
- **BillRefNumber** - Bill Reference Number (Optional).

### 💼 Account Balance

The Account Balance API requests for the account balance of a shortcode.

```javascript
mpesa
  .accountBalance({
    Initiator: "Initiator Name",
    PartyA: "Party A",
    IdentifierType: "Identifier Type",
    QueueTimeOutURL: "Queue Timeout URL",
    ResultURL: "Result URL",
    CommandID: "Command ID" /* OPTIONAL */,
    Remarks: "Remarks" /* OPTIONAL */,
  })
  .then((response) => {
    //Do something with the response
    //eg
    console.log(response);
  })
  .catch((error) => {
    //Do something with the error;
    //eg
    console.error(error);
  });
```

- **Initiator** - This is the credential/username used to authenticate the transaction request.
- **CommandID** - A unique command passed to the M-Pesa system. Default is `AccountBalance`
- **PartyB** - The shortcode of the organisation receiving the transaction.
- **ReceiverIdentifierType** - Type of the organisation receiving the transaction.
- **Remarks** - Comments that are sent along with the transaction.
- **QueueTimeOutURL** - The timeout end-point that receives a timeout message.
- **ResultURL** - The end-point that receives a successful transaction.

### 🔍 Transaction Status

Transaction Status API checks the status of a B2B, B2C and C2B APIs transactions.

```javascript
mpesa
  .transactionStatus({
    Initiator: "Initiator",
    TransactionID: "Transaction ID",
    PartyA: "Party A",
    IdentifierType: "Identifier Type",
    ResultURL: "Result URL",
    QueueTimeOutURL: "Queue Timeout URL",
    CommandID: "Command ID" /* OPTIONAL */,
    Remarks: "Remarks" /* OPTIONAL */,
    Occasion: "Occasion" /* OPTIONAL */,
  })
  .then((response) => {
    //Do something with the response
    //eg
    console.log(response);
  })
  .catch((error) => {
    //Do something with the error;
    //eg
    console.error(error);
  });
```

- **Initiator** - The name of Initiator to initiating the request.
- **CommandID** - Unique command for each transaction type, possible values are: `TransactionStatusQuery`.
- **TransactionID** - Organization Receiving the funds.
- **Party A** - Organization/MSISDN sending the transaction.
- **IdentifierType** - Type of organization receiving the transaction.
- **ResultURL** - The path that stores information of transaction.
- **QueueTimeOutURL** - The path that stores information of time out transaction.
- **Remarks** - Comments that are sent along with the transaction.
- **Occasion** - Optional.

### 📱 Lipa Na M-Pesa Online (STK Push)

Initiate M-Pesa transactions on behalf of customers using **STK Push**. This is the same technology used by the mySafaricom App for seamless payments.

```javascript
mpesa
  .lipaNaMpesaOnline({
    BusinessShortCode: 123456,
    Amount: 1000 /* 1000 is an example amount */,
    PartyA: "Party A",
    PhoneNumber: "Phone Number",
    CallBackURL: "CallBack URL",
    AccountReference: "Account Reference",
    passKey: "Lipa Na Mpesa Pass Key",
    TransactionType: "Transaction Type" /* OPTIONAL */,
    TransactionDesc: "Transaction Description" /* OPTIONAL */,
  })
  .then((response) => {
    //Do something with the response
    //eg
    console.log(response);
  })
  .catch((error) => {
    //Do something with the error;
    //eg
    console.error(error);
  });
```

- **BusinessShortCode** - The organization shortcode used to receive the transaction.
- **Amount** - The amount to be transacted.
- **PartyA** - The MSISDN sending the funds.
- **PartyB** - The organization shortcode receiving the funds. Default is the BusinessShorCode.
- **PhoneNumber** - The MSISDN sending the funds.
- **CallBackURL** - The url to where responses from M-Pesa will be sent to.
- **AccountReference** - Used with M-Pesa PayBills.
- **TransactionDesc** - A description of the transaction.
- **passKey** - Lipa Na Mpesa Pass Key.
- **Transaction Type** - Default is `CustomerPayBillOnline`

### ❓ STK Push Query

```javascript
mpesa
  .lipaNaMpesaQuery({
    BusinessShortCode: 123456,
    CheckoutRequestID: "Checkout Request ID",
    passKey: "Lipa Na Mpesa Pass Key",
  })
  .then((response) => {
    //Do something with the response
    //eg
    console.log(response);
  })
  .catch((error) => {
    //Do something with the error;
    //eg
    console.error(error);
  });
```

- **BusinessShortCode** - Business Short Code
- **CheckoutRequestID** - Checkout RequestID
- **passKey** - Lipa Na Mpesa Pass Key

### ↩️ Transaction Reversal

Reverse any B2B, B2C, or C2B M-Pesa transaction.

```javascript
mpesa
  .reversal({
    Initiator: "Initiator",
    TransactionID: "Transaction ID",
    Amount: 1000 /* 1000 is an example amount */,
    ReceiverParty: "Reciever Party",
    ResultURL: "Result URL",
    QueueTimeOutURL: "Queue Timeout URL",
    CommandID: "Command ID" /* OPTIONAL */,
    RecieverIdentifierType: 11 /* OPTIONAL */,
    Remarks: "Remarks" /* OPTIONAL */,
    Occasion: "Ocassion" /* OPTIONAL */,
  })
  .then((response) => {
    //Do something with the response
    //eg
    console.log(response);
  })
  .catch((error) => {
    //Do something with the error;
    //eg
    console.error(error);
  });
```

- **Initiator** - This is the credential/username used to authenticate the transaction request.
- **TransactionID** - Organization Receiving the funds.
- **Amount** - The Amount To Be Reversed
- **PartyA** - Organization/MSISDN sending the transaction.
- **RecieverIdentifierType** - Type of organization receiving the transaction. Default is 11
- **ResultURL** - The path that stores information of transaction.
- **QueueTimeOutURL** - The path that stores information of time out transaction.
- **Remarks** - Comments that are sent along with the transaction.
- **Occasion** - Optional.
- **Command ID** - Default is `TransactionReversal`

---

## 🛡️ IP Whitelisting

⚠️ **Important**: Whitelist M-Pesa IPs on your server/firewall to receive callbacks.

🔗 [View IP Whitelist](https://developer.safaricom.co.ke/APIs)

---

## 🎯 Demo

You can try it out on [Runkit](https://runkit.com/newtonmunene/mpesa-api)

---

## 🗺️ Roadmap

- ✅ Basic Documentation
- ✅ Deploy to NPM
- ✅ Migrate to TypeScript
- ✅ Detailed Documentation
- ✅ NestJS Integration
- ⏳ Write Comprehensive Tests
- ⏳ Input Validators
- ⏳ Tree Shaking
- ⏳ Performance Optimization

---

## 🛠️ Build From Source

If you wish to build from source:

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/payment-gateway.git

# 2. Navigate to directory
cd payment-gateway

# 3. Install dependencies
npm install

# 4. Build the project
npm run build

# 5. Run in development mode
npm run start:dev
```

---

## 🤝 Contributing

We welcome contributions! Here's how:

1. 🍴 **Fork** the project
2. 🌿 Create a feature branch: `git checkout -b feature/amazing-feature`
3. ✍️ Make your changes and add your name to Contributors
4. 💾 Commit: `git commit -m 'Add amazing feature'`
5. 🚀 Push: `git push origin feature/amazing-feature`
6. 🎉 Submit a **Pull Request**

---

## 👏 Credits

| Name           | Role               |
|----------------|--------------------|  
| Newton Munene  | 💻 Core Contributor |
| Nelson Bwogora | 💻 Core Contributor |

---

## 📄 License

MIT License

Copyright (c) 2018 Newton Munene

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

---

## 📚 About This Project

A powerful **NestJS** module for seamless **M-Pesa Daraja API** integration, enabling secure and efficient mobile payment processing.

### 🏷️ Topics
`money` • `online-payments` • `mpesa` • `mpesa-api` • `safaricom` • `nestjs` • `payment-gateway` • `daraja-api` • `stk-push` • `mobile-payments` • `kenya` • `fintech`

---

<div align="center">

**⭐ Star this repo if you find it helpful!**

Made with ❤️ for the developer community

</div>
