



1. **Get a User JWT Token** (for authenticated endpoints):
   - Register a user:
     ```bash
     curl -X POST "https://smsbackend-iota.vercel.app/api/auth/register" \
     -H "Content-Type: application/json" \
     -d '{"email": "test@example.com", "password": "test123"}'
     ```
     - Response: `{ "token": "<user_token>" }`
     - Save `<user_token>` for endpoints requiring `Authorization: Bearer <user_token>`.

2. **Get an Admin JWT Token** (for admin endpoints):
   - Run `node seedAdmin.js` to create an admin:
     ```bash
     node seedAdmin.js
     ```
   - Login as admin:
     ```bash
     curl -X POST "https://smsbackend-iota.vercel.app/api/auth/login" \
     -H "Content-Type: application/json" \
     -d '{"email": "admin@example.com", "password": "admin123"}'
     ```
     - Response: `{ "token": "<admin_token>" }`
     - Save `<admin_token>` for admin endpoints.

3. **Dynamic Values**:
   - `<paymentId>`: Obtain from a payment response (e.g., Stripe `pay_xxx` from `/payments/initiate`).
   - `<promoId>`: Obtain from `POST /api/promo` response.
  


---

### Endpoints and cURL Commands

#### 1. Authentication (2 Endpoints)

- **POST /auth/register**
  - **Description**: Registers a new user.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/auth/register" \
  -H "Content-Type: application/json" \
  -d '{"email": "test2@example.com", "password": "test123"}'
  ```

- **POST /auth/login**
  - **Description**: Logs in a user.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com", "password": "test123"}'
  ```

#### 2. Numbers (5 Endpoints)

- **GET /numbers/available**
  - **Description**: Lists available numbers by country. You can get country specific numbers by just adding the countrys symbol
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/available?country=US"
  ```
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/available?country=GB"
  ```
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/available?country=CA"
  ```
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/available?country=JP"
  ```
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/available?country=BR"
  ```

- **GET /numbers/available/:country**
  - **Description**: Lists numbers for a specific country (e.g., US).
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/available/US"
  ```

- **POST /numbers/buy** (Authenticated)
  - **Description**: Buys a number using wallet or gateway.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/numbers/buy" \
  -H "Authorization: Bearer <user_token>" \
  -H "Content-Type: application/json" \
  -d '{"country": "US", "paymentMethod": "wallet"}'
  ```
  - **Note**: For Stripe, use:
    ```bash
    curl -X POST "https://smsbackend-iota.vercel.app/api/numbers/buy" \
    -H "Authorization: Bearer <user_token>" \
    -H "Content-Type: application/json" \
    -d '{"country": "US", "paymentMethod": "stripe", "paymentDetails": {"paymentMethodId": "pm_test"}}'
    ```

- **POST /numbers/rent** (Authenticated)
  - **Description**: Rents a number using wallet or gateway.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/numbers/rent" \
  -H "Authorization: Bearer <user_token>" \
  -H "Content-Type: application/json" \
  -d '{"country": "US", "duration": 7, "paymentMethod": "wallet"}'
  ```

- **GET /numbers/my-numbers** (Authenticated)
  - **Description**: Lists user’s numbers with messages.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/my-numbers" \
  -H "Authorization: Bearer <user_token>"
  ```

#### 3. Wallet (2 Endpoints)

- **POST /wallet/deposit** (Authenticated)
  - **Description**: Deposits funds into wallet.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/wallet/deposit" \
  -H "Authorization: Bearer <user_token>" \
  -H "Content-Type: application/json" \
  -d '{"amount": 20, "paymentGateway": "stripe", "paymentDetails": {"paymentMethodId": "pm_test"}}'
  ```

- **GET /wallet/balance** (Authenticated)
  - **Description**: Gets wallet balance.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/wallet/balance" \
  -H "Authorization: Bearer <user_token>"
  ```

#### 4. Payments (3 Endpoints)

- **GET /payments/gateways**
  - **Description**: Lists available payment gateways.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/payments/gateways"
  ```

- **POST /payments/initiate** (Authenticated)
  - **Description**: Initiates a standalone payment.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/payments/initiate" \
  -H "Authorization: Bearer <user_token>" \
  -H "Content-Type: application/json" \
  -d '{"amount": 15, "paymentGateway": "stripe", "paymentDetails": {"paymentMethodId": "pm_test"}, "description": "Test payment"}'
  ```

- **GET /payments/status/:paymentId** (Authenticated)
  - **Description**: Checks payment status (Stripe only).
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/payments/status/<paymentId>?paymentGateway=stripe" \
  -H "Authorization: Bearer <user_token>"
  ```
  - **Note**: Replace `<paymentId>` with a real Stripe `pay_xxx` ID from a previous payment.

#### 5. Admin (5 Endpoints)

- **GET /admin/users** (Admin Authenticated)
  - **Description**: Lists all users.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/admin/users" \
  -H "Authorization: Bearer <admin_token>"
  ```

- **GET /admin/numbers** (Admin Authenticated)
  - **Description**: Lists all numbers.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/admin/numbers" \
  -H "Authorization: Bearer <admin_token>"
  ```

- **POST /admin/add-number** (Admin Authenticated)
  - **Description**: Adds a number manually.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/admin/add-number" \
  -H "Authorization: Bearer <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{"phoneNumber": "+12025550123", "country": "US", "price": 10}'
  ```

- **GET /admin/payments** (Admin Authenticated)
  - **Description**: Lists all payments (placeholder).
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/admin/payments" \
  -H "Authorization: Bearer <admin_token>"
  ```

