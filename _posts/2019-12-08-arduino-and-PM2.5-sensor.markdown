---
layout: post
title:  "Arduino and PM2.5 Sensor"
date:   2019-12-08 18:09:11 +0800
categories: Arduino Sensor
---

# Arduino and PM2.5 Sensor

## 1. 將 PM2.5 Sensor 傳輸接上插上 Arduino

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 2. 程式碼
```cpp
SoftwareSerial mySerial(10, 11); // RX, TX
#include "PMS.h"

PMS pms(mySerial);
PMS::DATA data;

void setup()
{
  Serial.begin(9600);
  mySerial.begin(9600);
}

void loop()
{
  if (pms.read(data))
  {
    Serial.print("PM 1.0 (ug/m3): ");
    Serial.println(data.PM_AE_UG_1_0);

    Serial.print("PM 2.5 (ug/m3): ");
    Serial.println(data.PM_AE_UG_2_5);

    Serial.print("PM 10.0 (ug/m3): ");
    Serial.println(data.PM_AE_UG_10_0);

    Serial.println();
  }
}
```

## 3. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
