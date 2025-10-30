# API Testing Guide - Spend Wise

This guide explains how to test the complete API flow from generating an API key to importing and categorizing transactions.

## 🔑 Step 1: Generate API Key

1. **Open your Spend Wise app** and go to Settings
2. **Click "Generate API Key"** in the API Key section
3. **Copy the generated key** (it will look like: `aB3xY9mK2pQ7vR4tU8wE1nL5sA6dF0gH`)

## 🧪 Step 2: Test the API

### Testing Transactions API

#### Test with curl

```bash
curl -X POST https://your-app.vercel.app/api/transactions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: YOUR_API_KEY_HERE" \
  -d '{
    "transactions": [
      {
        "id": "70f2a1273233c59ed5749b0236a896df641c1c75a1815e23e4c8b302ae56116c",
        "date": "2025-06-26T20:00:00.000Z",
        "description": "jetshr>Tbilisi GE",
        "amount": "-0.60",
        "currency": "GEL",
        "entryType": "BlockedTransaction",
        "status": "Green"
      },
      {
        "id": "80f3b2384344d60fe6850c1347b907ef752d2d86b2926f34f5d9c413bf67227d",
        "date": "2025-06-27T10:30:00.000Z",
        "description": "Coffee Shop>Tbilisi GE",
        "amount": "-5.00",
        "currency": "GEL",
        "entryType": "Purchase",
        "status": "Green"
      }
    ]
  }'
```

#### Test with JavaScript

```javascript
const apiKey = 'YOUR_API_KEY_HERE';
const transactions = [
  {
    id: '70f2a1273233c59ed5749b0236a896df641c1c75a1815e23e4c8b302ae56116c',
    date: '2025-06-26T20:00:00.000Z',
    description: 'jetshr>Tbilisi GE',
    amount: '-0.60',
    currency: 'GEL',
    entryType: 'BlockedTransaction',
    status: 'Green'
  },
  {
    id: '80f3b2384344d60fe6850c1347b907ef752d2d86b2926f34f5d9c413bf67227d',
    date: '2025-06-27T10:30:00.000Z',
    description: 'Coffee Shop>Tbilisi GE',
    amount: '-5.00',
    currency: 'GEL',
    entryType: 'Purchase',
    status: 'Green'
  }
];

fetch('https://your-app.vercel.app/api/transactions', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-API-Key': apiKey
  },
  body: JSON.stringify({ transactions })
})
.then(response => response.json())
.then(data => console.log('Success:', data))
.catch(error => console.error('Error:', error));
```

### Testing OTP API

The OTP endpoint allows you to save OTP codes for users. The API key is provided in the header (X-API-Key) and the server uses it to identify the user.

#### Test OTP with curl

```bash
curl -X POST https://your-app.vercel.app/api/otps \
  -H "Content-Type: application/json" \
  -H "X-API-Key: aB3xY9mK2pQ7vR4tU8wE1nL5sA6dF0gH" \
  -d '{
    "otpCode": "123456"
  }'
```

#### Test OTP with JavaScript

```javascript
const otpData = {
  otpCode: '123456'
};

fetch('https://your-app.vercel.app/api/otps', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-API-Key': 'aB3xY9mK2pQ7vR4tU8wE1nL5sA6dF0gH'
  },
  body: JSON.stringify(otpData)
})
.then(response => response.json())
.then(data => console.log('OTP Success:', data))
.catch(error => console.error('OTP Error:', error));
```

### Retrieving OTP API

The GET OTP endpoint allows you to retrieve the current OTP code for a user based on their API key.

#### Get OTP with curl

```bash
curl -X GET https://your-app.vercel.app/api/otps \
  -H "X-API-Key: aB3xY9mK2pQ7vR4tU8wE1nL5sA6dF0gH"
```

#### Get OTP with JavaScript

```javascript
fetch('https://your-app.vercel.app/api/otps', {
  method: 'GET',
  headers: {
    'X-API-Key': 'aB3xY9mK2pQ7vR4tU8wE1nL5sA6dF0gH'
  }
})
.then(response => response.json())
.then(data => console.log('OTP Retrieved:', data))
.catch(error => console.error('OTP Error:', error));
```

## 📊 Expected Responses

### Transactions API Response

If successful, you'll get:

```json
{
  "success": true,
  "message": "Transactions processed successfully",
  "receivedAt": "2025-06-27T20:25:56.768Z",
  "processedData": {
    "userId": "user123",
    "totalReceived": 2,
    "savedCount": 2,
    "skippedCount": 0,
    "savedTransactions": [
      "70f2a1273233c59ed5749b0236a896df641c1c75a1815e23e4c8b302ae56116c",
      "80f3b2384344d60fe6850c1347b907ef752d2d86b2926f34f5d9c413bf67227d"
    ],
    "skippedTransactions": []
  }
}
```

### OTP API Response

If successful (new OTP), you'll get:

```json
{
  "success": true,
  "message": "OTP saved successfully",
  "data": {
    "otpId": "abc123def456ghi789",
    "userId": "user123",
    "action": "created"
  }
}
```

If successful (existing user OTP updated), you'll get:

```json
{
  "success": true,
  "message": "OTP updated successfully",
  "data": {
    "otpId": "abc123def456ghi789",
    "userId": "user123",
    "action": "updated"
  }
}
```

