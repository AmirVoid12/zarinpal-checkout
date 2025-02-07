# ZarinPal Checkout [![Build Status](https://travis-ci.org/siamak/zarinpal-checkout.svg?branch=master)](https://travis-ci.org/siamak/zarinpal-checkout)

Modern and robust implementation of [ZarinPal Payment Gateway](https://www.zarinpal.com/) for Node.js applications.

## ✨ Key Features

- Enterprise-grade Payment Processing
- Full TypeScript Support
- Promise-based Architecture
- Comprehensive Error Handling
- Sandbox Environment for Testing
- Detailed Transaction Reports
- Automatic Currency Conversion
- Request Rate Limiting
- Connection Pooling

## 🚀 Installation

Choose your preferred package manager to install:

```bash
# Using npm
npm install zarinpal-checkout

# Using yarn
yarn add zarinpal-checkout

# Using pnpm
pnpm add zarinpal-checkout
```

## 📦 TypeScript Support

Install TypeScript definitions:

```bash
# Using npm
npm install @types/zarinpal-checkout

# Using yarn
yarn add @types/zarinpal-checkout
```

## 🔧 Basic Setup

```javascript
const ZarinpalCheckout = require('zarinpal-checkout');
// or ES6 import
import ZarinPalCheckout from 'zarinpal-checkout';

/**
 * Initialize ZarinPal Gateway
 * @param {String} merchantId - Your unique merchant identifier
 * @param {Boolean} sandbox - Enable sandbox mode for testing
 * @param {String} currency - Currency type (IRR/IRT)
 */
const zarinpal = ZarinpalCheckout.create(
    'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx',
    false,
    'IRT'
);
```

## 💳 Payment Operations

### Payment Request

```javascript
const paymentRequest = async () => {
    try {
        const response = await zarinpal.PaymentRequest({
            Amount: '1000',                // Amount in Tomans
            CallbackURL: 'https://your-api.com/verify',
            Description: 'Payment for Order #1234',
            Email: 'customer@example.com',
            Mobile: '09120000000',
            Metadata: {                    // Optional metadata
                orderId: '1234',
                customerId: 'cust_123'
            }
        });

        if (response.status === 100) {
            return response.url;           // Redirect user to this URL
        }
        
        throw new Error('Payment initiation failed');
    } catch (error) {
        console.error('Payment Request Error:', error);
        throw error;
    }
};
```

### Payment Verification

```javascript
const verifyPayment = async () => {
    try {
        const response = await zarinpal.PaymentVerification({
            Amount: '1000',              // Amount in Tomans
            Authority: 'A00000000000000000000000000000000',
        });

        if (response.status === 100) {
            console.log(`Payment Verified! Reference ID: ${response.RefID}`);
            return response.RefID;
        }
        
        throw new Error(`Verification failed with status: ${response.status}`);
    } catch (error) {
        console.error('Verification Error:', error);
        throw error;
    }
};
```

### Unverified Transactions

```javascript
const checkUnverified = async () => {
    try {
        const response = await zarinpal.UnverifiedTransactions();
        
        if (response.status === 100) {
            return response.authorities.map(auth => ({
                authority: auth.authority,
                amount: auth.amount,
                channel: auth.channel,
                date: new Date(auth.date),
                callbackURL: auth.callbackURL
            }));
        }
        
        return [];
    } catch (error) {
        console.error('Unverified Transactions Error:', error);
        throw error;
    }
};
```

### Refresh Authority

```javascript
const refreshAuthority = async () => {
    try {
        const response = await zarinpal.RefreshAuthority({
            Authority: 'A00000000000000000000000000000000',
            Expire: '1800'               // Expiration time in seconds
        });

        if (response.status === 100) {
            return true;
        }
        
        throw new Error('Authority refresh failed');
    } catch (error) {
        console.error('Refresh Authority Error:', error);
        throw error;
    }
};
```

## 🔄 Error Handling

```javascript
const handlePaymentErrors = (error) => {
    switch (error.code) {
        case -1:
            return 'Information submitted is incomplete.';
        case -2:
            return 'Merchant ID or IP is not authorized.';
        case -3:
            return 'Amount should be above 100 Tomans.';
        case -4:
            return 'Approved level of service provider is lower than silver.';
        case -11:
            return 'Request not found.';
        case -21:
            return 'Financial operations for this transaction was not found.';
        case -22:
            return 'Transaction unsuccessful.';
        case -33:
            return 'Transaction amount does not match.';
        case -34:
            return 'Transaction limit exceeded.';
        case -40:
            return 'Invalid session.';
        case -41:
            return 'Payment request information does not match.';
        case -54:
            return 'Request archived.';
        default:
            return 'An unknown error occurred.';
    }
};
```

## 📊 Advanced Usage

### Custom Headers

```javascript
zarinpal.setHeaders({
    'User-Agent': 'CustomApp/1.0.0',
    'Accept-Language': 'fa-IR'
});
```

### Timeout Configuration

```javascript
zarinpal.setTimeout(5000); // 5 seconds
```

## 🛠 Development

```bash
# Install dependencies
npm install

# Run tests
npm test

# Run linter
npm run lint

# Build TypeScript
npm run build
```

## 📝 Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

MIT License

Copyright (c) 2025 Amir

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
