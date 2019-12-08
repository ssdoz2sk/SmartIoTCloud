---
layout: post
title:  "Arduino and PM2.5 Sensor"
date:   2019-12-08 18:09:11 +0800
categories: Arduino Sensor
---

# Arduino and PM2.5 Sensor

## 1. 將 PM2.5 Sensor 傳輸接上插上 Arduino

![]({{ "images/2019-12-08/PMS5003_and_LCD1604_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-12-08-PMS5003_and_LCD1604.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 2. 程式碼
```cpp
#include <LiquidCrystal_I2C.h>

#include "PMS.h"
#include <SoftwareSerial.h>

LiquidCrystal_I2C lcd(0x27,20,4);
SoftwareSerial PMSSerial(11, 12); 
PMS pms(PMSSerial);
PMS::DATA data;

void setup()
{
  lcd.init();
  lcd.backlight();
  
  Serial.begin(9600); 
  PMSSerial.begin(9600);
  Serial.print("begin");
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

    lcd.setCursor(0,0);
    lcd.print("PM 1.0: "+String(data.PM_AE_UG_1_0) + " ug/m3");
    lcd.setCursor(0,1);
    lcd.print("PM 2.5: "+String(data.PM_AE_UG_2_5) + " ug/m3");
  }

  // Do other stuff...
}
```

## 3. 結果

![]({{ "images/2019-12-08/vlcsnap-2019-12-19-14h45m15s125.png" | relative_url }})   
