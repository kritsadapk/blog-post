---
title: "สร้าง AI Chatbot ด้วย Node.js และ OpenAI API"
date: "2026-06-21"
category: "javascript"
image: "https://pixelplex.io/next/images/chatbot-development/header/chatbot-development-bg-1600.jpg"
imageAlt: "woman doing yoga meditation on brown parquet flooring"
---
# สร้าง AI Chatbot ด้วย Node.js และ OpenAI API

AI Chatbot กำลังกลายเป็นเครื่องมือสำคัญสำหรับธุรกิจ ไม่ว่าจะเป็นการตอบคำถามลูกค้า การช่วยค้นหาข้อมูล หรือการทำงานอัตโนมัติบนเว็บไซต์ ในบทความนี้เราจะพาคุณสร้าง AI Chatbot ด้วย Node.js และ OpenAI API แบบง่าย ๆ พร้อมตัวอย่างโค้ดที่สามารถนำไปต่อยอดใช้งานจริงได้

---

## AI Chatbot คืออะไร?

AI Chatbot คือโปรแกรมที่สามารถสนทนากับผู้ใช้งานโดยใช้ปัญญาประดิษฐ์ (AI) เพื่อทำความเข้าใจคำถามและตอบกลับอย่างเป็นธรรมชาติ

ตัวอย่างการใช้งาน:

* ตอบคำถามลูกค้าอัตโนมัติ
* Chatbot บนเว็บไซต์
* ผู้ช่วยภายในองค์กร
* ระบบค้นหาข้อมูลจากเอกสาร
* AI Customer Support

---

## สิ่งที่ต้องเตรียม

### 1. ติดตั้ง Node.js

ดาวน์โหลด Node.js เวอร์ชันล่าสุดจากเว็บไซต์ทางการ

```bash
node -v
npm -v
```

---

### 2. สร้างโปรเจกต์

```bash
mkdir ai-chatbot
cd ai-chatbot

npm init -y
```

---

### 3. ติดตั้ง Package

```bash
npm install openai dotenv express cors
```

---

## ขอ API Key จาก OpenAI

สร้างไฟล์ `.env`

```env
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxx
```

---

## สร้าง Backend API

สร้างไฟล์ `server.js`

```javascript
require("dotenv").config();

const express = require("express");
const cors = require("cors");
const OpenAI = require("openai");

if (!process.env.OPENAI_API_KEY) {
  console.error("OPENAI_API_KEY is not set");
  process.exit(1);
}

const app = express();

app.use(cors());
app.use(express.json());

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
});

app.post("/chat", async (req, res) => {
  try {
    const { message } = req.body;

    if (!message || typeof message !== "string") {
      return res.status(400).json({
        error: "message is required",
      });
    }

    const response = await openai.responses.create({
      model: "gpt-5",
      input: message,
    });

    res.json({
      reply: response.output_text,
    });
  } catch (error) {
    console.error("OpenAI API error:", error);

    const status = error.status || 500;
    res.status(status).json({
      error: error.message || "Something went wrong",
    });
  }
});

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

---

## ทดสอบ API

รันเซิร์ฟเวอร์

```bash
node server.js
```

ทดสอบด้วย curl

```bash
curl -X POST http://localhost:3000/chat \
-H "Content-Type: application/json" \
-d '{"message":"สวัสดี"}'
```

ผลลัพธ์

```json
{
  "reply": "สวัสดีครับ มีอะไรให้ช่วยไหมครับ?"
}
```

---

## สร้างหน้าเว็บ Chat

สร้างไฟล์ `index.html`

```html
<!DOCTYPE html>
<html>
<head>
  <title>AI Chatbot</title>
</head>
<body>

<h1>AI Chatbot</h1>

<input id="message" />
<button onclick="sendMessage()">Send</button>

<div id="result"></div>

