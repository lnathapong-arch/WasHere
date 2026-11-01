# WasHere — คู่มือ Deploy ขึ้น Firebase Hosting

โครงสร้างแพ็คเกจ

```
washere/
├─ firebase.json      ← ตั้งค่า Hosting (โฟลเดอร์ public, header, cache)
├─ .firebaserc        ← ใส่ Project ID ของคุณ (แก้ YOUR_PROJECT_ID)
└─ public/            ← ไฟล์แอปทั้งหมดที่จะถูกอัปโหลด
   ├─ index.html  manifest.json  sw.js
   └─ icon-192.png  icon-512.png  icon-maskable-512.png  apple-touch-icon.png  favicon.png
```

## ส่วนที่ 1: ตั้งค่าบน Firebase Console

1. เปิด https://console.firebase.google.com แล้วล็อกอินด้วยบัญชี Google
2. กด **Create a project** (หรือ Add project)
3. ตั้งชื่อโปรเจกต์ เช่น `WasHere` แล้วดู **Project ID** ที่ขึ้นใต้ช่องชื่อ (เช่น `washere-a1b2c`) — **จดไว้** จะใช้ในขั้นตอนที่ 2
4. หน้า Google Analytics จะปิดก็ได้ (แอปนี้ไม่จำเป็น) แล้วกด **Create project** รอจนขึ้นว่าพร้อม กด **Continue**
5. เมนูซ้าย เลือก **Build → Hosting** (ถ้าไม่เห็น ให้กด "All products" แล้วหา Hosting)
6. กด **Get started** แล้วกด Next/Continue ไปตามหน้าจอจนถึงขั้นสุดท้าย (ขั้น Deploy ผ่านได้เลย เพราะเราจะ deploy ด้วยคำสั่งเอง)
7. แพ็กเกจที่ใช้ได้ฟรีคือ **Spark plan** (ค่าเริ่มต้นของโปรเจกต์ใหม่) เพียงพอสำหรับแอปนี้ ไม่ต้องใส่บัตรเครดิต

## ส่วนที่ 2: ติดตั้งเครื่องมือบนคอมพิวเตอร์

1. ติดตั้ง **Node.js** (เวอร์ชัน LTS) จาก https://nodejs.org
2. เปิด Terminal (Windows: PowerShell / Mac: Terminal) แล้วรัน

```
npm install -g firebase-tools
firebase login
```

   เบราว์เซอร์จะเปิดให้อนุญาต — เลือกบัญชี Google เดียวกับที่สร้างโปรเจกต์

## ส่วนที่ 3: Deploy

1. แตก zip แล้วเปิดไฟล์ `.firebaserc` (ไฟล์ซ่อน) แก้ `YOUR_PROJECT_ID` เป็น Project ID ที่จดไว้
   - หรือใช้คำสั่ง `firebase use --add` แล้วเลือกโปรเจกต์แทนก็ได้
2. เข้าโฟลเดอร์ `washere` ใน Terminal แล้วรัน

```
cd washere
firebase deploy --only hosting
```

   ห้ามรัน `firebase init` ซ้ำ เพราะจะเขียนทับ `firebase.json`
3. เมื่อสำเร็จจะขึ้น **Hosting URL** เช่น `https://washere-a1b2c.web.app` (ใช้ HTTPS อัตโนมัติ)

## ส่วนที่ 4: ติดตั้งลงมือถือ

- **Android (Chrome):** เปิด URL → เมนู ⋮ → **ติดตั้งแอป / Add to Home screen**
- **iPhone (Safari เท่านั้น):** เปิด URL → ปุ่ม Share → **Add to Home Screen**
- เปิดแอปครั้งแรกแล้วกด "อนุญาต" ตำแหน่ง (Location)
- iPhone: ถ้าเคยกดไม่อนุญาต ไปที่ Settings → Privacy & Security → Location Services เปิดให้ Safari/แอปนี้

## ส่วนที่ 5: อัปเดตแอปภายหลัง

แก้ไฟล์ใน `public/` แล้วรัน `firebase deploy --only hosting` อีกครั้ง
หากแก้ไฟล์ที่แคชไว้ ให้เปลี่ยน `washere-v1` ใน `sw.js` เป็น `washere-v2` เพื่อบังคับให้เครื่องผู้ใช้โหลดของใหม่

## ส่วนที่ 6 (ไม่บังคับ): เชื่อมฐานข้อมูล Cloud

ตอนนี้ข้อมูลเก็บในเครื่อง (IndexedDB) ถ้าต้องการซิงค์ข้ามเครื่อง: Console → **Build → Authentication → Get started** เปิด Sign-in method ที่ต้องการ และ **Build → Firestore Database → Create database** แล้วบอกผมให้ต่อโค้ดส่วนซิงค์ให้ (ตัวแปร `SYNC_URL` ใน `index.html` เป็นจุดเชื่อมไว้แล้ว)

## แก้ปัญหาที่พบบ่อย

| อาการ | วิธีแก้ |
|---|---|
| `firebase: command not found` | ปิดแล้วเปิด Terminal ใหม่หลังติดตั้ง Node.js |
| Deploy ไม่เจอโปรเจกต์ | ตรวจ Project ID ใน `.firebaserc` ให้ตรงกับ Console |
| GPS ไม่ทำงาน | ต้องเปิดผ่าน `https://` เท่านั้น และอนุญาต Location |
| ไอคอนไม่เปลี่ยน | ลบแอปจากหน้าจอหลักแล้วติดตั้งใหม่ |
