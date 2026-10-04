# CivicFix — AI-Powered Civic Issue Reporting Platform

> Turn a photo of a public problem into a structured civic complaint, help citizens understand it, share it through WhatsApp, and track its resolution.

## 🚨 Problem

Citizens frequently see potholes, garbage accumulation, broken streetlights, drainage problems, open manholes, water leaks, and other public infrastructure issues. Reporting these problems can be difficult because citizens may not know how to describe the issue, assess its urgency, or route it to the appropriate department.

## 💡 Solution

**CivicFix** uses Google Gemini AI to analyze a citizen's civic-issue photo and turn it into a structured complaint.

The core workflow is:

**📸 Photo → 🤖 Gemini Vision → 📝 Review & Edit → 🆔 Complaint → 💬 AI Assistant → 📲 WhatsApp → 📊 Track → ✅ Resolve**

A citizen can upload an image, review the AI-generated report, add location details, submit the complaint, ask questions about it, generate a concise summary, and share the summary through WhatsApp. An administrator can update the complaint status and attach resolution evidence.

---

## ✨ Key Features

### Citizen Features
- 📸 Upload JPG, PNG, JPEG, or WEBP civic-issue photos
- 🤖 Gemini Vision image analysis
- 🏷️ Civic issue classification
- ⚠️ Severity estimation: Low, Medium, High, Critical
- 🛡️ Public-safety risk assessment
- 🏢 Suggested department
- ✏️ Editable AI-generated complaint
- 📍 Area, city, and landmark details
- 🆔 Dynamic complaint IDs
- 📋 My Reports
- 💬 Complaint-aware AI assistant
- 📲 WhatsApp complaint sharing
- 🔎 Possible duplicate detection

### Admin Features
- 🔐 Password-protected admin section
- 📊 Complaint dashboard
- 🔍 Search and filtering
- 🔄 Complaint status workflow
- 🏢 Department assignment
- 📝 Resolution notes
- 📸 Before/after resolution evidence
- ✅ Resolution tracking

### AI Guardrails
CivicFix instructs Gemini to:
- Base visual analysis only on available evidence
- Avoid inventing addresses, GPS coordinates, policies, deadlines, or official ticket numbers
- Reject unrelated/non-civic images instead of creating false complaints
- Return structured JSON for issue analysis
- Keep complaint chat grounded in the actual complaint record

---

## 🏗️ Architecture

```text
                    CITIZEN
                       │
                       ▼
              Upload Issue Photo
                       │
                       ▼
              Google Gemini Vision
                       │
                       ▼
             Structured AI Analysis
                       │
                       ▼
              Citizen Review/Edit
                       │
                       ▼
             Duplicate Check
                       │
                       ▼
                Complaint Store
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
   Gemini Complaint Chat      WhatsApp Sharing
          │                         │
          └────────────┬────────────┘
                       ▼
                Admin Dashboard
                       │
                       ▼
            Status + Resolution Proof
```

The repository contains two related implementation layers:

- **Web application:** React + Vite frontend with an Express server and Google Gemini integration.
- **Streamlit implementation:** `app.py` plus modular Python services under the `civicfix/` directory, following the workshop architecture.

For the workshop submission, the Streamlit implementation is the intended Python/Streamlit build.

---

## 🛠️ Technology Stack

### Streamlit implementation
- Python
- Streamlit
- Google Gemini / `google-genai`
- SQLite
- Twilio WhatsApp
- Pillow

### Web application
- React
- TypeScript
- Vite
- Express
- Google GenAI SDK
- Tailwind CSS
- Lucide React
- Motion

---

## 📁 Project Structure

```text
civicfix/
│
├── app.py
├── prompts.py
├── ai_service.py
├── database.py
├── whatsapp_service.py
├── utils.py
├── requirements.txt
│
├── data/
│   └── civicfix.db
│
├── uploads/
│   ├── reports/
│   └── resolutions/
│
└── .streamlit/
    └── secrets.toml.example

Root web application:
├── src/
│   ├── App.tsx
│   ├── main.tsx
│   └── index.css
├── server.ts
├── package.json
├── vite.config.ts
├── tsconfig.json
├── .env.example
└── README.md
```

---

# 🚀 Streamlit Setup

## 1. Prerequisites

Install:

- Python 3.10+
- Git

## 2. Clone the repository

```bash
git clone YOUR_GITHUB_REPOSITORY_URL
cd civicfix
```

## 3. Create a virtual environment

### Windows PowerShell

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

### macOS / Linux

```bash
python3 -m venv venv
source venv/bin/activate
```

## 4. Install dependencies

```bash
pip install -r requirements.txt
```

## 5. Configure Streamlit secrets

Create:

```text
.streamlit/secrets.toml
```

Use the provided example as the template:

```text
.streamlit/secrets.toml.example
```

Example:

```toml
GEMINI_API_KEY = "YOUR_GEMINI_API_KEY"
MODEL_NAME = "YOUR_GEMINI_MODEL"

TWILIO_ACCOUNT_SID = "YOUR_TWILIO_ACCOUNT_SID"
TWILIO_AUTH_TOKEN = "YOUR_TWILIO_AUTH_TOKEN"
TWILIO_WHATSAPP_FROM = "whatsapp:+14155238886"
TWILIO_CONTENT_SID = "YOUR_TWILIO_CONTENT_SID"

ADMIN_PASSWORD = "YOUR_ADMIN_PASSWORD"
```

**Never commit your real `secrets.toml`.**

## 6. Run CivicFix

```bash
streamlit run app.py
```

