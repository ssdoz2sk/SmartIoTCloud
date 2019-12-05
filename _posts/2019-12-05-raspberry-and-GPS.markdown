---
layout: post
title:  "Raspberry 跟 GPS"
date:   2019-12-05 18:09:11 +0800
categories: RaspberryPi LoRa
---

# Raspberry 跟 GPS

## 1. 基礎設置
1. [pip 與 GPIO 函式庫安裝]({{ "2019/11/12/set-up-raspberry-pi.html#5-python3-相關套件安裝安裝" | relative_url }})
2. gpsd 
    ```bash apt install gpsd```

## 2. 將 GPS 傳輸接上插上樹莓派

![]({{ "images/2019-12-05/2019-12-05-Raspberry_GPS_bb.png" | relative_url }})   

[連接圖檔案]({{ "files/fz_sketch/2019-12-05-Raspberry_GPS.fzz" | relative_url }})   

## 2.1 修改 `/etc/default/gpsd`
設定成 DEVICES="/dev/ttyS0"   
啟動服務 `systemctl start gpsd`   
如果沒有問題，執行 cgps 應該可以看到資料(要稍等一下)   
如果一直如下圖，可以嘗試看看 RX TX 有沒有接反
![]({{ "images/2019-12-05/螢幕快照_2019-12-05_12-50-24.png" | relative_url }})   


## 3. 程式碼
```python
from gps3 import gps3
gps_socket = gps3.GPSDSocket()
data_stream = gps3.DataStream()
gps_socket.connect()
gps_socket.watch()
for new_data in gps_socket:
    if new_data:
        data_stream.unpack(new_data)
        print('Altitude = ', data_stream.TPV['alt'])
        print('Latitude = ', data_stream.TPV['lat'])

```

## 4. 結果

![]({{ "images/2019-12-05/螢幕快照_2019-12-05_12-41-17.png" | relative_url }})    
n/a 一段時間後才有資料   
![]({{ "images/2019-12-05/IMG_0665.jpg" | relative_url }})    

