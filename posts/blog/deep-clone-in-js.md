---
title: "Deep Clone ใน Javascript"
date: "2024-04-20"
category: "javascript"
image: "/posts/1_qT25TPSPtov_LFjTYcjM2A.webp"
imageAlt: "woman doing yoga meditation on brown parquet flooring"
---

# Deep Clone !!

โคลน คือ ดินร่วนปนดินหรือดินผสมกับน้ำ มันมักจะก่อตัวหลังจากฝนตกหรือใกล้แหล่งน้ำ ไม่ใช่ละ!! (Clone) คือ ขั้นตอนซึ่งพืชต้นใหม่สามารถถูกสร้างขึ้นจากกิ่งได้ และในทางคอมพิวเตอร์คือ สิ่งที่ทำขึ้นให้เหมือนกับของเดิม หรือเหมือนกับต้นฉบับ … กว่าจะรู้เรื่อง!!!

## อะไรกันแน่ 

พอเราได้เรื่อง Clone คืออะไร แล้ว Deep แปลว่า ลึก,ลึกซึ้ง เพราะฉะนั้นจับสองคำมาร่วมกัน Deep Clone ก็คือ การทำให้เหมือนกันแบบไม่ขึ้นต่อกัน นั่นเอง.. — แปลเอง ไม่มีที่มา ขออภัย–

## โค้ด

```tsx
let array = [1,2,3];
let arrayCopy = array; 
arrayCopy[0] = '👻'; // แก้ไข array ในตำแหน่งที่ 1
console.log(arrayCopy);  
console.log(array);
/* ผลลัพท์
["👻",2,3] 
["👻",2,3] 
*/
```

จากตัวอย่าง เราจะเห็นว่า การใช้วิธี a=b เมื่อเราแก้ arrayCopy ในตำแหน่งที่ 1 จะทำให้ข้อมูล array เปลี่ยนไปด้วย มันคือการไปแก้ที่ address ที่ในหน่วยความจำตำแหน่งเดียวกัน นั่นเอง เช่นเดียวกันกับตัวอย่างนี้

```tsx
let obj1 = {name: 'Nut', game:['DOTA2', 'ROV']}
let obj2 = {...obj1}   // Object.assign({}, obj1);
obj1.game[0] = 'FREE FIRE' ;
console.log(obj1)
console.log(obj2)
/* ผลลัพท์
{
  "name": "Nut",
  "game": [
    "FREE FIRE",
    "ROV"
  ]
}
{
  "name": "Nut",
  "game": [
    "FREE FIRE",
    "ROV"
  ]
}
*/
```

> ก่อนอื่นใน JS ES5 เราจะใช้ Object.assign() ในการจะ Copy Object ชุดนึง และใน ES6 ก็จะใช้ spread operator แทนแบบเดิม

จากตัวอย่าง ด้านบน คือการใช้ spread operator โดยให้ obj2 โคลนจาก obj1 และแก้ไขตรง obj1.game[0] ปรากฎว่า ผลลัพท์ที่ได้ obj2.game[0] ก็มีการเปลี่ยนแปลงด้วย เพราะฉะนั้นแล้ว ทำอย่างไร ให้ทั้ง 2 object ไม่ขึ้นต่อกัน นั่นคือการใช้ deep clone ดังนี้

```tsx
let obj1 = {name: 'Nut', game:['DOTA2', 'ROV']}
let obj2 = JSON.parse(JSON.stringify(obj1));
obj1.game[0] = 'FREE FIRE'
// ลอง console.log() มาดูนะครับ
```

ตัวอย่างโค้ด คือการแปลงจาก object เป็น string แล้วจาก string ก็เป็น json อีกที แต่ๆ แต่ๆ ปัญหาเหล่านี้จะหายไป ถ้าใช้ Lib พวก lodash ครับ
