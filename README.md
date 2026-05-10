# 🤖 AI Digital Twin (Auaii) - Serverless RAG Edition

โปรเจกต์ **AI Digital Twin** เป็นแชทบอทอัจฉริยะที่ทำหน้าที่เป็นตัวแทนของคุณ Auaii (Petcharat Limprasert) — Cloud Developer & AI Specialist โดย AI จะเรียนรู้ข้อมูลจากประวัติการทำงาน, Resume, LinkedIn, และสไตล์การพูด เพื่อให้สามารถตอบคำถามแทนตัวคุณได้เสมือนจริงผ่านเทคโนโลยี RAG (Retrieval-Augmented Generation)

โปรเจกต์นี้ถูกออกแบบมาให้ทำงานบน **AWS แบบ Serverless 100%** เพื่อความประหยัด ทนทาน และสเกลได้อัตโนมัติ

---

## ✨ ฟีเจอร์หลัก (Features)

- **Personalized AI (RAG)**: ตอบคำถามเกี่ยวกับประวัติและความเชี่ยวชาญได้อย่างแม่นยำ โดยใช้ข้อมูลจริงจาก Knowledge Base
- **Multi-Environment**: รองรับการแยกสภาพแวดล้อม `dev`, `test`, `prod` ผ่าน Terraform Workspaces
- **Advanced LLM**: ขับเคลื่อนด้วยโมเดลล่าสุด **Amazon Nova Micro** ผ่าน Amazon Bedrock
- **Cloud-Native Architecture**:
    - **Backend**: Python 3.13 บน AWS Lambda
    - **Frontend**: Next.js 16 (Static Export) บน Amazon S3 + CloudFront
    - **Memory**: ระบบจำบทสนทนาเก็บข้อมูลลง S3 แยกตาม Session
- **Automated Deployment**: สคริปต์รันคำสั่งเดียวสร้างของครบทั้งระบบ (Zero-to-Hero)

---

## 🛠 Tech Stack

- **Core**: Python 3.13, TypeScript
- **Frontend**: Next.js 16, Tailwind CSS v4
- **AI/LLM**: Amazon Bedrock (Nova Micro)
- **Infrastructure**: Terraform v1.0+
- **AWS Services**: 
  - **Compute**: AWS Lambda, Amazon API Gateway (HTTP API)
  - **Storage**: Amazon S3 (Website & Memory)
  - **CDN**: Amazon CloudFront
  - **Security**: AWS IAM (Least Privilege)
- **Tooling**: `uv` (Python Package Manager), Docker (สำหรับการ Build Lambda Package)

---

## 📁 โครงสร้างโปรเจกต์ (Project Structure)

```text
twin/
├── frontend/             # Next.js Application (UI)
├── backend/              # Python FastAPI (API)
│   ├── deploy.py         # สคริปต์สำหรับ Build Lambda .zip (ใช้ Docker)
│   └── data/             # Knowledge Base (Resume, LinkedIn, etc.)
├── terraform/            # Infrastructure as Code (AWS Resources)
│   └── TERRAFORM.md      # คู่มือเจาะลึกส่วน Infrastructure
├── scripts/              # Automation Scripts
│   ├── deploy.sh         # สคริปต์สร้างระบบทั้งหมด (Apply)
│   └── destroy.sh        # สคริปต์ลบระบบทั้งหมด (Destroy)
└── README.md
```

---

## 🚀 วิธีการติดตั้งและรันระบบ (Quick Start)

### 1. เตรียมความพร้อม (Prerequisites)
- ติดตั้ง **Terraform**, **AWS CLI**, **Docker** และ **Node.js**
- รัน `aws configure` เพื่อตั้งค่าสิทธิ์การเข้าถึง AWS
- ขอสิทธิ์ใช้งานโมเดล **Nova Micro** ในหน้า AWS Bedrock Console

### 2. การ Deploy ขึ้น AWS (Automated)
คุณสามารถรันคำสั่งเดียวเพื่อสร้างระบบทั้งหมดได้เลย:

```bash
# ให้สิทธิ์สคริปต์ (ทำครั้งเดียว)
chmod +x scripts/*.sh

# Deploy ไปยัง environment ที่ต้องการ (dev, test, หรือ prod)
./scripts/deploy.sh dev
```
*สคริปต์จะทำการ: Build Backend -> สร้าง Infrastructure -> Build Frontend -> อัปโหลดขึ้น S3*

### 3. การลบทรัพยากรทิ้ง (Clean up)
เพื่อป้องกันค่าใช้จ่ายส่วนเกินเมื่อไม่ได้ใช้งาน:

```bash
./scripts/destroy.sh dev
```

---

## 👨‍💻 ผู้พัฒนา

**Petcharat Limprasert (Auaii)**  
Cloud Developer & AI Specialist (GenAI, RAG & Multi-agent Systems)  
[GitHub Profile](https://github.com/auaii)

---
*หมายเหตุ: โปรเจกต์นี้อ้างอิงและพัฒนาต่อยอดจากหลักสูตร [ed-donner/production](https://github.com/ed-donner/production)*
