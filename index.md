# Athen API Documentation

## Introduction

Welcome to the Athen API documentation. The base URL for all API requests is:

```
https://api.athen-main.c3.athen.app
```
To use the API, you need an API key.  
  
You can create and manage your API keys at [Athen Settings](https://athen.app/settings). Include your API key in the `X-API-KEY` header for all requests.

## Endpoints

### Chat

The chat endpoint is used to get a response from Athen AI.

#### TypeScript Type

The expected request body type is:

```typescript
import fetch from 'node-fetch';

const chat = async (body: {
  conversationKey: string;
  conversation?: Array<{
    content: string
    name?: string
    format?: 'text' | 'html' | 'markdown'
  }>;
  message: {
    content: string;
    name?: string;
    format?: string;
  };
  responseFormat?: 'text' | 'html' | 'markdown'
}) => {
  const response = await fetch('https://api.athen-main.c3.athen.app/v1/api/chat', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-API-KEY': 'your-api-key-here',
    },
    body: JSON.stringify(body),
  });

  const data = await response.json();
  return data;
};

// Example usage
const requestBody = {
  conversationKey: 'exampleKey',
  message: {
    content: 'Hello, Athen AI!',
  },
};

chat(requestBody).then(response => console.log(response));
```

```bash
curl -X POST https://api.athen-main.c3.athen.app/v1/api/chat \
  -H "Content-Type: application/json" \
  -H "X-API-KEY: your-api-key-here" \
  -d '{
    "conversationKey": "exampleKey",
    "message": {
      "content": "Hello, Athen AI!"
    }
  }'
```

#### Response
```json
{
  "message": "Your response from Athen AI"
}
```

### Upload Files Endpoint
The upload-files endpoint is used to upload files to your account.  
These files can later be queried.

```typescript
import fetch from 'node-fetch';
import FormData from 'form-data';
import fs from 'fs';

const uploadFiles = async (params: {
  reqId: string;
  files: Array<{
    content: Buffer;
    contentType: string;
    filename?: string;
    checksum?: string;
    injectedContext?: string;
  }>;
}) => {
  const formData = new FormData();
  params.files.forEach(file => {
    formData.append('files', file.content, {
      filename: file.filename,
      contentType: file.contentType,
    });
  });

  const response = await fetch(`https://api.athen-main.c3.athen.app/v1/api/files/upload-form?reqId=${params.reqId}`, {
    method: 'POST',
    headers: {
      'X-API-KEY': 'your-api-key-here',
    },
    body: formData,
  });

  const data = await response.json();
  return data;
};

// Example usage
const fileContent = fs.readFileSync('path/to/your/file.txt');
const requestBody = {
  reqId: 'exampleRequestId',
  body: {
    files: [
      {
        content: fileContent,
        contentType: 'text/plain',
        filename: 'file.txt',
      },
    ],
  },
};

uploadFiles(requestBody).then(response => console.log(response));
```

```bash
curl -X POST "https://api.athen-main.c3.athen.app/v1/api/files/upload-form?reqId=exampleRequestId" \
  -H "X-API-KEY: your-api-key-here" \
  -F "files=@path/to/your/file.txt;type=text/plain"
```

#### Response
```json
{
  "fullfilled": "accepted"
}
```