### GET OTP API Response

If successful, you'll get:

```json
{
  "success": true,
  "message": "OTP retrieved successfully",
  "data": {
    "otpId": "abc123def456ghi789",
    "userId": "user123",
    "otpCode": "123456"
  }
}
```

If no OTP found, you'll get:

```json
{
  "success": false,
  "message": "No OTP found for this user"
}
```

## 🔍 Step 3: Check Firebase

### For Transactions
1. **Go to Firebase Console** → Firestore Database
2. **Navigate to**: `users/{userId}/expenses`
3. **You should see** the imported transactions with:
   - `category: ""` (empty for manual categorization)
   - `source: "bank_import"`
   - `importedAt: "2025-06-27T20:25:56.768Z"`

### For OTPs
1. **Go to Firebase Console** → Firestore Database
2. **Navigate to**: `OTPS` collection
3. **You should see** the saved OTP documents with:
   - `userId: "user123"`
   - `otpCode: "123456"`

**Note**: Each userId is unique in the OTPS collection. If you send a new OTP for the same user (same API key), it will update the existing document rather than creating a new one.

## 📋 Step 4: Categorize Transactions

1. **Add the UncategorizedTransactions component** to your app
2. **Import it** in your main dashboard or create a new page
3. **Users can now**:
   - See all uncategorized transactions
   - Select categories from dropdown
   - Delete unwanted transactions

## 🚨 Error Scenarios

### Transactions API Errors

#### Invalid API Key
```json
{
  "success": false,
  "message": "Invalid API key"
}
```

#### Missing API Key
```json
{
  "success": false,
  "message": "API key is required. Please provide X-API-Key header."
}
```

#### Duplicate Transaction
- Transaction will be skipped
- `skippedCount` will increase
- Transaction ID will be in `skippedTransactions` array

### OTP API Errors

#### POST /api/otps Errors

##### Missing Required Fields
```json
{
  "success": false,
  "message": "otpCode is required"
}
```

##### Invalid OTP Code Format
```json
{
  "success": false,
  "message": "Invalid OTP code format"
}
```

#### GET /api/otps Errors

##### No OTP Found
```json
{
  "success": false,
  "message": "No OTP found for this user"
}
```

#### Common API Key Errors (Both Endpoints)

##### Missing API Key Header
```json
{
  "success": false,
  "message": "API key is required. Please provide X-API-Key header."
}
```

##### Invalid API Key
```json
{
  "success": false,
  "message": "Invalid API key"
}
```

## 🔧 Environment Variables for Vercel

Add these to your Vercel project settings:

```
FIREBASE_API_KEY=your_firebase_api_key
FIREBASE_AUTH_DOMAIN=your_project.firebaseapp.com
FIREBASE_PROJECT_ID=your_project_id
FIREBASE_STORAGE_BUCKET=your_project.appspot.com
FIREBASE_MESSAGING_SENDER_ID=123456789
FIREBASE_APP_ID=1:123456789:web:abcdef
```

## 📱 Integration Example

Here's how to integrate the UncategorizedTransactions component:

```tsx
// In your dashboard or main app
import { UncategorizedTransactions } from "@/components/UncategorizedTransactions";

// Add to your component
<UncategorizedTransactions />
```

## 🎯 Complete Flow Summary

### Transactions Flow
1. ✅ **User generates API key** in Settings
2. ✅ **API key is saved** to Firebase
3. ✅ **External system calls API** with transactions + API key
4. ✅ **Server validates API key** and finds user
5. ✅ **Transactions are saved** to Firebase with empty categories
6. ✅ **User categorizes transactions** manually in the app
7. ✅ **Categorized transactions** appear in normal expense tracking

### OTP Flow

#### Saving OTP (POST /api/otps)
1. ✅ **External system calls OTP API** with API key in header + OTP code in body
2. ✅ **Server validates API key** from header and identifies user
3. ✅ **Server validates OTP format** from request body
4. ✅ **Server checks if userId exists** in OTPS collection
5. ✅ **If exists**: Updates existing OTP code for that user
6. ✅ **If new**: Creates new document with userId and OTP code

#### Retrieving OTP (GET /api/otps)
1. ✅ **External system calls GET OTP API** with API key in header
2. ✅ **Server validates API key** from header and identifies user
3. ✅ **Server searches for OTP** in OTPS collection by userId
4. ✅ **If found**: Returns OTP code and document details
5. ✅ **If not found**: Returns 404 error with appropriate message

## 🔒 Security Notes

- API keys are stored securely in Firebase
- Each API key is unique per user
- Transactions are validated before saving
- Duplicate transactions are automatically skipped
- All API calls are logged for monitoring

## 🐛 Troubleshooting

### API Key Not Working
- Check if the key is copied correctly
- Verify the key exists in Firebase
- Ensure the key is in the `X-API-Key` header

### Transactions Not Appearing
- Check Firebase console for errors
- Verify the user ID is correct
- Check if transactions already exist (duplicates are skipped)

### Categorization Not Working
- Ensure the UncategorizedTransactions component is added
- Check Firebase permissions
- Verify the component is properly imported

## 📈 Next Steps

- Add transaction import history
- Implement bulk categorization
- Add transaction search/filtering
- Create import scheduling
- Add transaction validation rules 