The local application normally opens at:

```text
http://localhost:8501
```

---

# 🤖 Gemini Configuration

CivicFix uses Gemini for:

1. **Civic image analysis**
2. **Structured issue classification**
3. **Severity and safety-risk assessment**
4. **Complaint-aware chat**
5. **Complaint summary generation**
6. **Duplicate reasoning**

The Gemini API key is read from Streamlit secrets or the environment.

The application uses a cached Gemini client in the Streamlit implementation to avoid unnecessary client recreation during Streamlit reruns.

---

# 📲 WhatsApp Setup

CivicFix supports WhatsApp sharing through Twilio.

Required configuration:

```text
TWILIO_ACCOUNT_SID
TWILIO_AUTH_TOKEN
TWILIO_WHATSAPP_FROM
TWILIO_CONTENT_SID
```

For development/testing, Twilio's WhatsApp Sandbox can be used.

### Important

The recipient WhatsApp number must first join the correct Twilio Sandbox.

Use the **current join code displayed in your Twilio Console**. Do not reuse an old Sandbox code.

When the Twilio credentials are unavailable, CivicFix should continue operating and show a configuration message instead of treating the message as successfully sent.

---

# 🗄️ Database

The Streamlit implementation uses SQLite.

The database is initialized automatically and stores:

- Complaint records
- AI analysis
- Citizen corrections
- Complaint status
- Chat messages
- Resolution notes
- Resolution image references
- Creation/update timestamps

The database file is created under:

```text
data/civicfix.db
```

Do not commit the local database to GitHub.

---

# 🔄 Complaint Lifecycle

```text
Reported
    ↓
Under Review
    ↓
Assigned
    ↓
In Progress
    ↓
Resolved
```

A complaint can also be marked:

```text
Rejected
```

A complaint should only be shown as resolved after the administrator explicitly updates its status.

---

# 🧪 Suggested Test Flow

## Test 1 — Pothole

1. Open CivicFix.
2. Enter your name and WhatsApp number.
3. Go to **Report Issue**.
4. Upload a clear pothole photograph.
5. Click **Analyze Issue with Gemini**.
6. Check the AI-generated:
   - Issue type
   - Title
   - Description
   - Severity
   - Safety risk
   - Suggested department
   - Confidence
7. Edit the generated complaint.
8. Add area, city, and landmark.
9. Submit the complaint.
10. Confirm that a unique complaint ID is created.

## Test 2 — AI Assistant

Open the complaint and ask:

```text
Why is this classified as High severity?
```

Then try:

```text
Make this complaint shorter.
```

## Test 3 — WhatsApp

Generate the complaint summary and select:

```text
📲 Send to WhatsApp
```

Verify the Twilio Sandbox/WhatsApp configuration before testing delivery.

## Test 4 — Invalid Image

Upload an unrelated image such as food or a household object.

CivicFix should **not** create a false civic complaint.

## Test 5 — Duplicate

Submit another complaint with the same issue type and similar locality.

CivicFix should warn about a possible duplicate instead of silently rejecting it.

## Test 6 — Resolution

From the Admin section:

```text
Reported
→ Under Review
→ Assigned
→ In Progress
→ Resolved
```

Add a resolution note and upload an after-resolution image.

---

# 🔐 Security

Never commit:

```text
.streamlit/secrets.toml
.env
API keys
Twilio Auth Tokens
Admin passwords
Citizen-uploaded images
Local SQLite databases
```

Use:

- Streamlit secrets
- Environment variables
- Parameterized SQLite queries
- Input validation
- Safe file handling

Before publishing to GitHub, check:

```bash
git status
```

and make sure secrets are not included.

---

# ☁️ Streamlit Community Cloud Deployment

CivicFix can be deployed using Streamlit Community Cloud.

### 1. Push the project to GitHub

```bash
git add .
git commit -m "Build CivicFix AI civic reporting platform"
git push origin main
```

Make sure the repository is **public** if required by the workshop submission.

### 2. Create the Streamlit app

In Streamlit Community Cloud:

- Select the GitHub repository
- Select the branch
- Set the main file to:

```text
app.py
```

### 3. Add secrets

In the Streamlit app settings, add the contents of your private:

```text
.streamlit/secrets.toml
```

Do not upload the private secrets file to GitHub.

### 4. Deploy

After deployment, test:

- Home
- Image upload
- Gemini analysis
- Complaint submission
- Dashboard
- AI chat
- WhatsApp
- Admin
- Resolution proof

---

# 📊 Project Evaluation Focus

CivicFix is designed around four evaluation areas:

| Area | Focus |
|---|---|
| Functionality | Complete working reporting and tracking workflow |
| Prompt Design | Structured and grounded Gemini prompts |
| Code Quality | Modular services and persistent storage |
| Creativity / Polish | Civic-tech use case, dashboard, WhatsApp and resolution proof |

---

# 🎯 Future Enhancements

Potential future improvements include:

- GPS-based location capture
- Interactive civic issue map
- Real municipal API integration
- Image-similarity duplicate detection
- Firebase/PostgreSQL production database
- Citizen authentication
- Department-specific dashboards
- Push notifications
- Multilingual/regional-language support
- Analytics for recurring civic problems

These are future enhancements and should not be represented as currently implemented unless the corresponding feature is enabled in the deployed build.

---

# 📄 License

MIT License.

---

## 👥 Project

**CivicFix**

**AI-Powered Citizen Reporting and Public Issue Resolution Platform**

Built with Google Gemini AI, Streamlit, React/TypeScript, SQLite, and Twilio WhatsApp.
