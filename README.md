# ☁️ Cloud Fun Facts Generator

Learning AWS can sometimes feel dry when tutorials only show isolated services. You often see **Lambda**, **S3**, or **API Gateway** separately — but rarely a project that connects them all meaningfully.

The **Cloud Fun Facts Generator** changes that! It's a fun, interactive project that lets you explore multiple AWS services together by building a real cloud-powered web app that delivers random, **AI-enhanced** fun facts about cloud computing 🌩️

---

## 🧠 Overview

This project helps you understand how different AWS services work together in a real-world architecture.

Instead of another "Hello World" demo, you'll build a **small but complete cloud application** using:
- A **serverless backend** (Lambda + API Gateway)
- A **database layer** (DynamoDB)
- A **Generative AI layer** (Amazon Bedrock — Claude 3.5 Sonnet)
- A **frontend** hosted on **AWS Amplify**

With one click, the user gets a random fun fact about the cloud — made witty and engaging by AI. Behind the scenes, AWS services interact seamlessly, showing how scalable, event-driven architectures work in practice.

---

## 🚀 Features

✅ Fetches random cloud computing facts from DynamoDB  
✅ Enhances facts with AI-generated witty responses using Amazon Bedrock (Claude 3.5 Sonnet)  
✅ Graceful fallback — returns the original fact if AI enhancement fails or is too long  
✅ Serverless backend powered by AWS Lambda  
✅ RESTful API exposed via Amazon API Gateway (CORS-enabled)  
✅ Lightweight, responsive HTML/CSS/JS frontend — no framework overhead  
✅ Frontend hosted on AWS Amplify  
✅ Least-privilege IAM role policy included for production-ready security  

---

## 📁 Project Structure

```
FunFacts/
├── Lambda.py          # AWS Lambda function (Python) — core backend logic
├── index.html         # Frontend — single-page HTML/CSS/JS application
├── lambda_role.json   # IAM policy template for the Lambda execution role
├── LICENSE            # MIT License
└── README.md          # Project documentation
```

| File | Description |
|------|-------------|
| **`Lambda.py`** | Python Lambda handler that scans the `CloudFacts` DynamoDB table, picks a random fact, sends it to Claude 3.5 Sonnet via Bedrock for a witty rewrite, and returns the result as JSON with CORS headers. |
| **`index.html`** | A self-contained, responsive frontend with a gradient UI. Calls the API Gateway endpoint via `fetch()` and displays the AI-enhanced fact. |
| **`lambda_role.json`** | A least-privilege IAM policy granting the Lambda function access to Bedrock model invocation, DynamoDB read/write, and CloudWatch logging. Placeholders must be replaced before use. |

---

## 🛠️ AWS Services Used

| Service | Purpose |
|---------|---------|
| **AWS Lambda** | Runs the serverless Python backend — fetches facts and invokes AI |
| **Amazon API Gateway** | Exposes the Lambda function as a public REST API endpoint |
| **Amazon DynamoDB** | Stores cloud computing facts in a `CloudFacts` table |
| **Amazon Bedrock** | Invokes Claude 3.5 Sonnet to rewrite facts in a fun, witty style |
| **AWS Amplify** | Hosts the static HTML frontend |
| **AWS IAM** | Manages least-privilege roles and permissions for the Lambda function |
| **Amazon CloudWatch** | Captures Lambda execution logs for monitoring and debugging |

---

## 🧩 Architecture

Below is the high-level architecture of the **Cloud Fun Facts Generator**:

```
                     ┌────────────────────────┐
                     │        USER            │
                     │  Clicks "Fun Fact"     │
                     └────────────┬───────────┘
                                  │
                                  ▼
                     ┌────────────────────────┐
                     │      AWS AMPLIFY       │
                     │  (Frontend Hosting)    │
                     │   index.html served    │
                     └────────────┬───────────┘
                                  │ 1. fetch() → API URL
                                  ▼
                     ┌────────────────────────┐
                     │    API GATEWAY         │
                     │ REST Endpoint (GET)    │
                     │ CORS enabled           │
                     └────────────┬───────────┘
                                  │ 2. Triggers Lambda
                                  ▼
                     ┌────────────────────────┐
                     │     LAMBDA (Python)    │
                     │  Lambda.py handler     │
                     │  - Scan DynamoDB       │
                     │  - Pick random fact    │
                     │  - Invoke Bedrock AI   │
                     └───────┬────────┬───────┘
                             │        │
   3. table.scan() →        │        │  4. invoke_model() →
   random.choice()          │        │     Claude 3.5 Sonnet
                             ▼        ▼
                     ┌──────────┐  ┌──────────────────┐
                     │ DYNAMODB │  │     BEDROCK       │
                     │CloudFacts│  │ Claude 3.5 Sonnet │
                     │  table   │  │ Rewrites fact     │
                     └──────────┘  └────────┬─────────┘
                                            │ 5. Witty fact returned
                                            ▼
                               ┌────────────────────────┐
                               │  AWS AMPLIFY FRONTEND  │
                               │ Displays the fun fact  │
                               └────────────────────────┘
```

---

## ⚙️ Setup Guide

Follow these steps to build your own Cloud Fun Facts Generator 👇

### 1️⃣ Create the DynamoDB Table

