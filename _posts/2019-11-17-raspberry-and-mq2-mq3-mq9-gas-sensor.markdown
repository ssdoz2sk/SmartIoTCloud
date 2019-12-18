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

![]({{ "images/2019-11-17/2019-11-17_rasp_MCP3_base_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-17_rasp_MCP3_MQ2_MQ3_MQ9.fzz" | relative_url }})   

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
    value = readChannel(0)
    value2 = readChannel(1)
    value3 = readChannel(2)
    print(f'MQ2: {value}  MQ3: {value2}  MQ9: {value3}')
    time.sleep(1)
```

## 4. 結果

![]({{ "images/2019-11-17/螢幕快照_2019-11-17_12-03-44.png" | relative_url }})   
