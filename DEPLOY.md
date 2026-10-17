# TKO iMATOMs Client MKT · ขั้นตอนติดตั้ง

ระบบมี 3 ส่วน

| ไฟล์ | อยู่ที่ไหน | หมายเหตุ |
|---|---|---|
| `Code.gs` | Apps Script ของ Google Sheet | ห้ามขึ้น GitHub |
| `RefData.gs` | Apps Script ของ Google Sheet | มีข้อมูล P&L AFM 2021 · ห้ามขึ้น GitHub |
| `index.html` | GitHub Pages | ไม่มีข้อมูลลูกค้าในไฟล์ ขึ้น repo ได้ |

ทำทุกขั้นตอนด้วยบัญชี **imatomsapps1976@gmail.com** เพราะอีเมล OTP จะส่งจากบัญชีที่ Deploy

---

## 1. สร้าง Google Sheet และวางสคริปต์

1. เข้า Google Sheets → สร้างชีตใหม่ ตั้งชื่อ `TKO_iMATOMsClientMKT_DB`
2. เมนู **ส่วนขยาย › Apps Script**
3. ลบโค้ดเดิมในไฟล์ `Code.gs` แล้ววางเนื้อหาไฟล์ `Code.gs` ทั้งหมด
4. กด **+ › Script** ตั้งชื่อ `RefData` แล้ววางเนื้อหาไฟล์ `RefData.gs`
5. กด **บันทึก** (ไอคอนแผ่นดิสก์)

## 2. รัน setup (ครั้งเดียว)

1. ที่แถบด้านบน เลือกฟังก์ชัน **setup** → กด **Run**
2. Google จะขอสิทธิ์ → เลือกบัญชี → **Advanced › Go to project (unsafe)** → **Allow**
   (ขึ้นคำเตือนเพราะเป็นสคริปต์ที่เราเขียนเอง ไม่ได้ผ่านการรับรองจาก Google)
3. กลับไปที่ชีต จะมีแท็บ: `Users`, `Sites`, `Logs`, `Actions`, `Config`, `Ref_AFM2021` (48 ไซต์)
4. แนะนำ: เลือกฟังก์ชัน **testEmail** → Run เพื่อเช็กว่าส่งอีเมลได้

## 3. Deploy เป็น Web App

1. กด **Deploy › New deployment**
2. ไอคอนเฟือง → เลือก **Web app**
3. ตั้งค่า
   - Execute as: **Me (imatomsapps1976@gmail.com)**
   - Who has access: **Anyone**
4. กด **Deploy** → คัดลอก **Web app URL** (ลงท้ายด้วย `/exec`)

> "Anyone" หมายถึงหน้าเว็บเรียก API ได้ แต่ข้อมูลทุกอย่างต้องใช้ token ที่ได้จากการยืนยัน OTP ทางอีเมลที่อยู่ในแท็บ Users เท่านั้น

## 4. ขึ้น GitHub Pages

1. เปิด `index.html` แก้บรรทัด
   ```js
   const API_URL = '';
   ```
   เป็น URL จากข้อ 3 เช่น
   ```js
   const API_URL = 'https://script.google.com/macros/s/xxxx/exec';
   ```
2. สร้าง repo ใหม่ เช่น `tko-imatoms-client-mkt` → อัปโหลด **เฉพาะ `index.html`**
3. **Settings › Pages** → Source: `Deploy from a branch` → Branch: `main` / `(root)` → Save
4. รอ 1–2 นาที แล้วเปิด `https://<username>.github.io/tko-imatoms-client-mkt/`
5. ใส่อีเมล imatomsapps1976@gmail.com → รับรหัส 6 หลักทางอีเมล → เข้าระบบ

## เพิ่มผู้ใช้ทีมในอนาคต

หน้า **ตั้งค่า › ผู้ใช้งานระบบ** (เห็นเฉพาะ Admin) → **+ เพิ่มผู้ใช้** → ใส่อีเมล ชื่อ บทบาท → **บันทึกผู้ใช้**
หรือเพิ่มแถวในแท็บ `Users` ของชีตโดยตรง (Active = `Yes`)

- **Admin** แก้ไขผู้ใช้ได้ · **Member** ใช้งานทั่วไป
- ปิด "ใช้งาน" แล้ว เครื่องที่เคยเข้าระบบจะถูกออกจากระบบในการใช้งานครั้งถัดไป

## แก้โค้ด Apps Script ภายหลัง

หลังแก้ `Code.gs` ต้อง **Deploy › Manage deployments › ✏️ › Version: New version › Deploy**
URL เดิมใช้ต่อได้ ไม่ต้องแก้ `index.html`

## ปัญหาที่พบบ่อย

| อาการ | วิธีแก้ |
|---|---|
| หน้าเว็บขึ้น "ยังไม่ได้ตั้งค่า" | ยังไม่ได้ใส่ `API_URL` ใน index.html |
| "เชื่อมต่อ API ไม่ได้" | ตรวจว่า Deploy แบบ Anyone และใช้ URL `/exec` ล่าสุด |
| ไม่ได้รับอีเมล OTP | ดูโฟลเดอร์ Spam · รัน `testEmail` · บัญชี Gmail ส่งได้ราว 100 ฉบับ/วัน |
| "อีเมลนี้ไม่มีสิทธิ์" | อีเมลยังไม่อยู่ในแท็บ Users หรือ Active ไม่ใช่ Yes |
| ข้อมูล Ref เพี้ยน | รัน `setup` ใหม่ได้ ระบบจะล้างและนำเข้า Ref_AFM2021 ใหม่ (ข้อมูลที่ทีมกรอกในแท็บอื่นไม่หาย) |