1. Open the [DynamoDB Console](https://console.aws.amazon.com/dynamodbv2).
2. Create a new table named **`CloudFacts`**.
3. Set the partition key (e.g., `FactId` — String).
4. Add items with a **`FactText`** attribute containing your cloud fun facts.

   Example item:
   ```json
   {
     "FactId": "1",
     "FactText": "AWS Lambda can run up to 15 minutes per invocation."
   }
   ```

### 2️⃣ Enable Amazon Bedrock Model Access

1. Open the [Amazon Bedrock Console](https://console.aws.amazon.com/bedrock).
2. Navigate to **Model access** and request access to **Claude 3.5 Sonnet** (`anthropic.claude-3-5-sonnet-20240620-v1:0`).
3. Wait for access to be granted (usually instant for supported regions).

### 3️⃣ Create the Lambda Function

1. Open the [Lambda Console](https://console.aws.amazon.com/lambda).
2. Create a new function with **Python 3.12** runtime.
3. Copy the contents of **`Lambda.py`** into the function code editor.
4. Set the execution timeout to **30 seconds** (Bedrock calls may take a few seconds).
5. Attach the IAM policy from **`lambda_role.json`** to the function's execution role.

   > ⚠️ **Important:** Replace all `<<PLACEHOLDER>>` values in `lambda_role.json` before attaching:
   > - `<<AWS_REGION>>` — your AWS region (e.g., `us-east-1`)
   > - `<<AWS_ACCOUNT_ID>>` — your 12-digit AWS account ID
   > - `<<BEDROCK_MODEL_ID>>` — `anthropic.claude-3-5-sonnet-20240620-v1:0`
   > - `<<DYNAMODB_TABLE_NAME>>` — `CloudFacts`
   > - `<<LAMBDA_FUNCTION_NAME>>` — your Lambda function's name

### 4️⃣ Set Up API Gateway

1. Open the [API Gateway Console](https://console.aws.amazon.com/apigateway).
2. Create a new **REST API**.
3. Add a **GET** method and integrate it with your Lambda function.
4. Enable **CORS** on the resource.
5. Deploy the API to a stage (e.g., `prod` or `funfact`).
6. Copy the **Invoke URL** — you'll need it for the frontend.

### 5️⃣ Deploy the Frontend

1. Open **`index.html`** and replace the `API_URL` value with your API Gateway invoke URL:
   ```javascript
   const API_URL = 'https://YOUR-API-ID.execute-api.REGION.amazonaws.com/STAGE';
   ```
2. Push the project to a **GitHub repository**.
3. Open the [AWS Amplify Console](https://console.aws.amazon.com/amplify).
4. Connect your GitHub repo and deploy — Amplify will host `index.html` automatically.

### 6️⃣ Test It!

Open your Amplify app URL and click **"Generate Fun Fact"** — you should see a witty, AI-enhanced cloud fact! 🎉

---

## 📸 Demo

🔗 **Live Demo:** [Cloud Fun Facts Generator](https://main.d3tj576yja86q4.amplifyapp.com/)

---

## ⏱️ Estimated Time & Cost

| Parameter | Estimate |
|-----------|----------|
| **Time** | 60–90 minutes |
| **Cost** | ~$0.03 (within Free Tier limits) |

💡 *Note:* New AWS accounts get **$200 free credits** for the first 6 months — more than enough to build and run this project.

---

## 🧰 Tech Stack

| Layer | Technology |
|-------|------------|
| **Frontend** | HTML, CSS, JavaScript (vanilla) — hosted on AWS Amplify |
| **Backend** | AWS Lambda (Python 3.12) |
| **API** | Amazon API Gateway (REST) |
| **Database** | Amazon DynamoDB (`CloudFacts` table) |
| **AI / GenAI** | Amazon Bedrock — Claude 3.5 Sonnet |
| **Security** | AWS IAM (least-privilege policy) |
| **Monitoring** | Amazon CloudWatch Logs |

---

## 💡 Learning Outcomes

By completing this project, you'll learn:

- How **serverless architecture** works end-to-end in AWS
- How to connect **API Gateway → Lambda → DynamoDB** in a real workflow
- How to invoke **Amazon Bedrock** foundation models (Claude) from Lambda
- How to design **least-privilege IAM roles** using custom policies
- How to **host static websites** with AWS Amplify
- How to build a **responsive frontend** that consumes a REST API
- How **CORS headers** work in API Gateway and Lambda responses

---

## 🔐 IAM Policy Reference

The included `lambda_role.json` follows the **principle of least privilege** and grants three specific permissions:

| Permission | Scope |
|------------|-------|
| `bedrock:InvokeModel` | Only the specified Bedrock foundation model |
| `dynamodb:PutItem, GetItem, Query, Scan` | Only the specified DynamoDB table |
| `logs:CreateLogGroup, CreateLogStream, PutLogEvents` | Only the Lambda function's log group |

---

## 🧑‍💻 Author

**Divesh Kumawat**  
📧 [kumavatdivesh671@gmail.com](mailto:kumavatdivesh671@gmail.com)  
💼 [LinkedIn](https://www.linkedin.com/in/divesh-kumawat-0874662b9/)

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

## ⭐ Contributing

Contributions, issues, and feature requests are welcome!  
Feel free to open a pull request or issue on GitHub.

---

### 🌈 Show Some Love

If you like this project, don't forget to **⭐ Star this repo** — it helps others discover it too!
