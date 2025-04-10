# Calendar MCP Server

A Model Context Protocol (MCP) server for Google Calendar integration in Claude Desktop. This server enables AI assistants to manage Google Calendar events through natural language interactions.

[![npm version](https://badge.fury.io/js/%40tkhduracell%2Fserver-calendar-mcp.svg)](https://www.npmjs.com/package/tkhduracell/server-calendar-mcp)
[![License: ISC](https://img.shields.io/badge/License-ISC-blue.svg)](https://opensource.org/licenses/ISC)

## Features

- Create calendar events with title, time, description, and location
- Retrieve event details by event ID
- Update existing events (title, time, description, location)
- Delete events
- List events within a specified time range
- Full integration with Google Calendar API
- Secure OAuth2 authentication

## Installation

```bash
git clone https://github.com/tkhduracell/Calendar-MCP-Server.git
npm install
```

## Setup

### Setup project 
1. Enable gcloud CLI
    ```bash
    gcloud projects create YOUR_PROJECT_ID --name="Your Project Name"
    # or 
    gcloud config set project YOUR_PROJECT_ID
    ```
2. Enable the API
   ```bash
   gcloud services enable calendar-json.googleapis.com
   ```

### Setup OAuth 2.0 credentials
1. Go to the Google Cloud Console.
1. Ensure your desired project is selected.
1. Navigate to `APIs & Services` > `Credentials`.
1. Click `Create Credentials` > `OAuth client ID`.
1. Select the appropriate application type (e.g., `Web application` if you intend to use the OAuth Playground as the redirect URI).
1. Give it a name (e.g., `Goose Calendar Access`).
1. Under `Authorized redirect URIs`, click `ADD URI` and enter https://developers.google.com/oauthplayground.
1. Click `Create`.
1. Copy the displayed Client ID and Client Secret. This is your `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET`. These are needed for the OAuth Playground step.

### Get Refresh Token:
1. ```bash
    export GOOGLE_CLIENT_ID="YOUR_CLIENT_ID"
    export GOOGLE_CLIENT_SECRET="YOUR_CLIENT_SECRET"
    export GOOGLE_REDIRECT_URI="https://developers.google.com/oauthplayground"
    export GOOGLE_SCOPE="https://www.googleapis.com/auth/calendar"
    ```
1. ```bash
    open "https://accounts.google.com/o/oauth2/v2/auth?client_id=${GOOGLE_CLIENT_ID}&redirect_uri=${GOOGLE_REDIRECT_URI}&scope=${GOOGLE_SCOPE}&response_type=code&access_type=offline&prompt=consent"
    ```
1. Copy the code and run 
   ```bash 
   export AUTH_CODE="PASTE_CODE_HERE"`
   ```
1. Get refesh token
   ```bash
    curl -s https://oauth2.googleapis.com/token \
      -X POST \
      -H "Content-Type: application/x-www-form-urlencoded" \
      -d "code=${AUTH_CODE}" \
      -d "client_id=${GOOGLE_CLIENT_ID}" \
      -d "client_secret=${GOOGLE_CLIENT_SECRET}" \
      -d "redirect_uri=${GOOGLE_REDIRECT_URI}" \
      -d "grant_type=authorization_code" | jq -r '.refresh_token'
    ```
1. Copy the "Refresh token" - this is your `GOOGLE_REFRESH_TOKEN`

2. Configure the MCP toole:

```json
{
  "calendar": {
    "command": "npx",
    "args": ["tkhduracell/server-calendar-mcp"],
    "env": {
      "GOOGLE_CLIENT_ID": "your_client_id_here",
      "GOOGLE_CLIENT_SECRET": "your_client_secret_here",
      "GOOGLE_REFRESH_TOKEN": "your_refresh_token_here"
    }
  }
}
```

## Usage Examples

The server provides several tools that can be used through the Claude Desktop:

### Create Event
```json
{
  "summary": "Team Meeting",
  "start": {
    "dateTime": "2024-01-20T10:00:00Z"
  },
  "end": {
    "dateTime": "2024-01-20T11:00:00Z"
  },
  "description": "Weekly team sync",
  "location": "Conference Room A"
}
```

### List Calendars
```json
{
  "minAccessRole": "writer"
}
```

### List Events
```json
{
  "timeMin": "2024-01-01T00:00:00Z",
  "timeMax": "2024-12-31T23:59:59Z",
  "maxResults": 10,
  "orderBy": "startTime"
}
```

### Update Event
```json
{
  "eventId": "event123",
  "summary": "Updated Meeting Title",
  "start": {
    "dateTime": "2024-01-20T11:00:00Z"
  },
  "end": {
    "dateTime": "2024-01-20T12:00:00Z"
  }
}
```

### Delete Event
```json
{
  "eventId": "event123"
}
```

## Security Notes

- Keep your Google API credentials secure
- Regularly rotate your refresh tokens
- Store sensitive information in Claude Desktop configuration
- Never share or commit your credentials to version control
- The refresh token gives access to your Google Calendar, treat it like a password

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the ISC License.

## Author

gongrzhe

## Support

If you encounter any issues or have questions, please file an issue on the GitHub repository.
