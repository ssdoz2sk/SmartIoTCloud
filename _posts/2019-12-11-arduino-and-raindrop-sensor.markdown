---
layout: post
title:  "Arduino & 雨滴感測器 "
date:   2019-12-11 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & 雨滴感測器

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})

## 2. 將Lora 傳輸接上插上樹莓派

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp
void setup() {
 Serial.begin(9600); 
}
void loop() {
 int value = analogRead(A0);
 
 Serial.println(value);
}
```

## 4. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
