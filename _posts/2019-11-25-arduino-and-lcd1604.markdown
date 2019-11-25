---
layout: post
title:  "Arduino 寫入 LCD1604 i2c 顯示器"
date:   2019-11-25 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino &  寫入 LCD1604 顯示器

## 1. 將LCD1604 i2c 顯示器接上插上Arduino

![]({{ "images/2019-11-25/2019-11-25_LCD_1604_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-25_LCD_1604.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 2. 程式碼
```cpp
#include <Wire.h> 
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27,20,4);  

void setup() {
  Serial.begin(9600);  
  lcd.init(); 
  lcd.backlight(); // 開啟背光
  lcd.clear();
}

void loop() {
  // 輸入文字
  if (Serial.available()) {
    delay(100);
    lcd.clear();
    while (Serial.available() > 0) {
      lcd.print(Serial.read());
    }
  }
}
```

## 4. 結果

![]({{ "images/2019-11-25/SNk2GZ.jpeg" | relative_url }})   
