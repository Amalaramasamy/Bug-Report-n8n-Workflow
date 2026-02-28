
# QA Bug Report Generator - AI Workflow

An intelligent n8n workflow that automatically analyzes error logs and generates structured bug reports using AI.

## 🎯 What This Workflow Does

This workflow uses AI (powered by Groq's LLaMA 3.3 70B model) to:
- Receive error logs through a chat interface
- Analyze the error information
- Generate structured bug reports with:
  - **Title**: Brief description of the issue
  - **Severity**: Critical/High/Medium/Low classification
  - **Steps to Reproduce**: Detailed reproduction steps
  - **Expected Result**: What should happen
  - **Actual Result**: What actually happens
  - **Additional Information**: Relevant technical details

## 📋 Prerequisites

- n8n instance (self-hosted or cloud)
- Groq API credentials ([Get free API key](https://console.groq.com/))

## 🚀 Installation

### Step 1: Import the Workflow

1. Download the `qa-bug-report-ai.json` file from this repository
2. Open your n8n instance
3. Click **"Add workflow"** → **"Import from file"**
4. Select the downloaded JSON file
5. Click **"Import"**

### Step 2: Configure Credentials

1. Click on the **AI Agent** node
2. Click on the **Groq Chat Model** sub-node
3. Click **"Create New Credential"** for Groq API
4. Enter your Groq API key
5. Click **"Save"**

### Step 3: Test the Workflow

1. Click **"Test workflow"** in the top right
2. A chat interface will appear on the right side
3. Paste an error log (see examples below)
4. The AI will generate a structured bug report

## 📝 Usage Examples

### Example Error Log Input

[ERROR] 2024-01-15 10:23:45 - Database Connection Failed
Service: user-authentication-service
Error: Connection timeout after 30s
Database: PostgreSQL 14.2
Host: db-prod-01.example.com:5432
Expected: Connection established within 5s
Actual: Connection timeout, service unavailable
Impact: Users cannot log in


### Expected Output

Title: Database Connection Timeout - User Authentication Service

Severity: Critical

Steps to Reproduce:

Attempt to connect to PostgreSQL database at db-prod-01.example.com:5432
Wait for connection response
Observe timeout after 30 seconds
Expected Result:
Connection should be established within 5 seconds to the PostgreSQL 14.2 database

Actual Result:
Connection times out after 30 seconds, causing the user-authentication-service to become unavailable and preventing users from logging in

Additional Information:

Service: user-authentication-service
Database: PostgreSQL 14.2
Host: db-prod-01.example.com:5432
Timestamp: 2024-01-15 10:23:45
Impact: Users cannot log in

## 🔧 Workflow Structure

When chat message received (Trigger)
↓
AI Agent (with Groq LLaMA 3.3 70B)
↓
Structured Bug Report Output


### Nodes Included:

1. **When chat message received**: Chat trigger that receives error logs
2. **AI Agent**: Processes the error log using the configured prompt
3. **Groq Chat Model**: LLaMA 3.3 70B model for intelligent analysis

## 🎨 Customization

### Modify the Bug Report Format

1. Click on the **AI Agent** node
2. Edit the **"Prompt"** field to change:
   - Report sections
   - Severity levels
   - Output format
   - Analysis depth

### Change the AI Model

1. Click on the **Groq Chat Model** sub-node
2. Select a different model from the dropdown:
   - `llama-3.3-70b-versatile` (default, best for complex analysis)
   - `llama-3.1-8b-instant` (faster, lighter)
   - `mixtral-8x7b-32768` (alternative option)

## 🔗 Extending the Workflow

### Save Reports to a Database

Add a **Postgres/MySQL node** after the AI Agent to store bug reports.

### Send to Bug Tracking Systems

Add integration nodes:
- **Jira** node - Create issues automatically
- **Linear** node - Create tickets
- **GitHub** node - Create issues in repositories
- **Slack** node - Send reports to team channels

### Parse Output into Structured Data

Add a **Code node** after AI Agent:

```javascript
const response = $input.first().json.output;

const titleMatch = response.match(/\*\*Title:\*\*\s*(.+)/);
const severityMatch = response.match(/\*\*Severity:\*\*\s*(.+)/);
const stepsMatch = response.match(/\*\*Steps to Reproduce:\*\*\s*([\s\S]+?)\*\*Expected/);
const expectedMatch = response.match(/\*\*Expected Result:\*\*\s*([\s\S]+?)\*\*Actual/);
const actualMatch = response.match(/\*\*Actual Result:\*\*\s*([\s\S]+?)(\*\*|$)/);

return [{
  json: {
    title: titleMatch ? titleMatch[1].trim() : '',
    severity: severityMatch ? severityMatch[1].trim() : '',
    steps: stepsMatch ? stepsMatch[1].trim() : '',
    expected: expectedMatch ? expectedMatch[1].trim() : '',
    actual: actualMatch ? actualMatch[1].trim() : '',
    timestamp: new Date().toISOString()
  }
}];
🐛 Troubleshooting
Chat Trigger Shows 0 Items
This is normal before testing
Click "Test workflow" and use the chat interface to send a message
The trigger only produces data when messages are received
AI Agent Not Responding
Verify Groq API credentials are configured correctly
Check your Groq API quota/limits
Ensure the error log is being passed correctly
Poor Quality Bug Reports
Provide more detailed error logs
Adjust the AI prompt to be more specific
Try a different model (llama-3.3-70b-versatile is recommended)
📄 License
This workflow is provided as-is for use with n8n.

🤝 Contributing
Feel free to submit issues or pull requests to improve this workflow!

📚 Resources
n8n Documentation
Groq API Documentation
LangChain Documentation

---
