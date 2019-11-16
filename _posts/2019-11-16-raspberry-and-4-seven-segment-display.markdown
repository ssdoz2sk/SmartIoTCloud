---
layout: post
title:  "Raspberry 與四位數七段顯示器"
date:   2019-11-16 18:09:11 +0800
categories: RaspberryPi 七段顯示器
---

# Raspberry 顯示4*七段顯示器
在之前我們拿LCD1604來當作時鐘（這時鐘好像有點貴），但是這在黑暗中看沒那麼容易看...   
再來我們拿二個四位數的七斷顯示器來當作時鐘，只顯示月，日，小時，分鐘。   
   
此範例採用共陽極四位數七段顯示器   
上下各6個腳位，其中8個的功能就是控制七段顯示器的亮暗，另4個腳位則代表想要控制哪一個七段顯示器。   
當腳位0為 Low、而腳位1、2、3為High時，代表想要控制最右邊的七段顯示器，依此類推。   

## 1. 把 線接上 樹莓派

| 接腳 | GPIO |
| --- | ---- |
| GND | GND |
| Vcc | 5V |
| 第1顆的 0~3 | GPIO 0~3  |
| 第2顆的 0~3 | GPIO 4~7  |
| a | GPIO 11 |
| b | GPIO 12 |
| c | GPIO 13 |
| d | GPIO 14 |
| e | GPIO 15 |
| f | GPIO 16 |
| g | GPIO 17 |
| h | GPIO 18 |


--- 

![]({{ "images/2019-11-16/2019-11-16.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-11-16_LCD_1604.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/LCD I2C 1602.fzpz" | relative_url }})-->

## 2. 程式碼
```python
import time
import RPi.GPIO as GPIO

GPIO.setmode(GPIO.BCM)
lcd = [3, 2, 1, 0, 7, 6, 5, 4]
lcd_pin = [11, 12, 13, 14, 15, 16, 17, 18]

digital = {
    '0': [False, False, False, False, False, False, True,  True],
    '1': [True,  False, False, True,  True,  True,  True,  True],
    '2': [False, False, True,  False, False, True,  False, True],
    '3': [False, False, False, False, True,  True,  False, True],
    '4': [True,  False, False, True,  True,  False, False, True],
    '5': [False, True,  False, False, True,  False, False, True],
    '6': [False, True,  False, False, False, False, False, True],
    '7': [False, False, False, True,  True,  True,  True,  True],
    '8': [False, False, False, False, False, False, False, True],
    '9': [False, False, False, False, True,  False, False, True],
    '.': [True,  True,  True,  True,  True,  True,  True,  False]
}
for p in lcd + lcd_pin:
    GPIO.setup(p, GPIO.OUT)

def setDigital(index, num):
    GPIO.output(lcd[index], True)
    for pin, status in zip(lcd_pin, digital[str(num)]):
        GPIO.output(pin, status)
    time.sleep(0.002)
    GPIO.output(lcd[index], False)
 
while True:
    try:
        timeString = time.strftime(f'%m%d%H%M')
        for (index, value) in enumerate(timeString):
            setDigital(index, value)

    except RuntimeError as error:
        print(error.args[0])

```

## 3. 結果

![]({{ "images/2019-11-16/IMG_0543.JPG" | relative_url }})   

