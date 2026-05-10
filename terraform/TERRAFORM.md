# Terraform Infrastructure Guide

เอกสารฉบับนี้อธิบายรายละเอียดของไฟล์ Terraform ในโปรเจกต์นี้ ซึ่งใช้สำหรับสร้าง Infrastructure บน AWS สำหรับระบบ **Twin-RAG (AI Digital Twin)**

## 📂 โครงสร้างไฟล์
ไฟล์ในโฟลเดอร์ `terraform/` มีหน้าที่แบ่งแยกกันชัดเจนตามมาตรฐานของ Terraform:

### 1. `main.tf` (Core Logic)
เป็นไฟล์หลักที่กำหนด **Resources** ทั้งหมดที่จะถูกสร้างบน AWS:
- **S3 Buckets**: 
    - `frontend`: สำหรับเก็บไฟล์ Static Website (Next.js)
    - `memory`: สำหรับเก็บข้อมูล Conversation History (JSON)
- **IAM Role & Policies**: กำหนดสิทธิ์ให้ Lambda สามารถใช้งาน Bedrock และอ่าน/เขียน S3 ได้
- **Lambda Function**: ส่วนประมวลผล Backend (Python) ที่ต่อกับ AI Bedrock
- **API Gateway (HTTP API)**: ทำหน้าที่เป็นจุดรับส่งข้อมูลระหว่าง Frontend และ Lambda
- **CloudFront (CDN)**: ช่วยให้เข้าถึงเว็บไซต์ได้รวดเร็วผ่าน HTTPS ทั่วโลก
- **Route 53 & ACM** (Optional): จัดการ Domain Name และ SSL Certificate หากมีการตั้งค่า `use_custom_domain = true`

### 2. `variables.tf` (Input Definitions)
ใช้สำหรับกำหนด **ตัวแปร** ที่โปรเจกต์ต้องการ เพื่อให้สามารถนำไปใช้ซ้ำในสภาพแวดล้อมที่ต่างกัน (dev, test, prod) ได้:
- `project_name`: ชื่อโปรเจกต์ (เช่น twin)
- `environment`: สภาพแวดล้อม (dev, test, prod)
- `bedrock_model_id`: ID ของโมเดล AI ใน AWS Bedrock
- `use_custom_domain`: เลือกว่าจะใช้ Domain ส่วนตัวหรือไม่

### 3. `terraform.tfvars` (Value Assignments)
ไฟล์สำหรับ **กำหนดค่าจริง** ให้กับตัวแปรใน `variables.tf` โดยค่าในไฟล์นี้จะเป็นค่าพื้นฐานที่ใช้ในการ Deploy
- *หมายเหตุ*: ไฟล์นี้ควรระวังไม่ให้มีข้อมูลที่เป็นความลับ (Secret) รั่วไหล (แม้ว่าในโปรเจกต์นี้จะเก็บเพียงค่า Config ทั่วไปก็ตาม)

### 4. `outputs.tf` (Results)
ใช้สำหรับ **แสดงค่าสำคัญ** หลังจากที่ Terraform ทำงานเสร็จสิ้น เพื่อให้เรานำไปใช้งานต่อได้:
- `api_gateway_url`: URL สำหรับเชื่อมต่อ Backend
- `cloudfront_url`: URL สำหรับเข้าใช้งานเว็บไซต์ผ่าน Browser
- `s3_frontend_bucket`: ชื่อ Bucket ที่เราต้องอัปโหลดไฟล์ Frontend ไปวาง

### 5. `versions.tf` (Providers & Requirements)
กำหนดเวอร์ชั่นของ Terraform และ Provider ที่ต้องใช้:
- ระบุให้ใช้ **AWS Provider** เวอร์ชั่น 6.0 ขึ้นไป
- ตั้งค่า Region พื้นฐานสำหรับการจัดการ Certificate (us-east-1)

---

## 🏗️ แผนผังการทำงาน (Infrastructure Architecture)
เมื่อรัน Terraform นี้ ระบบจะสร้างสิ่งต่อไปนี้:

1. **User** เข้าใช้งานผ่าน **CloudFront URL**
2. **CloudFront** ดึงไฟล์หน้าเว็บจาก **S3 Frontend** มาแสดงผล
3. เมื่อ User พิมพ์คุยกับ AI, หน้าเว็บจะส่ง Request ไปที่ **API Gateway**
4. **API Gateway** สั่งงาน **Lambda Function**
5. **Lambda** ทำหน้าที่:
    - ดึงความจำเก่าจาก **S3 Memory**
    - ส่งคำถามไปประมวลผลที่ **AWS Bedrock (AI)**
    - เก็บคำตอบใหม่ลง **S3 Memory**
    - ส่งคำตอบกลับไปที่หน้าเว็บ

---

## 🚀 วิธีการใช้งานพื้นฐาน
คุณสามารถใช้สคริปต์ `scripts/deploy.sh` ในการจัดการทั้งหมด หรือรันเองตามลำดับนี้:

```bash
# 1. เริ่มต้นระบบ (ครั้งแรก)
terraform init

# 2. ตรวจสอบสิ่งที่จะถูกสร้าง
terraform plan

# 3. เริ่มสร้างทรัพยากรจริง
terraform apply -auto-approve
```
