---
layout: post
title:  "Arduino & 繼電器"
date:   2019-12-10 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Arduino & 繼電器

## 1. 基礎設置
一盞燈，可以拿來看繼電器有沒有發揮作用


## 2. 將 繼電器 傳輸接上Arduino

可以把繼電器想成一種開關，只是開關的控制是由後面的 Arduino 決定的

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
```cpp
int relay_pin = 7;

void setup() {
}

void loop() {
    digitalWrite(relay_pin, HIGH);
    delay(5000);
    digitalWrite(relay_pin, LOW);
    delay(5000);
}
```

## 4. 結果
如果繼電器可能有彈跳問題，就可能要拿二極體來處理了

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
