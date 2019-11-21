---
layout: post
title:  "Raspberry & 雨滴感測器"
date:   2019-11-21 18:09:11 +0800
categories: RaspberryPi Sensor
---

# Raspberry & 雨滴感測器

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})
2. MCP3208
3. spidev 函式庫

## 2. 雨滴感測器接上樹莓派
![]({{ "images/2019-11-15/2019-11-18.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-21.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

## 3. 程式碼   
```python
import time
import spidev

spi = spidev.SpiDev()
spi.open(0, 0)
spi.max_speed_hz=1000000

def readChannel(channel):
    adc = spi.xfer2([1,(8+channel)<<4,0])
    data = ((adc[1]&3) << 8) + adc[2]
    return data

while True:
    readChannel(0)
    time.sleep(0)
```
## 4. 結果

![]({{ "images/2019-11-15/IMG_0529.JPG" | relative_url }})   
