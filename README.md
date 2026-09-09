```
วิธีการย้ายจาก Phabricator ไปยัง Phorge
กระบวนการย้ายจาก Phabricator ไปยัง Phorge มีความคล้ายคลึงกันมาก กระบวนการอัพเกรดยกเว้นว่าคุณจะเปลี่ยนไปใช้รีโมทอื่น

สมมติฐานและข้อกําหนด
เอกสารนี้ตั้งสมมติฐานหลายประการเกี่ยวกับการติดตั้งที่มีอยู่แล้วของคุณ:

เราถือว่าคุณได้ติดตั้ง Phabricator โดยใช้คําแนะนําอย่างเป็นทางการที่ได้รับการสนับสนุน https://secure.phabricator.com/book/phabricator/article/installation_guide/● คือ เราถือว่าคุณเคยใช้ คอมไพล์โคลน หากต้องการรับโค้ดจากมิเรอร์ GitHub ของ Phacility หรือที่เก็บข้อมูลได้ที่ secure.phabricator.com●
เราถือว่าคุณไม่ได้ทําการเปลี่ยนแปลงภายในเครื่อง ("ส้อม") กับโค้ด หากคุณได้เพิ่มไลบรารีก็ไม่เป็นไร แต่เราถือว่าไม่มีการเปลี่ยนแปลงใดๆ ฟาบริเคเตอร์ และ นักอาร์คานิสต์ ฐานรหัส
หากคุณได้ทําการเปลี่ยนแปลงการติดตั้งในเครื่องแล้ว เราถือว่าคุณพอใจเพียงพอ คอมไพล์ เพื่อปรับตัว

กระบวนการ
เราจะไม่เปลี่ยนชื่อไดเร็กทอรีใดๆ ที่นี่ - ในขณะที่การติดตั้งใหม่จะมี ธอช/ และ นักอาร์คานิสต์/ ไดเรกทอรีคุณจะเก็บไว้ ช่างประกอบ/ และ นักอาร์คานิสต์/● ด้วยวิธีนี้ สคริปต์และเครื่องมือที่มีอยู่ของคุณควรทํางานต่อไป นี่คือสิ่งที่ควรคํานึงถึงในระหว่างการย้ายข้อมูล หากคุณต้องการเปลี่ยนชื่อไดเร็กทอรีเหล่านี้ในอนาคต คุณสามารถทําการเปลี่ยนแปลงนี้ได้ด้วยตัวเองหากคุณรู้สึกสบายใจ

นอกจากนี้ยังมีการอ้างอิงภายในมากมายเกี่ยวกับ ฟาบริเคเตอร์ เทอม - เราจะเก็บสิ่งเหล่านั้นไว้เช่นกันด้วยเหตุผลด้านความเข้ากันได้

ค้นหาเวอร์ชันปัจจุบันของคุณ
ขั้นแรก คุณจะต้องตรวจสอบการติดตั้ง Phabricator เวอร์ชันปัจจุบันของคุณ Phabricator และ Phorge ใช้ Git คอมมิตแฮชเป็น "เวอร์ชัน" คุณสามารถดูเวอร์ชันปัจจุบันของคุณได้ ฟาบริเคเตอร์ และ นักอาร์คานิสต์ แพ็คเกจโดยไปที่การติดตั้งของคุณ /กําหนดค่า หน้า

คุณจะต้องได้รับข้อมูลล่าสุดอย่างสมเหตุสมผลก่อนย้ายข้อมูล - ดู บันทึกการเปลี่ยนแปลงของ Phabricator สําหรับรายละเอียด เราขอแนะนําอย่างยิ่งให้อัปเดตอย่างน้อยเป็นเวอร์ชันต้นปี 2022

ก่อนที่จะพิจารณาการย้ายไปยัง Phorge Phabricator ของคุณควรได้รับข้อมูลล่าสุดและมี อย่างน้อย ความมุ่งมั่นต้นน้ําเหล่านี้:

พื้นที่เก็บข้อมูล	คอมมิตแฮช	วันที่	ความคิดเห็น
ฟาบริเคเตอร์	9426765a2c6a	14 มิ.ย.2565	แบนวัตถุ "RemarkupValue" เมื่อตั้งค่าค่าเริ่มต้นของฟิลด์สําหรับแบบฟอร์มที่กําหนดเอง
นักอาร์คานิสต์	85c953ebe4a6	18 พฤษภาคม 2565	แก้ไขปัญหาเครื่องหมายพื้นที่เก็บข้อมูล PHP 8.1 ใน Mercurial
โปรดทราบว่าตั้งแต่ปี 2022 Phorge ได้เปิดตัวคอมมิตมากกว่า 300 รายการ และ Phabricator มีคอมมิตน้อยกว่า 30+ รายการ โดยทั่วไปเราตระหนักถึงสถานการณ์นี้ (ดู ความแตกต่างของรหัสระหว่าง Phabricator และ Phorge) หากคุณสนใจคุณสมบัติเฉพาะที่เป็น Phabricator เท่านั้น โปรดติดต่อ Phorge เพื่อติดตามสิ่งเหล่านี้

ขึ้นอยู่กับระยะเวลาที่คุณอัปเดตครั้งล่าสุด อาจมีการเปลี่ยนแปลงบางอย่างที่เพิ่มเข้ามาใน Phorge - คุณอาจต้องการตรวจสอบ บันทึกการเปลี่ยนแปลง ตั้งแต่ปี 2022

คุณยังสามารถสร้างชุด (.ช) ไฟล์ที่มีคําสั่งทั้งหมดที่เราจะเรียกใช้ผ่านกระบวนการอัพเกรดแม้ว่าคุณควรตรวจสอบให้แน่ใจว่ามันจะใช้สาขาที่คุณยินดีใช้

กําลังอัปเดต
1 หยุดเว็บเซิร์ฟเวอร์และภูต
2 สํารองข้อมูลการติดตั้งของคุณ - การจัดเก็บฐานข้อมูลและไฟล์
3 อัปเดต แหล่งกําเนิด ที่อยู่ในแต่ละพื้นที่เก็บข้อมูลและทําการเปลี่ยนแปลง:
# ขณะนี้คุณอยู่ในไดเรกทอรี Phabricator ของคุณ เราจะมาที่อาร์คานิสต์ทีหลัง
$ ซีดี ./ช่างประกอบ/

# นี้จะพิมพ์ออก `master` or`stable - เป็นสาขาที่คุณกําลังใช้:
$ git rev-parse --คําย่อ-ref '@{u}'

