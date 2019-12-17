---
layout: post
title:  "Raspberry & Arduino 使用藍牙 傳輸"
date:   2019-12-03 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Raspberry & Arduino 藍牙傳輸

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})

## 2. 將藍牙接上 Arduino 

![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-15.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼
Arduino
```cpp
#include <SoftwareSerial.h>
 
// 定義連接藍牙模組的序列埠
SoftwareSerial BT(8, 9); // 接收腳, 傳送腳
char val;  // 儲存接收資料的變數
 
void setup() {
  Serial.begin(9600);
  Serial.println("BT is ready!");
 
  BT.begin(9600);
}
 
void loop() {
  // 序列埠的資料送到藍牙模組
  if (Serial.available()) {
    val = Serial.read();
    BT.print(val);
  }
 
  // 藍牙模組的資料送到序列埠
  if (BT.available()) {
    val = BT.read();
    Serial.print(val);
  }
}
```

> Raspberry Pi
```python
#!/usr/bin/python

import serial
import time
import thread

port = serial.Serial("/dev/rfcomm0", baudrate=38400, timeout=0.1)

def receive_thread():
    while True:
        try:
            recvS = port.readlines()
            for recv in recvS:
                print("\nAndroid] " + recv.replace("\n", ""))

        except serial.SerialException:
            print "SerialException: "
            port.close()

        time.sleep(0.1)

thread.start_new_thread(receive_thread, ())

while True:
    s = raw_input("RPi] ")
    port.write(s)

```

## 4. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
