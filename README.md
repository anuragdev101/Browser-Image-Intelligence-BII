# 📸 Real-Time Screenshot Analyzer v2.1 (Secured)

![Version](https://img.shields.io/badge/version-2.1-blue)  
![License](https://img.shields.io/badge/license-MIT-brightgreen)  
![Build Status](https://img.shields.io/badge/build-passing-green)  
![Dependencies](https://img.shields.io/badge/dependencies-up%20to%20date-blueviolet)

> ⚠️ **Disclaimer:** This project is intended solely for **educational, accessibility, and research purposes.** It is not meant for academic dishonesty. **Use responsibly.**

---
<img src="https://github.com/user-attachments/assets/d8aaa9e5-418a-4f92-99a4-183654c423be" width="1200" height="900" />

## 📚 Table of Contents

- [📘 Overview](#-overview)
- [🛠️ Features](#-features)
- [🧱 Architecture](#-architecture)
- [📁 Repository Structure](#-repository-structure)
- [⚙️ Setup and Installation](#-setup-and-installation)
- [🧪 Usage Workflow](#-usage-workflow)
- [🔐 Security Considerations](#-security-considerations)
- [🩹 Troubleshooting](#-troubleshooting)
- [📝 License](#-license)
- [🧾 Final Notes](#-final-notes)

---

## 📘 Overview

**Real-Time Screenshot Analyzer** is a full-stack solution to capture, upload, and analyze browser screenshots using:

- 🧩 A Chrome Extension for capturing screenshots  
- ☁️ AWS services (S3, API Gateway, Lambda, DynamoDB) for secure and scalable backend processing  
- 🤖 OpenAI’s GPT-4 Vision model for analyzing images in real time  
- 🖥️ A Python (Tkinter) desktop app to interact with and display analysis results

---

## 🛠️ Features

- ⚡ **Real-Time Communication** — WebSocket-based notifications for near-instant updates  
- ☁️ **Cloud-Backed Storage** — Screenshots securely stored in AWS S3 with signed URL access  
- 🧬 **Serverless Architecture** — Powered by CloudFormation, Lambda, and API Gateway  
- 🧠 **Advanced AI Analysis** — Uses GPT-4 Vision for intelligent interpretation of screenshots  
- 🧩 **Chrome Extension** — Lightweight extension captures tab screenshots via shortcut  
- 🔐 **Secure by Design** — API keys, signed URLs, and least-privilege IAM roles

---

## 🧱 Architecture

![AWS](https://img.icons8.com/color/24/000000/amazon-web-services.png)  
A simplified flow of how the system operates:

```
+---------------------+
|   Chrome Extension  |   <-- Captures Screenshot (Ctrl + X)
+---------------------+
           |
           v
+------------------------+
|     HTTP API (AWS)     |  <-- Upload to S3, Trigger WebSocket
+------------------------+
           |
           v
+------------------------+
|     AWS Infrastructure |
|  (S3, Lambda, DynamoDB)|
+------------------------+
           |
           v
+------------------------+
|   Python Host App (Tk) |  <-- Receives message, downloads image
+------------------------+
           |
           v
+------------------------+
|   OpenAI GPT-4 Vision  |  <-- Sends image for analysis
+------------------------+
```

---

## 📁 Repository Structure

```bash
/ (root) 
├── image-processor/               # Chrome Extension files
│   ├── manifest.json              # Extension manifest
│   ├── background.js              # Handles screenshot + API calls
│   ├── content.js                 # For optional DOM interaction
├── image_processor.py             # Python GUI + WebSocket + OpenAI
├── aws_cloudfront_infra.cfn.json  # CloudFormation AWS infrastructure
├── requirements.txt               # Python dependencies
├── .env                           # Root env config for host app
└── README.md                      # You're reading it!
```

---

## ⚙️ Setup and Installation

### 🔧 Prerequisites

- 🧠 Basic understanding of AWS, Python, and JavaScript
- ✅ AWS account with permission to deploy CloudFormation
- 🔑 OpenAI API key
- 🌐 Python 3.7+ with Tkinter

---

### 🚀 1. Deploy AWS Infrastructure

```bash
aws cloudformation deploy \
  --template-file aws_cloudfront_infra.cfn.json \
  --stack-name image-processor-infra \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides APIGatewayApiKey=YOUR_API_GATEWAY_API_KEY
```

🔁 **Remember:** Replace `YOUR_API_GATEWAY_API_KEY` with a secure string. You should generate create this string manually ex: "9ngh846k543ngh846kt3"  
📋 Note the outputs from CloudFormation (WebSocket URL, S3 Bucket, etc.).

---

### 🧩 2. Set Up the Chrome Extension

1. Update background.js with these keys:

  ```bash
  GET_UPLOAD_URL_ENDPOINT (HttpApiGetUploadUrlEndpoint from cloudformation output)
  TRIGGER_RELAY_ENDPOINT (HttpApiTriggerUrl from cloudformation output)
  API_KEY_VALUE (API Gateway API key you generated manually earlier and supplied to cloud formation deploy)
```

2. Load in Chrome:
   - Visit `chrome://extensions`
   - Enable **Developer mode**
   - Click **Load unpacked** and select the `image-processor/` folder

---

### 🖥️ 3. Run the Python Host App

1. Create or update `.env` in project root:
	Update the follwing values, use cloudformation output values
   ```env
   OPENAI_API_KEY=sk-...
   WSS_URL=wss://your-websocket-endpoint
   S3_BUCKET_NAME=your-s3-bucket-name
   OPENAI_MODEL=gpt-4o
   WEBSOCKET_SHARED_SECRET=<API Gateway API key you generated manually earlier and supplied to cloud formation deploy>
   ```

2. Install dependencies:
   ```bash
   python -m venv venv
   source venv/bin/activate  # Windows: venv\Scripts\activate
   pip install -r requirements.txt
   ```

3. Run the app:
   ```bash
   python image_processor.py
   ```

---

## 🧪 Usage Workflow

### 🧩 Chrome Extension

- Press **Ctrl + X** to capture a screenshot
- Screenshot is uploaded via HTTP API
- AWS triggers WebSocket notification

### 🖥️ Python Host App

- Listens via WebSocket
- Downloads the image from S3 using signed URL
- Sends it to OpenAI GPT-4 for analysis
- Displays the result and copies to clipboard

---

## 🔐 Security Considerations

- 🔑 **Environment Variables:** All keys/secrets stored in `.env`, excluded via `.gitignore`
- 🔒 **Signed URLs:** Used for secure, temporary access to uploaded images
- 🧾 **IAM Roles:** Follow least-privilege principle
- 🔒 **SSL:** Ensure verification is **enabled** in production

---

## 🩹 Troubleshooting

| Issue                      | Fix                                                                 |
|---------------------------|----------------------------------------------------------------------|
| Missing `.env` values     | Double-check both image_processor.py `.env` file                   |
| WebSocket connection fail | Verify `WSS_URL` and secret match in both host + extension configs  |
| Upload issues             | Check Chrome Dev Tools > Network tab for errors                     |
| OpenAI error              | Ensure valid API key and image size limits                          |

---

## 📝 License

This project is licensed under the **[MIT License](https://opensource.org/licenses/MIT)**.

---

## 🧾 Final Notes

- 🛠️ **Customize:** Update endpoints, credentials, and prompts as needed
- 📂 **.gitignore:** Ensure sensitive files like `.env` are not committed
- 🖼️ **Architecture Diagram:** Add a PNG/SVG diagram in `/assets` and reference it in README if desired
- ✅ **Great for Interviews** — Showcases full-stack development, AWS, WebSockets, and OpenAI integration

---

🚀 Happy Building!