<script>
async function sendMessage() {
  const input =
    document.getElementById("message");
  const message = input.value.trim();

  if (!message) return;

  const result =
    document.getElementById("result");

  result.innerHTML += `
    <p><b>You:</b> ${message}</p>
  `;

  try {
    const response = await fetch(
      "http://localhost:3000/chat",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          message
        })
      }
    );

    if (!response.ok) {
      const err = await response.json()
        .catch(() => ({}));
      throw new Error(
        err.error || response.statusText
      );
    }

    const data = await response.json();

    result.innerHTML += `
      <p><b>AI:</b> ${data.reply}</p>
    `;
  } catch (error) {
    result.innerHTML += `
      <p><b>Error:</b> ${error.message}</p>
    `;
  }

  input.value = "";
}
</script>

</body>
</html>
```

---

## เพิ่ม Memory ให้ Chatbot

หากต้องการให้ AI จำบทสนทนาได้ สามารถเก็บข้อความใน Array

```javascript
const messages = [];

messages.push({
  role: "user",
  content: message,
});
```

จากนั้นส่งประวัติการสนทนาไปพร้อมกัน

```javascript
const response = await openai.responses.create({
  model: "gpt-5",
  input: messages.map((m) => ({
    role: m.role,
    content: m.content,
  })),
});
```

สำหรับ Production ควรเก็บข้อมูลใน:

* MongoDB
* PostgreSQL
* Redis

---

## เพิ่ม System Prompt

กำหนดบุคลิกของ Chatbot

```javascript
const response = await openai.responses.create({
  model: "gpt-5",
  instructions: `
    คุณเป็นพนักงานขายของบริษัท
    ตอบภาษาไทยสุภาพ
    เน้นบริการทำเว็บไซต์และการตลาดออนไลน์
  `,
  input: message,
});
```

---

## ต่อยอดสู่ AI Chatbot สำหรับธุรกิจ

หลังจากสร้าง Chatbot พื้นฐานแล้ว สามารถพัฒนาเพิ่มเติมได้ เช่น

### เชื่อมต่อข้อมูลบริษัท

* FAQ
* เอกสาร PDF
* ข้อมูลสินค้า
* ฐานความรู้ภายในองค์กร

### เชื่อมต่อ LINE OA

* ตอบลูกค้าอัตโนมัติ
* ส่งใบเสนอราคา
* ตรวจสอบสถานะคำสั่งซื้อ

### เชื่อมต่อเว็บไซต์

* Live Chat
* Lead Generation
* Customer Support

### สร้าง AI Agent

* จองคิว
* ค้นหาข้อมูล
* สรุปรายงาน
* สั่งงานระบบภายใน

---

## Best Practices สำหรับ Production

### จำกัดจำนวนข้อความ

ไม่ควรส่งประวัติการสนทนาทั้งหมดทุกครั้ง เพราะจะทำให้ Token เพิ่มขึ้นและค่าใช้จ่ายสูงขึ้น

ตัวอย่าง:

```javascript
const recentMessages = messages.slice(-20);
```

### ป้องกัน Rate Limit

ใช้ Middleware จำกัดจำนวน Request

```javascript
npm install express-rate-limit
```

```javascript
const rateLimit = require("express-rate-limit");

app.use(
  rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 100,
  })
);
```

### เก็บ API Key ให้ปลอดภัย

* ห้ามส่ง API Key ไปยัง Frontend
* เก็บไว้ใน Environment Variables
* ใช้ Backend เป็นตัวกลางเรียก OpenAI API

---

## สรุป

การสร้าง AI Chatbot ด้วย Node.js และ OpenAI API เป็นจุดเริ่มต้นที่ดีสำหรับนักพัฒนาที่ต้องการเข้าสู่โลกของ AI Application Development โดยใช้เวลาเพียงไม่กี่นาทีก็สามารถสร้าง Chatbot ที่ตอบคำถามได้จริง

เมื่อเข้าใจพื้นฐานแล้ว คุณสามารถต่อยอดไปสู่การสร้าง AI Agent, ระบบ RAG, Chatbot ที่เชื่อมต่อฐานข้อมูล หรือระบบ Customer Support อัตโนมัติสำหรับธุรกิจได้อย่างเต็มรูปแบบ

