
# Google Drive WhatsApp Assistant for Act and Rise Foundation

This project provides a powerful AI-driven assistant that allows the Act and Rise Foundation team to manage their Google Drive files directly from WhatsApp. Built on the low-code automation platform **n8n**, this assistant can understand commands to list, delete, move, and summarize documents, streamlining file management and improving productivity.

## ✨ Features

  * **Conversational Interface**: Manage files using simple commands in WhatsApp.
  * **File & Folder Management**:
      * **LIST**: List the contents of any Google Drive folder.
      * **DELETE**: Securely delete files.
      * **MOVE**: Move files between folders.
  * **AI-Powered Summarization**: Get concise summaries of text-based documents (`.pdf`, `.docx`, `.txt`) directly in your chat.
  * **Secure & Robust**: Uses Google's secure OAuth2 protocol and has built-in safety checks.
  * **Memory**: The assistant can remember the context of your conversation for follow-up questions.

## 🏗️ How It Works: Architecture

The workflow uses a hybrid model to efficiently handle different types of requests. A main router first checks if the user's command is for a `SUMMARY`.

1.  **General Commands (LIST, DELETE, MOVE)**: If the command is not `SUMMARY`, it is sent to a general-purpose **AI Agent**. This agent understands the command, uses its set of Google Drive tools to perform the action, and formulates a confirmation message.
2.  **Summary Command**: If the command is `SUMMARY`, it is routed to a **dedicated, step-by-step workflow** optimized for file processing. This path finds the file, downloads it, extracts the text based on its type (PDF, DOCX, etc.), uses a specialized AI agent to summarize the content, and then sends the summary back.

This architecture ensures that simple, common tasks are handled flexibly by the main agent, while the complex, multi-step summarization process is handled by a more reliable, hard-coded procedure.

```
graph TD
    A[WhatsApp Message via Webhook] --> B{If Command is 'SUMMARY'};
    B -- No --> C[🤖 General AI Agent <br> (Handles LIST, MOVE, DELETE)];
    B -- Yes --> D[⚙️ Dedicated Summary Workflow <br> (Find -> Download -> Parse -> Summarize)];
    C --> E[✅ Twilio Reply];
    D --> E[✅ Twilio Reply];
```

## 🚀 Setup and Installation

Follow these steps to get your own instance of the assistant running.

### Prerequisites

  * An **n8n** instance (Cloud or self-hosted).
  * A **Google Cloud Platform** account with billing enabled.
  * A **Twilio** account with a WhatsApp Sandbox number.
  * An **OpenAI** API Key.

### Step 1: Configure Google Cloud & Credentials

The assistant needs permission to access Google Drive on your behalf.

1.  Go to the [Google Cloud Console](https://console.cloud.google.com/).
2.  Create a new project.
3.  In the menu, go to **APIs & Services \> Enabled APIs & Services** and enable the **Google Drive API**.
4.  Go to **APIs & Services \> Credentials**.
5.  Click **+ CREATE CREDENTIALS** and select **OAuth client ID**.
6.  Choose **Web application** as the application type.
7.  Under **Authorized redirect URIs**, you must add the OAuth redirect URL from your n8n instance. To get this, go to your n8n dashboard, click **Credentials \> New**, select **Google OAuth2 API**, and copy the URL provided there.
8.  Save the credential. Copy the **Client ID** and **Client Secret**.

### Step 2: Configure Twilio WhatsApp Sandbox

1.  Log in to your [Twilio Console](https://www.twilio.com/console).
2.  Navigate to **Messaging \> Try it out \> Send a WhatsApp message**.
3.  Follow the instructions to connect to your sandbox by sending a code from your phone to your sandbox number.
4.  In the **Sandbox settings** tab, find the field **"When a message comes in"** and paste your n8n Webhook URL.

### Step 3: Configure n8n

1.  **Import the Workflow**: Download the `Google Drive Managing Agent.json` file from this repository. In your n8n canvas, select **Import \> From file** and upload it.
2.  **Add Credentials**:
      * **Google Drive**: Go to **Credentials \> New**, search for **Google OAuth2 API**, and create a new credential using the **Client ID** and **Client Secret** from Step 1.
      * **Twilio**: Create a new **Twilio** credential using your Account SID and Auth Token from the Twilio Console.
      * **OpenAI**: Create a new **OpenAI** credential using your API key.
3.  **Link Credentials to Nodes**: Open the imported workflow and link the correct credential to each relevant node:
      * All **Google Drive** nodes and tools must be linked to your Google credential.
      * The **OpenAI Chat Model** nodes must be linked to your OpenAI credential.
      * The **Twilio** node must be linked to your Twilio credential.
4.  **Activate the Workflow**: Save the workflow and click the "Active" toggle at the top right.

Your WhatsApp assistant is now live\!

## 🛠️ Usage (Command Reference)

Send messages to your Twilio WhatsApp number in the following formats:

| Command   | Format                               | Description                                                                 |
| :-------- | :----------------------------------- | :-------------------------------------------------------------------------- |
| **LIST** | `LIST /FolderName`                   | Lists all files and folders inside the specified `FolderName`.              |
| **DELETE**| `DELETE /FolderName/FileName.ext`    | Deletes the specified file.                                                 |
| **MOVE** | `MOVE /FromFolder/File.ext /ToFolder`| Moves a file from one folder to another.                                    |
| **SUMMARY**| `SUMMARY /FolderName/FileName.ext` | Reads the content of a PDF, DOCX, or TXT file and provides a concise summary. |

## 🔒 Environment Variables

For security, it is highly recommended to manage your credentials using environment variables rather than saving them directly in n8n. Create a `.env` file for your n8n instance with the following keys:

```bash
# .env file for n8n

# Google OAuth2 Credentials
GOOGLE_CLIENT_ID=your_google_client_id_here
GOOGLE_CLIENT_SECRET=your_google_client_secret_here

# Twilio Credentials
TWILIO_ACCOUNT_SID=your_twilio_account_sid_here
TWILIO_AUTH_TOKEN=your_twilio_auth_token_here

# OpenAI API Key
OPENAI_API_KEY=your_openai_api_key_here
```

## 🤝 Contributing

Contributions from the community are welcome\! If you'd like to improve the workflow or add new features, please feel free to fork the repository and submit a pull request.

1.  Fork the Project
2.  Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3.  Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4.  Push to the Branch (`git push origin feature/AmazingFeature`)
5.  Open a Pull Request

## 📄 License

This project is distributed under the MIT License. See `LICENSE` for more information.#
