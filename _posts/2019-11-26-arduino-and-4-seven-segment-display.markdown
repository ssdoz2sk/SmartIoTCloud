---
layout: post
title:  "Arduino & 四位數七段顯示器"
date:   2019-11-26 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & 四位數七段顯示器

## 1. 將四位數七段顯示器接上插上Arduino
![]({{ "images/2019-11-26/Arduino_7dig_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-26-Arduino_7dig.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp
// Lab13 使用 74HC595 與七段顯示器製作倒數功能

byte seven_seg_digits[10] = { B00000011,  // = 0
                              B10011111,  // = 1
                              B00100101,  // = 2
                              B00001101,  // = 3
                              B10011001,  // = 4
                              B01001001,  // = 5
                              B01000001,  // = 6
                              B00011111,  // = 7
                              B00000001,  // = 8
                              B00001001   // = 9
                             };

// 接 74HC595 的 ST_CP (pin 12,latch pin)
int latchPin = 5;
// 接 74HC595 的 SH_CP (pin 11, clock pin)
int clockPin = 4;
// 接 74HC595 的 DS (pin 14)
int dataPin = 6;
int dig1Pin = 8;
int dig2Pin = 9;
int dig3Pin = 10;
int dig4Pin = 11;
int value = 9999;

void setup() {
  // 將 latchPin, clockPin, dataPin 設置為輸出
  pinMode(latchPin, OUTPUT);
  pinMode(clockPin, OUTPUT);
  pinMode(dataPin, OUTPUT);
  pinMode(dig1Pin, OUTPUT);
  pinMode(dig2Pin, OUTPUT);
  pinMode(dig3Pin, OUTPUT);
  pinMode(dig4Pin, OUTPUT);
}

// 在七段顯示器上顯示指定的一個數字 
void sevenSegWrite(byte digit) {
  // 送資料前要先把 latchPin 拉成低電位
  digitalWrite(latchPin, LOW);
    
  // 送出數字的位元資料 (bit pattern)
  shiftOut(dataPin, clockPin, LSBFIRST, seven_seg_digits[digit]);  

  // 送完資料後要把 latchPin 拉回成高電位
  digitalWrite(latchPin, HIGH);
}

void loop() {       
  // 從 9999 倒數到 0 
  int dig1 = value % 10;
  int dig2 = value / 10 % 10;
  int dig3 = value / 100 % 10;
  int dig4 = value / 1000;
  for(int i = 0; i < 50; i++) {
    digitalWrite(dig1Pin, HIGH);
    sevenSegWrite(dig1); 
    delay(4);
    digitalWrite(dig1Pin, LOW);
    
    digitalWrite(dig2Pin, HIGH);
    sevenSegWrite(dig2); 
    delay(4);
    digitalWrite(dig2Pin, LOW);
    
    digitalWrite(dig3Pin, HIGH);
    sevenSegWrite(dig3); 
    delay(4);
    digitalWrite(dig3Pin, LOW);
  
    digitalWrite(dig4Pin, HIGH);
    sevenSegWrite(dig4); 
    delay(4);
    digitalWrite(dig4Pin, LOW);
    delay(4);
  }
  
  value -= 1;
  if (value < 0) {
    value = 9999;
  }
}


```

## 4. 結果

![]({{ "images/2019-11-26/IMG_0653.JPG" | relative_url }})   
