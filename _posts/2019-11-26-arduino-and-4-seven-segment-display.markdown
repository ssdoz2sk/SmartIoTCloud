---
layout: post
title:  "Arduino & 四位數七段顯示器"
date:   2019-11-26 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & 四位數七段顯示器

## 1. 將四位數七段顯示器接上插上Arduino

| 接腳 | GPIO |
| --- | ---- |
| 第1顆的 0~3 | D0~3  |
| a | GPIO D4 |
| b | GPIO D5 |
| c | GPIO D6 |
| d | GPIO D7 |
| e | GPIO D8 |
| f | GPIO D9 |
| g | GPIO D10 |

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp
const int pinState[10][7] = {
    {1, 1, 1, 1, 1, 1, 0}, // 1
    {0, 1, 1, 0, 0, 0, 0}, // 1
    {1, 1, 0, 1, 1, 0, 1}, // 2
    {1, 1, 1, 1, 1, 0, 1}, // 3
    {0, 1, 1, 0, 0, 1, 1}, // 4
    {1, 0, 1, 1, 0, 1, 1}, // 5
    {1, 0, 1, 1, 1, 1, 1}, // 6
    {1, 1, 1, 0, 0, 0, 0}, // 7
    {1, 1, 1, 1, 1, 1, 1}, // 8
    {1, 1, 1, 1, 0, 1, 1}  // 9
};

void setup() {
    for(int i = 0; i < 11; i++) {
        pinMode(i, OUTPUT);
    } 
}

void loop() {
    set7Seg(8, 4); 
    set7Seg(7, 3); 
    set7Seg(6, 2);
    set7Seg(5, 1);
}

void set7Seg(int number, int pos) {
    for(int i = 0; i < 4; i++) {
        digitalWrite(i, 1);
    }

    for(int i = 4;i < 11;i++) {
        digitalWrite(i, pinState[number - 1][i]);
    }

    digitalWrite(pos + 9, 0);
    delay(10);
}
```

## 4. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
