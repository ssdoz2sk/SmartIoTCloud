---
layout: post
title:  "Arduino and GPS 模組"
date:   2019-12-06 18:09:11 +0800
categories: Arduino GPS
---

# Arduino and GPS 模組

## 1. 基礎設置


## 2. 將Lora 傳輸接上插上樹莓派

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp
SoftwareSerial mySerial(10, 11); // RX, TX
 
void setup() 
{
  Serial.begin(9600);
 
  mySerial.begin(9600);
 
}
 
void loop() // run over and over
{
  if (mySerial.available())
    Serial.write(mySerial.read());
 
}
```

## 4. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