$ git remote เปลี่ยนชื่อแหล่งกําเนิดเก่า
$ git remote เพิ่มต้นกําเนิด https://github.com/phorgeit/phorge.git
$ git ดึงต้นกําเนิด

# สิ่งนี้จะสร้างการสํารองข้อมูลสถานะปัจจุบันของคุณ:
$ git สาขา last_known_good HEAD

# ลบไฟล์บางไฟล์ที่เราทําลายใน Aphlict - เราจะอัปเดตในภายหลัง
$ rm -f./support/aphlict/server/package-lock.json./support/aphlict/server/package.json

# กําหนดค่ารีโมทใหม่ของคุณ: คุณสามารถใช้ `origin/stable` แทน นั่นคือสาขาที่คุณเคยใช้
$ สาขา git --set-upstream-to=แหล่งกําเนิดสินค้า/อาจารย์
# รีเซ็ตสําเนาการทํางานของ Phorge เป็นรีโมทล่าสุด: คุณสามารถใช้ `origin/stable` แทน นั่นคือสาขาที่คุณเคยใช้ ซึ่งอาจลบไฟล์ใดๆ ที่คุณเปลี่ยนแปลงในสําเนาการทํางานของคุณ
$ git รีเซ็ต --แหล่งกําเนิดยาก/ต้นแบบ

# ทําซ้ําสําหรับไดเรกทอรี Arcanist:
$ ซีดี .../นักโบราณคดี/
$ git remote เปลี่ยนชื่อแหล่งกําเนิดเก่า
$ git remote เพิ่มต้นกําเนิด https://github.com/phorgeit/arcanist.git
$ git ดึงต้นกําเนิด
$ git สาขา last_known_good HEAD

# กําหนดค่ารีโมทและฟอร์ซรีเซ็ตใหม่ของคุณ: คุณสามารถใช้ `origin/stable` แทน นั่นคือสาขาที่คุณเคยใช้
$ สาขา git --set-upstream-to=แหล่งกําเนิดสินค้า/อาจารย์
$ git รีเซ็ต --แหล่งกําเนิดยาก/ต้นแบบ
4 อัปเดตการกําหนดค่าบางอย่าง
สําคัญ: คุณอาจเปลี่ยนการกําหนดค่าเหล่านี้บางส่วนในการติดตั้งเป็นค่าที่ไม่ใช่ค่าเริ่มต้นแล้ว ตรวจสอบให้แน่ใจว่าไม่ได้เปลี่ยนเป็นค่าที่ไม่ถูกต้อง!
คุณสามารถแสดงรายการค่า Config ของคุณได้โดยการรัน ./bin/กําหนดค่ารายการ และตรวจสอบค่าเฉพาะกับ ./bin/config get <config คีย์>
ค่าเริ่มต้นของค่าการกําหนดค่าเหล่านี้อาจเปลี่ยนจาก Phabricator เป็น Phorge ดังนั้นที่นี่เราจะตั้งค่าเป็นค่าเริ่มต้นก่อนหน้าอย่างชัดเจน

