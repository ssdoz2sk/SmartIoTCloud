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

![]({{ "images/2019-12-03/2019-12-03-Arduino_Bluetooth_bb.png" | relative_url }})   

[連接圖檔案]({{ "file/2019-12-03-Arduino_Bluetooth.fzz" | relative_url }})   
<!--[元件位置]({{ "files/fz_part/.fzpz" | relative_url }})-->

### 按下 HC-05 的按鍵，並重接上 Arduino ，進入AT 模式
在 Arduino 的序列埠監控視窗內打下 AT ，如果回應 OK ，則成功進入 AT mode   
在序列埠監控視窗分別打入以下指令   

```
AT+UART=38400
AT+NAME=ArudinoBLE
```

並輸入 
```
AT+ADDR?
```
獲取此藍牙的Address

![]({{ "images/2019-12-03/螢幕快照_2019-12-03_18-03-54.png" | relative_url }})   
如果都顯示OK，即完成該設定，請把 HC-05 重插，離開 AT 模式

## 2.1 在樹莓派上的藍牙準備
```bash
sudo apt install libbluetooth-dev bluez bluez-tools
```

打 `sudo bluetoothctl` 進入藍牙 ctl 內
![]({{ "images/2019-12-03/螢幕快照_2019-12-03_18-03-48.png" | relative_url }})   

分別打入 
```bash
agent on
default-agent
scan on
```

這時會出現你藍牙當初設定的名稱，像是 `ArduinoBLE`，不過最重要的是複製其 MAC 位址   
```bash
pair <MAC Address>
```
預設密碼是 `1234`，配對之後就可以離開了

再來請到樹莓派的 shell 下
```bash
sudo rfcomm bind 1 <MAC Address>
```
之後就可以很方便的經過 `/dev/rfcomm1` 去跟 Arduino 去做藍牙通訊了

## 3. 程式碼
Arduino   
```cpp
#include <SoftwareSerial.h>
 
SoftwareSerial BT(8, 9); 
char val;  
bool flag = false;
 
void setup() {
  Serial.begin(38400); 
  Serial.println("BT is ready!?");
 
  BT.begin(38400); // 兩邊的藍牙頻率要一樣
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
    flag = true;
  } else {
    if(flag) {
      Serial.println();
    }
    flag = false;
  }
}
```

> Raspberry Pi   

```python
#!/usr/bin/python
import serial
import threading
import time
port="/dev/rfcomm1"
serial=serial.Serial(port, 38400) # 兩邊的藍牙頻率要一樣

def receive_thread():
    while True:
        try:
            data = serial.readline().decode()
            print("\nArduino] " + data)

        except IOError:
            print("SerialException: ")
        time.sleep(0.1)

t1 = threading.Thread(target=receive_thread, args=())
t1.daemon = True
t1.start()

while True:
    s = input("RPi] ")
    serial.write(str.encode(s))
    serial.flushInput()
```

## 4. 結果

![]({{ "images/2019-12-03/螢幕快照_2019-12-03_19-21-06.png" | relative_url }})   
   
   
如果要除錯的話可以在樹莓派下 `sudo btmon`，不過最好把其他跟樹莓派的藍牙連線斷開，留下要測試的   
   

原以為藍牙模組預設的鮑率是 38400 就沒有調整了，結果被陰了3個小時
