---
layout: post
title:  "Arduino & 測距感測器"
date:   2019-12-12 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & 測距感測器

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})

## 2. 將Lora 傳輸接上插上樹莓派

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp
int pirPin = 8;
int val;

void setup() {
 Serial.begin(9600);
}

void loop() {
 val = digitalRead(pirPin); //read state of the PIR
 
 if (val == LOW) {
 Serial.println("No motion"); //if the value read is low, there was no motion
 }
 else {
 Serial.println("Motion!"); //if the value read was high, there was motion
 }
 
 delay(1000);
}
```

## 4. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
