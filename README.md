# Gmail Integration Module 
   
This module provides a separate Google OAuth 2.0 integration specifically for Gmail access. It is completely isolated from the main Google Login system.

## 📁 Folder Structure
```text
emailManagement/
    routes/email.route.js          # API endpoints
    controllers/email.controller.js # Request handling
    services/email.service.js       # Business logic & Gmail API
    utils/googleEmailOAuth.js      # Isolated OAuth instance
    README.md                      # Setup & usage guide
```

## 🛠 Prerequisites

### 1. Environment Variables
Add these to your `.env` file:
```env
GOOGLE_CLIENT_ID_EMAIL=your_client_id
GOOGLE_CLIENT_SECRET_EMAIL=your_client_secret
GOOGLE_CALLBACK_URL_EMAIL=http://localhost:8001/api/email/callback
```

### 2. Google Cloud Console Setup
1. Go to the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a **New Project** (or use an existing one, but ensure you create a NEW OAuth client).
3. Enable **Gmail API** in the API Library.
4. Configure the **OAuth Consent Screen**:
    - User Type: External.
    - Add Scope: `https://www.googleapis.com/auth/gmail.readonly`.
5. Go to **Credentials**:
    - Click **Create Credentials** -> **OAuth client ID**.
    - Application type: **Web application**.
    - **Authorized redirect URIs**: Add `http://localhost:8001/api/email/callback`.
6. Copy the **Client ID** and **Client Secret** into your `.env` variables.

## 🚀 Routes & Usage

All routes are prefixed with `/api/email`. **Note: These routes (except callback) require a Bearer Token in the Authorization header.**

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/api/email/connect` | Returns a JSON response with the Google OAuth URL. **Requires Auth.** |
| `GET` | `/api/email/callback` | Internal callback handled by Google. **No Auth header required** (uses state). |
| `GET` | `/api/email/inbox` | Fetches last 20 emails for the logged-in user. **Requires Auth.** |


### Connect Response Example
```json
{
  "success": true,
  "url": "https://accounts.google.com/o/oauth2/v2/auth?..."
}
```

### Inbox Filtering
You can filter the inbox by category using query parameters:
- `GET /api/email/inbox?category=promotions`
- `GET /api/email/inbox?category=social`
- `GET /api/email/inbox?category=updates`
- `GET /api/email/inbox?category=forums`

### Inbox Response Format
```json
{
  "success": true,
  "data": [
    {
      "id": "message_id",
      "subject": "Email Subject",
      "from": "Sender Name <sender@example.com>",
      "snippet": "Preview text...",
      "date": "Date string"
    }
  ]
}
```
   
## 🛡 Security
- **Isolation**: Uses `GOOGLE_CLIENT_ID_EMAIL` to prevent collision with login credentials.
- **Privacy**: Admin cannot access other users' emails.
- **Tokens**: Access tokens refresh automatically. Refresh tokens are stored securely in the database.