อัปเดตการกําหนดค่าเหล่านี้โดยการรัน ./bin/config set <config key> <ค่า> ใน ฟาบริเคเตอร์ ไดเรกทอรี เช่น:

$./bin/config ชุด storage.default-namespace phabricator
กําหนดค่าคีย์	ค่าเริ่มต้นเก่า	ค่าเริ่มต้นใหม่	ความคิดเห็น
พื้นที่เก็บข้อมูล.default-namespace	ฟาบริเคเตอร์	ไม่เปลี่ยนแปลง	อาจจะเปลี่ยนได้ในเร็วๆนี้ ตั้งอันนี้เลย!
5 ติดตั้ง Aphlict อีกครั้ง
ความเจ็บปวดคือ เซิร์ฟเวอร์การแจ้งเตือน, อนุญาตให้มีการแจ้งเตือน "เรียลไทม์" เพื่อแสดงบนหน้า

เราได้เปลี่ยนคําแนะนําในการติดตั้งของ Aphlict ใน T15019● อย่างไรก็ตาม Aphlict ยังคงต้องการ โหนด และ เอ็นพีเอ็ม●

$ ซีดี ./phabricator/support/aphlict/เซิร์ฟเวอร์/
$ rm -rf โหนด_โมดูล/
ติดตั้ง $ npm --ไม่บันทึก
$ ซีดี ////
อัปเกรดการจัดเก็บและการรีสตาร์ท
เช่นเดียวกับการอัปเดตทั่วไป คุณควรเรียกใช้ phabricator/bin/การอัพเกรดพื้นที่เก็บข้อมูล ณ จุดนี้

สําคัญ: นี่คือจุดที่ไม่หวนกลับ เมื่อคุณเปลี่ยนสคีมาฐานข้อมูลแล้ว จะไม่มีการกลับไปใช้เวอร์ชันก่อนหน้า คุณควรทําการสํารองข้อมูลก่อนที่จะเริ่มการอัปเดต
```

# Simple XML2JSON Parser
[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/buglabs/node-xml2json?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://travis-ci.org/buglabs/node-xml2json.svg?branch=master)](https://travis-ci.org/buglabs/node-xml2json)

It does not parse the following elements:

* CDATA sections (*)
* Processing instructions
* XML declarations
* Entity declarations
* Comments

This module uses node-expat which will require extra steps if you want to get it installed on Windows. Please
refer to its [documentation](https://github.com/astro/node-expat/blob/master/README.md#windows).

## Installation
```
$ npm install xml2json
```

## Usage
```javascript
var parser = require('xml2json');

var xml = "<foo attr=\"value\">bar</foo>";
console.log("input -> %s", xml)

// xml to json
var json = parser.toJson(xml);
console.log("to json -> %s", json);

// json to xml
var xml = parser.toXml(json);
console.log("back to xml -> %s", xml)
```

## API

```javascript
parser.toJson(xml, options);
```
```javascript
parser.toXml(json);
```

### Options object for `toJson`

Default values:
```javascript
var options = {
    object: false,
    reversible: false,
    coerce: false,
    sanitize: true,
    trim: true,
    arrayNotation: false
    alternateTextNode: false
};
```

* **object:** Returns a Javascript object instead of a JSON string
* **reversible:** Makes the JSON reversible to XML (*)
* **coerce:** Makes type coercion. i.e.: numbers and booleans present in attributes and element values are converted from string to its correspondent data types. Coerce can be optionally defined as an object with specific methods of coercion based on attribute name or tag name, with fallback to default coercion.
* **trim:** Removes leading and trailing whitespaces as well as line terminators in element values.
* **arrayNotation:** XML child nodes are always treated as arrays NB: you can specify a selective array of nodes for this to apply to instead of the whole document. 
* **sanitize:** Sanitizes the following characters present in element values:

```javascript
var chars =  {
    '<': '&lt;',
    '>': '&gt;',
    '(': '&#40;',
    ')': '&#41;',
    '#': '&#35;',
    '&': '&amp;',
    '"': '&quot;',
    "'": '&apos;'
};
```
* **alternateTextNode:** Changes the default textNode property from $t to _t when option is set to true. Alternatively a string can be specified which will override $t to what ever the string is.


### Options object for `toXml`

Default values:
```javascript
var options = {
    sanitize: false,
    ignoreNull: false
};
```

* `sanitize: false` is the default option to behave like previous versions
* **ignoreNull:** Ignores all null values


(*) xml2json tranforms CDATA content to JSON, but it doesn't generate a reversible structure.

## License
(The MIT License)

Copyright (c) 2016 xml2json AUTHORS 

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to
deal in the Software without restriction, including without limitation the
rights to use, copy, modify, merge, publish, distribute, sublicense, and/or
sell copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS
IN THE SOFTWARE.
