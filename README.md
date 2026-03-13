# Thailand-Province 🇹🇭

ชุดข้อมูลฐานข้อมูลที่อยู่ของประเทศไทย (Thailand Address Database) สำหรับ **PostgreSQL** โดยเฉพาะ ประกอบไปด้วยข้อมูลภูมิศาสตร์ที่ครอบคลุมตั้งแต่ระดับภาคไปจนถึงรหัสไปรษณีย์ โครงสร้างตารางถูกออกแบบมาให้มีความสัมพันธ์กัน (Relational Database) รองรับการนำไปใช้งานในแอปพลิเคชันหรือเว็บไซต์ของคุณได้ทันที

## 📦 ข้อมูลที่รวบรวมไว้
* **ภาค (Geographies):** 6 ภาค
* **จังหวัด (Provinces):** 77 จังหวัด
* **อำเภอ/เขต (Amphures):** 928 อำเภอ
* **ตำบล/แขวง (Districts):** 7,410 ตำบล
* **รหัสไปรษณีย์ (Zip Codes):** ผูกติดกับตารางตำบล

---

## 🗄️ โครงสร้างฐานข้อมูล (Database Schema)

โครงสร้างของฐานข้อมูลประกอบด้วย 4 ตารางหลักที่มีการทำ Foreign Key เชื่อมต่อกัน ดังนี้:

1. `geographies` - ข้อมูลภาค
2. `provinces` - ข้อมูลจังหวัด (อ้างอิง `geography_id`)
3. `amphures` - ข้อมูลอำเภอ (อ้างอิง `province_id`)
4. `districts` - ข้อมูลตำบลและรหัสไปรษณีย์ (อ้างอิง `amphure_id`)

---

## 🚀 วิธีการติดตั้งและใช้งาน (Installation)

คุณสามารถนำเข้าไฟล์ SQL นี้ไปยังฐานข้อมูล PostgreSQL ของคุณได้ผ่าน Command Line (Terminal) หรือใช้เครื่องมือจัดการฐานข้อมูล เช่น pgAdmin, DBeaver, หรือ TablePlus

**การนำเข้าผ่าน Command Line (psql):**

```bash
# นำเข้าไฟล์ SQL ไปยังฐานข้อมูลที่ต้องการ
psql -U your_username -d your_database_name -f path/to/thailand_province.sql
