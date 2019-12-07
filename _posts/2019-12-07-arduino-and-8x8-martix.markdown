---
layout: post
title:  "Arduino & 8x8 矩陣"
date:   2019-12-07 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & 8x8 矩陣

## 1. 基礎設置
* 安裝 library `LedControl`

## 2. 將 8x8矩陣 插上 Arduino

![]({{ "images/2019-12-07/2019-12-07_Matrix_Led_Modul_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-12-07_Matrix_Led_Modul.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp
#include "LedControl.h"

// Pin 12:Data in, Pin 11: Clock,  Pin 10: CS(Load)
LedControl lc=LedControl(12,11,10,1); 

void setup()
{
    lc.shutdown(0,false);
    lc.setIntensity(0,10);  // 設定亮度為 10 
    lc.clearDisplay(0);
}
void loop()
{
    // 製造隨機亂亮的效果
    int randNumber_col = random(8);
    int randNumber_row = random(8);
    
    // hint: void setLed(int addr, int row, int col, boolean state);
    lc.setLed(0,randNumber_col,randNumber_row,1);
    delay(500);
    lc.setLed(0,randNumber_col,randNumber_row,0);
    delay(500);
}
```

## 4. 結果

剛好沒拍到發亮（炸   
![]({{ "images/2019-12-07/IMG_0667.JPG" | relative_url }})   
