---
layout: post
title:  "Arduino & 土壤溼度感測器"
date:   2019-12-09 18:09:11 +0800
categories: Arduino Sensor
---

# Arduino & 土壤溼度感測器

## 1. 基礎設置
## 2. 將Lora 傳輸接上插上樹莓派

![]({{ "images/2019-12-09/2019-12-09_Arduino_mosture_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-12-09_Arduino_mosture.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp
const int sensorPin=2;
void setup()
{
  pinMode(LEDRed, OUTPUT);    
  pinMode(sensorPin,INPUT);
  Serial.begin(9600);
}
 
void loop()
{
  int moist;
  moist = analogRead(sensorPin);
  Serial.println(moist);
}
```

## 4. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
