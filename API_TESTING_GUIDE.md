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

The OTP endpoint allows you to save API keys with OTP codes to the Firebase OTPS table.

#### Test OTP with curl

```bash
curl -X POST https://your-app.vercel.app/api/otps \
  -H "Content-Type: application/json" \
  -d '{
    "apiKey": "aB3xY9mK2pQ7vR4tU8wE1nL5sA6dF0gH",
    "otpCode": "123456"
  }'
```

#### Test OTP with JavaScript

```javascript
const otpData = {
  apiKey: 'aB3xY9mK2pQ7vR4tU8wE1nL5sA6dF0gH',
  otpCode: '123456'
};

fetch('https://your-app.vercel.app/api/otps', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(otpData)
})
.then(response => response.json())
.then(data => console.log('OTP Success:', data))
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
    "apiKey": "aB3xY9mK...",
    "action": "created"
  }
}
```

If successful (existing API key updated), you'll get:

```json
{
  "success": true,
  "message": "OTP updated successfully",
  "data": {
    "otpId": "abc123def456ghi789",
    "apiKey": "aB3xY9mK...",
    "action": "updated"
  }
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
   - `apiKey: "aB3xY9mK2pQ7vR4tU8wE1nL5sA6dF0gH"`
   - `otpCode: "123456"`

**Note**: Each API key is unique in the OTPS collection. If you send the same API key with a different OTP code, it will update the existing document rather than creating a new one.

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

#### Missing Required Fields
```json
{
  "success": false,
  "message": "Both apiKey and otpCode are required"
}
```

#### Invalid API Key Format
```json
{
  "success": false,
  "message": "Invalid API key format"
}
```

#### Invalid OTP Code Format
```json
{
  "success": false,
  "message": "Invalid OTP code format"
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
1. ✅ **External system calls OTP API** with API key + OTP code
2. ✅ **Server validates input** (API key and OTP format)
3. ✅ **Server checks if API key exists** in OTPS collection
4. ✅ **If exists**: Updates existing OTP code for that API key
5. ✅ **If new**: Creates new document with API key and OTP code
6. ✅ **OTP can be retrieved** and validated by other systems

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