- **PUT /admin/payments/:id** (Admin Authenticated)
  - **Description**: Updates payment status (placeholder).
  ```bash
  curl -X PUT "https://smsbackend-iota.vercel.app/api/admin/payments/<paymentId>" \
  -H "Authorization: Bearer <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{"status": "completed"}'
  ```

#### 6. Subscription (1 Endpoint)

- **POST /subscription/subscribe** (Authenticated)
  - **Description**: Subscribes to a plan.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/subscription/subscribe" \
  -H "Authorization: Bearer <user_token>" \
  -H "Content-Type: application/json" \
  -d '{"plan": "basic", "paymentGateway": "stripe", "paymentDetails": {"paymentMethodId": "pm_test"}}'
  ```

#### 7. Affiliate (3 Endpoints)

- **POST /affiliate/request-withdrawal** (Authenticated)
  - **Description**: Requests affiliate withdrawal.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/affiliate/request-withdrawal" \
  -H "Authorization: Bearer <user_token>" \
  -H "Content-Type: application/json" \
  -d '{"walletAddress": "0x123", "currency": "USD", "message": "Please send"}'
  ```

- **PUT /affiliate/withdrawal/:id** (Admin Authenticated)
  - **Description**: Updates withdrawal status.
  ```bash
  curl -X PUT "https://smsbackend-iota.vercel.app/api/affiliate/withdrawal/<withdrawalId>" \
  -H "Authorization: Bearer <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{"status": "paid"}'
  ```
  - **Note**: `<withdrawalId>` is a placeholder.

- **POST /affiliate/register-referral** (Authenticated)
  - **Description**: Registers a referral.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/affiliate/register-referral" \
  -H "Authorization: Bearer <user_token>" \
  -H "Content-Type: application/json" \
  -d '{"referralCode": "REF123"}'
  ```

#### 8. Promo (3 Endpoints)

- **GET /promo**
  - **Description**: Lists active promo icons.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/promo"
  ```

- **POST /promo** (Admin Authenticated)
  - **Description**: Adds a promo icon.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/promo" \
  -H "Authorization: Bearer <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{"name": "UHQ Accounts", "url": "https://uhqaccounts.com", "tooltip": "Get premium accounts!"}'
  ```

- **PUT /promo/:id** (Admin Authenticated)
  - **Description**: Updates a promo icon.
  ```bash
  curl -X PUT "https://smsbackend-iota.vercel.app/api/promo/<promoId>" \
  -H "Authorization: Bearer <admin_token>" \
  -H "Content-Type: application/json" \
  -d '{"enabled": false}'
  ```
  - **Note**: Get `<promoId>` from `POST /promo` response.

#### 9. Miscellaneous (8 Endpoints)

- **POST /translate**
  - **Description**: Translates text.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/translate" \
  -H "Content-Type: application/json" \
  -d '{"text": "Hello", "targetLang": "es"}'
  ```

- **POST /twilio/sms**
  - **Description**: Twilio webhook for incoming SMS.
  ```bash
  curl -X POST "https://smsbackend-iota.vercel.app/api/twilio/sms" \
  -H "Content-Type: application/json" \
  -d '{"To": "+12025550123", "From": "+12025550124", "Body": "Test SMS"}'
  ```
  - **Note**: Requires a valid Twilio number in `Number` collection.

- **GET /meta**
  - **Description**: Fetches SEO meta tags.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/meta"
  ```

- **GET /payment/paypal/success**
  - **Description**: PayPal success callback.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/payment/paypal/success?paymentId=test_payment_id&PayerID=test_payer_id"
  ```

- **GET /payment/paypal/cancel**
  - **Description**: PayPal cancel callback.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/payment/paypal/cancel"
  ```

- **GET /numbers/available/us**
  - **Description**: Lists US numbers (alias for `:country`).
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/available/us"
  ```

- **GET /numbers/available/gb**
  - **Description**: Lists GB numbers.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/available/gb"
  ```

- **GET /numbers/available/ca**
  - **Description**: Lists CA numbers.
  ```bash
  curl -X GET "https://smsbackend-iota.vercel.app/api/numbers/available/ca"
  ```

---

### Notes
- **Dynamic Values**:
  - `<user_token>`: From `/auth/login` or `/auth/register`.
  - `<admin_token>`: From admin login after running `seedAdmin.js`.
  - `<paymentId>`: From responses of `/payments/initiate`, `/wallet/deposit`, etc.
  - `<promoId>`: From `POST /promo`.
  - `<withdrawalId>`:
- **Payment Testing**:
  - Use test credentials for Stripe (`pm_test`), PayPal (sandbox), or Coinbase.
  - Ensure `.env` has valid keys.
- **Twilio**:
  - Requires funded Twilio account and valid numbers.
  - Test webhook with real Twilio data or mock it.
- **Vercel**:
  - Replace `https://smsbackend-iota.vercel.app` with `https://<your-vercel-url>`.
  - Ensure environment variables are set in Vercel dashboard.

---

### Testing Workflow
1. **Setup**:
   - Start server or deploy to Vercel.
   - Run `node seedAdmin.js` for admin user.
2. **Get Tokens**:
   - Register/login for `<user_token>`.
   - Login as admin for `<admin_token>`.
3. **Test Sequentially**:
   - Start with unauthenticated endpoints (`/auth/*`, `/numbers/available`, `/promo`, `/meta`, `/translate`).
   - Use `<user_token>` for user endpoints.
   - Use `<admin_token>` for admin endpoints.
4. **Capture IDs**:
   - Save `paymentId`, `promoId`, etc., from responses for subsequent calls.

