---
layout: post
title:  "Raspberry & MQ2 MQ3 MQ9 氣體感測器"
date:   2019-11-17 18:09:11 +0800
categories: RaspberryPi GasSensor
---

# Raspberry 讀取氣體感測器資訊

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})

-忘記買MCP3008- （炸，教學後補

## 2. 把 氣體感測器 模組接上 樹莓派

| 接腳 | GPIO |
| --- | ---- |
| GND | GND |
| Vcc | 5V |
| MQ2 A0 | GPIO2 (pin3) |
| MQ3 A0 | GPIO3 (pin5) |
| MQ9 A0 | GPIO4 (pin16)|

--- 

![]({{ "images/2019-11-17/2019-11-17.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-17.fzz" | relative_url }})   

## 3. 程式碼
```python
import time

GPIO.setmode(GPIO.BCM)
GPIO.setup(2, GPIO.IN)
GPIO.setup(3, GPIO.IN)
GPIO.setup(4, GPIO.IN)

while True:
    try:
        MQ2 = GPIO.input(2) 
        MQ3 = GPIO.input(3) 
        MQ9 = GPIO.input(4) 
        print(MQ2, MQ3, MQ9)
    except RuntimeError as error:
        print(error.args[0])

    time.sleep(0.5)
```

## 4. 結果

![]({{ "images/2019-11-15/IMG_0524.JPG" | relative_url }})   
