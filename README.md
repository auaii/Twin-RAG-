# 🤖 AI Digital Twin (Auaii)

โปรเจกต์ **AI Digital Twin** เป็นแชทบอทอัจฉริยะที่ทำหน้าที่เป็นตัวแทนของคุณ Auaii (Petcharat Limprasert) — Cloud Developer & AI Specialist โดย AI จะเรียนรู้ข้อมูลจากประวัติการทำงาน, Resume, LinkedIn, และสไตล์การพูด เพื่อให้สามารถตอบคำถามแทนตัวคุณได้เสมือนจริง 

โปรเจกต์นี้อ้างอิงและพัฒนาต่อยอดจากหลักสูตร [ed-donner/production](https://github.com/ed-donner/production/tree/main/week2) โดยถูกออกแบบมาให้สามารถ Deploy ทำงานบน AWS แบบ Serverless ได้อย่างสมบูรณ์แบบ

## ✨ ฟีเจอร์หลัก (Features)

- **Personalized AI**: ตอบคำถามเกี่ยวกับประวัติ, ประสบการณ์ทำงาน, และความเชี่ยวชาญของคุณ Auaii ได้อย่างแม่นยำ
- **Memory System**: ระบบจดจำประวัติการสนทนา (Session-based) ทำให้ AI เข้าใจบริบทและพูดคุยต่อเนื่องได้ (เก็บข้อมูลใน Local หรือ Amazon S3)
- **Serverless Architecture**: ทำงานบน AWS Lambda ประหยัดค่าใช้จ่าย (Pay-as-you-go) และรองรับการสเกล
- **Infrastructure as Code (IaC)**: จัดการโครงสร้างพื้นฐานทั้งหมดบน AWS อย่างเป็นระบบด้วย Terraform 
- **CI/CD Pipeline**: ระบบ Deploy อัตโนมัติด้วย GitHub Actions พร้อมความปลอดภัยระดับสูงผ่าน OIDC (ไม่เก็บ Access Key)

## 🛠 Tech Stack

- **Frontend**: Next.js 16, React 19, Tailwind CSS v4
- **Backend**: FastAPI (Python), Mangum (แปลง ASGI เป็น Lambda), Uvicorn
- **AI/LLM**: Groq API (llama-3.3-70b) และรองรับ Amazon Bedrock (Nova Models)
- **AWS Services**: 
  - **Compute**: AWS Lambda, Amazon API Gateway
  - **Storage**: Amazon S3 (สำหรับ Static Website & Memory Storage)
  - **CDN & Networking**: Amazon CloudFront, AWS Route 53, AWS ACM
  - **Management & Security**: AWS IAM, AWS STS, Amazon CloudWatch
- **DevOps**: Terraform, GitHub Actions

## 📁 โครงสร้างโปรเจกต์ (Project Structure)

```text
twin/
├── frontend/             # โค้ดส่วนหน้าเว็บ UI (Next.js)
├── backend/              # โค้ดส่วนระบบ Backend (FastAPI)
│   ├── server.py         # สมองหลักของระบบ (API Endpoint)
│   ├── lambda_handler.py # ตัวเชื่อมให้ FastAPI รันบน AWS Lambda
│   ├── context.py        # System Prompt เพื่อให้ AI รู้ว่าตัวเองเป็นใคร
│   ├── resources.py      # จัดการดึงข้อมูลส่วนตัว (JSON, Text, PDF)
│   └── data/             # แหล่งเก็บข้อมูลประวัติ, ทักษะ และสไตล์การพูด (Knowledge Base)
├── memory/               # โฟลเดอร์เก็บประวัติการสนทนา (สำหรับ Local)
└── README.md
```

## 🚀 วิธีการรันโปรเจกต์บนเครื่อง (Local Development)

### 1. ตั้งค่า Environment Variables
สร้างไฟล์ `.env` หรือตั้งค่าค่าตัวแปรสภาพแวดล้อมที่จำเป็น (เช่น `GROQ_API_KEY`) 

### 2. รัน Backend (FastAPI)
```bash
cd backend
pip install -r requirements.txt
uvicorn server:app --reload
```
API จะรันอยู่ที่ `http://localhost:8000`

### 3. รัน Frontend (Next.js)
```bash
cd frontend
npm install
npm run dev
```
หน้าเว็บแอปพลิเคชันจะพร้อมใช้งานที่ `http://localhost:3000`

## ☁️ การนำขึ้นระบบจริง (AWS Deployment)

ระบบนี้ถูกออกแบบให้ทำงานผ่าน CI/CD Pipeline อย่างสมบูรณ์:
- **Push-to-Deploy**: เมื่อทำการ `git push` ไปยัง Branch `main` ระบบ GitHub Actions จะสั่งให้ Terraform จัดการสร้าง/อัปเดต AWS Resources ต่างๆ
- **Zero Downtime & Safe**: โค้ด Backend จะถูกแพ็กและนำขึ้น AWS Lambda และ Frontend จะถูก Build เป็น Static Files นำไปวางใน Amazon S3 และเสิร์ฟผ่าน CloudFront อัตโนมัติ

## 👨‍💻 ผู้พัฒนา

**Petcharat Limprasert (Auaii)**  
Cloud Developer & AI Specialist (GenAI, RAG & Multi-agent Systems)  
eCloudvalley (ASEAN + HK)  
[GitHub Profile](https://github.com/auaii)
