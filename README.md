# Spend Wise Server

Express.js backend server for the Spend Wise application, providing API endpoints for transaction management and user authentication.

## Features

- 🔐 Firebase Admin SDK integration
- 📊 Transaction management API
- 🔑 API key validation
- 🌐 CORS enabled
- 📝 Request logging with Morgan
- 🔒 Environment-based configuration

## Quick Start

### Prerequisites

- Node.js 18+ 
- Firebase project with Firestore
- Firebase service account credentials

### Installation

1. Clone the repository:
```bash
git clone <your-server-repo-url>
cd spend-wise-server
```

2. Install dependencies:
```bash
npm install
```

3. Set up environment variables:
```bash
cp .env.example .env.local
```

4. Configure your Firebase credentials in `.env.local`:
```env
FIREBASE_CLIENT_EMAIL=your-service-account-email@project.iam.gserviceaccount.com
FIREBASE_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\nYour Private Key Here\n-----END PRIVATE KEY-----"
```

### Development

Start the development server with hot reload:
```bash
npm run dev
```

The server will start on `http://localhost:3001`

### Production

Start the production server:
```bash
npm start
```

## API Endpoints

### Health Check
- `GET /api/health` - Server health status

### Transactions
- `GET /api/transactions` - Get user transactions
- `POST /api/transactions` - Add new transaction
- `DELETE /api/transactions/:id` - Delete transaction (soft delete)

### API Key Validation
- `POST /api/validate-key` - Validate user API key

## Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `FIREBASE_CLIENT_EMAIL` | Firebase service account email | Yes |
| `FIREBASE_PRIVATE_KEY` | Firebase service account private key | Yes |
| `PORT` | Server port (default: 3001) | No |

## Deployment

### Vercel

This server is configured for Vercel deployment. The `vercel.json` file contains the necessary configuration for serverless functions.

1. Install Vercel CLI:
```bash
npm i -g vercel
```

2. Deploy:
```bash
vercel
```

3. Set environment variables in Vercel dashboard:
   - Go to your project settings
   - Add the environment variables from your `.env.local`

### Other Platforms

For other deployment platforms, ensure:
- Environment variables are properly set
- The `server.js` file is the entry point
- CORS is configured for your frontend domain

## Project Structure

```
spend-wise-server/
├── server.js          # Main server file
├── firebase-config.js # Firebase configuration
├── package.json       # Dependencies and scripts
├── vercel.json        # Vercel deployment config
├── .env.local         # Environment variables (not in git)
└── README.md          # This file
```

## Development Notes

- The server uses Firebase Admin SDK for secure database access
- All transactions are soft-deleted (status set to "deleted")
- API key validation checks against user documents in Firestore
- CORS is configured to allow requests from the frontend domain

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

MIT License - see LICENSE file for details 