# Thailand-Province 🇹🇭

ชุดข้อมูลฐานข้อมูลที่อยู่ของประเทศไทย (Thailand Address Database) สำหรับ **PostgreSQL** โดยเฉพาะ ประกอบไปด้วยข้อมูลภูมิศาสตร์ที่ครอบคลุมตั้งแต่ระดับภาคไปจนถึงรหัสไปรษณีย์ โครงสร้างตารางถูกออกแบบมาให้มีความสัมพันธ์กัน (Relational Database) พร้อม Foreign Key รองรับการนำไปใช้งานในแอปพลิเคชันหรือเว็บไซต์ของคุณได้ทันที

## 📦 ข้อมูลที่รวบรวมไว้

| ระดับ | ตาราง | จำนวน |
| --- | --- | ---: |
| ภาค | `geography` | 6 ภาค |
| จังหวัด | `province` | 77 จังหวัด |
| อำเภอ/เขต | `district` | 928 อำเภอ/เขต (878 อำเภอ + 50 เขตของกรุงเทพมหานคร) |
| ตำบล/แขวง | `subDistrict` | 7,456 ตำบล/แขวง |
| รหัสไปรษณีย์ | `zipcode` | 7,456 แถว (ทุกตำบลมีรหัสไปรษณีย์, รหัสไม่ซ้ำ 955 รหัส) |

รหัสพื้นที่ (`*_CODE`) อ้างอิงตามรหัสของกรมการปกครอง: จังหวัด 2 หลัก, อำเภอ 4 หลัก, ตำบล 6 หลัก

---

## 🗄️ โครงสร้างฐานข้อมูล (Database Schema)

```
geography (ภาค)
  └── province (จังหวัด)                    GEO_ID          → geography
        └── district (อำเภอ/เขต)             PROVINCE_ID     → province
              └── subDistrict (ตำบล/แขวง)     DISTRICT_ID     → district
                    └── zipcode (รหัสไปรษณีย์)  SUB_DISTRICT_ID → subDistrict
```

| ตาราง | คอลัมน์ |
| --- | --- |
| `geography` | `GEO_ID` (PK), `GEO_NAME` |
| `province` | `PROVINCE_ID` (PK), `PROVINCE_CODE`, `PROVINCE_NAME`, `GEO_ID` |
| `district` | `DISTRICT_ID` (PK), `DISTRICT_CODE`, `DISTRICT_NAME`, `GEO_ID`, `PROVINCE_ID` |
| `subDistrict` | `SUB_DISTRICT_ID` (PK), `SUB_DISTRICT_CODE`, `SUB_DISTRICT_NAME`, `DISTRICT_ID`, `PROVINCE_ID`, `GEO_ID` |
| `zipcode` | `ZIPCODE_ID` (PK), `SUB_DISTRICT_CODE`, `PROVINCE_ID`, `DISTRICT_ID`, `SUB_DISTRICT_ID`, `ZIPCODE` |

> หมายเหตุ: PostgreSQL จะแปลงชื่อตารางและคอลัมน์ที่ไม่ได้ใส่เครื่องหมายคำพูดให้เป็นตัวพิมพ์เล็ก เช่น `subDistrict` จะกลายเป็น `subdistrict` และ `GEO_ID` จะกลายเป็น `geo_id`

---

## 🚀 วิธีการติดตั้งและใช้งาน (Installation)

คุณสามารถนำเข้าไฟล์ SQL นี้ไปยังฐานข้อมูล PostgreSQL ของคุณได้ผ่าน Command Line (Terminal) หรือใช้เครื่องมือจัดการฐานข้อมูล เช่น pgAdmin, DBeaver หรือ TablePlus

> ⚠️ สคริปต์จะ `DROP TABLE IF EXISTS` ตาราง `geography`, `province`, `district`, `subDistrict` และ `zipcode` ก่อนสร้างใหม่ ควรนำเข้าในฐานข้อมูลหรือ schema ที่ไม่มีตารางชื่อซ้ำ

**การนำเข้าผ่าน Command Line (psql):**

```bash
# สร้างฐานข้อมูล (ถ้ายังไม่มี)
createdb -U your_username your_database_name

# นำเข้าไฟล์ SQL ไปยังฐานข้อมูลที่ต้องการ
psql -U your_username -d your_database_name -f thailand-address-postgres.sql
```

---

## 🔎 ตัวอย่างการใช้งาน (Example Queries)

**ค้นหาที่อยู่เต็มจากรหัสไปรษณีย์:**

```sql
SELECT z.zipcode,
       s.sub_district_name AS tambon,
       d.district_name     AS amphoe,
       p.province_name     AS province,
       g.geo_name          AS region
FROM zipcode z
JOIN subdistrict s ON s.sub_district_id = z.sub_district_id
JOIN district    d ON d.district_id     = s.district_id
JOIN province    p ON p.province_id     = s.province_id
JOIN geography   g ON g.geo_id          = p.geo_id
WHERE z.zipcode = '10200';
```

**ดึงรายชื่ออำเภอของจังหวัด (ใช้ทำ dropdown):**

```sql
SELECT district_id, district_name
FROM district
WHERE province_id = (SELECT province_id FROM province WHERE province_name = 'เชียงใหม่')
ORDER BY district_code;
```
