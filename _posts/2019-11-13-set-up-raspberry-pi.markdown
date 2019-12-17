---
layout: post
title:  "Raspberry 的設置及隨時連到樹莓派的方法"
date:   2019-11-13 18:15:56 +0800
categories: RaspberryPi 
---

# 樹莓派的設置

## 1. 基礎設置
首先到 [Raspberry Pi 的網站](https://www.raspberrypi.org/downloads/) 下載映像檔 Raspbian，習慣不用GUI了所以下載 Raspbian Buster Lite 的版本      
寫出這篇筆記時最新的 OS 是 `2019-09-26-raspbian-buster-lite.zip`
> 備註: 筆者在此試過下方的 ubuntu 19.10 server 版(使用樹莓派4B)
> 問題有點多
> 1. USB 抓不到滑鼠、鍵盤等輸入
> 2. 藍牙裝置也抓不到
> 3. GPIO也有點問題

下載後寫入到 micro SD 卡中
此為 linux 指令，其中讀卡機讀到的記憶卡在 mmcblk0，請自己下 lsblk 找到記憶卡在哪個位置
```bash
unzip -p 2019-09-26-raspbian-buster-lite.zip | sudo dd of=/dev/mmcblk0 bs=4M conv=fsync
```

## 2. 自動連線
現在如果手邊沒有 HDMI 線、螢幕、鍵盤等等的話，該如何設定樹莓派連線呢？   
在 SD 卡切出來的 boot 分區內建立一個 `wpa_supplicant.conf` 檔案。

寫入以下資料
```
country=TW
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
 ssid="Wifi SSID"
 scan_ssid=1
 psk="Wifi Password"
 key_mgmt=WPA-PSK
}
```
> 獲取樹莓派 IP 的話需要到，路由器後台或是使用 nmap 之類的工具查詢。

## 3. 開啟 SSH
現在如果手邊沒有 HDMI 線或是螢幕的話，該如何連上樹莓派呢？

答案是很簡單的，在 SD 卡切出來的 boot 分區內建立一個 `ssh` 檔案，樹莓派在開機時就會啟動 ssh server 的服務了。

```bash
touch ssh
```
等開機後你就可以連上樹莓派了

## 4. 隨時連到樹莓派
有時候跑來跑去，我們總不會待在同一個地點，所以可以隨時隨地連上樹莓派就是一個需求。   
藍牙再加上 PAN 是很好用的東西。   
在這邊你應該可以先通過 WiFi 或是有線網路連上樹莓派了

### 1. 先安裝相依套件，再來在樹莓派上建立藍牙 PAN 網路設定檔
```bash
apt install bluez-tools
```

```bash
#file: /etc/systemd/network/pan0.netdev
[NetDev]
Name=pan0
Kind=bridge
```

```bash
#file: /etc/systemd/network/pan0.network
[Match]
Name=pan0
 
[Network]
Address=172.20.1.1/24
DHCPServer=yes
```

### 2. 在樹莓派上面建立一個藍牙驗證服務 跟 藍牙網路服務 的 service 檔案
```bash
#file: /etc/systemd/system/bluetooth-agent.service
[Unit]
Description=Bluetooth Auth Agent
 
[Service]
ExecStart=/usr/bin/bt-agent -c NoInputNoOutput
Type=simple
 
[Install]
WantedBy=multi-user.target
```

```bash
#file: /etc/systemd/system/bluetooth-network.service
[Unit]
Description=Bluetooth NEP PAN
After=pan0.network
 
[Service]
ExecStart=/usr/bin/bt-network -s nap pan0
Type=simple
 
[Install]
WantedBy=multi-user.target
```

### 3. 重新啟動網路服務及啟動藍牙驗證服務、藍牙網路服務
並把自己建的藍牙服務改為啟用
```bash
systemctl enable bluetooth-agent
systemctl enable bluetooth-network
systemctl start systemd-networkd
systemctl start bluetooth-agent
systemctl start bluetooth-network
```

### 4. 與樹莓派的藍牙進行初次配對
進去藍牙命令工具
```bash
sudo bluetoothctl
```

```bluetoothctl
# 列出可用的藍牙控制器
[bluetooth]# list
Controller DC:A6:32:16:66:18 raspberrypi [default]

# 選擇欲使用的藍牙控制器
[bluetooth]# select DC:A6:32:16:66:18

# 啟動搜尋
[bluetooth]# scan on
Discovery started
[CHG] Controller DC:A6:32:16:66:18 Discovering: yes
[NEW] Device 49:E1:88:93:7A:30 49-E1-88-93-7A-30
[CHG] Device 00:28:F8:36:4F:29 RSSI: -51
[CHG] Device 00:28:F8:36:4F:29 TxPower: 12
[NEW] Device 00:28:F8:36:4F:29 ssdoz2skArch

# 進行配對
[bluetooth]# pair 00:28:F8:36:4F:29
Attempting to pair with 00:28:F8:36:4F:29
[CHG] Device 00:28:F8:36:4F:29 Connected: yes
Request confirmation

# 確認配對
[agent] Confirm passkey 537710 (yes/no): yes

[CHG] Device 00:28:F8:36:4F:29 UUIDs: 00001104-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 00001105-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 00001106-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 0000110c-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 0000110e-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 0000112f-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 00001132-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 00001133-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 00001200-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 00001801-0000-1000-8000-00805f9b34fb
[CHG] Device 00:28:F8:36:4F:29 UUIDs: 00005005-0000-1000-8000-0002ee000001
[CHG] Device 00:28:F8:36:4F:29 ServicesResolved: yes
[CHG] Device 00:28:F8:36:4F:29 Paired: yes

# 配對成功拉~~
Pairing successful
[CHG] Device 00:28:F8:36:4F:29 ServicesResolved: no
[CHG] Device 00:28:F8:36:4F:29 Connected: no
[CHG] Device 00:28:F8:36:4F:29 RSSI: -87
[CHG] Device 00:28:F8:36:4F:29 Connected: yes
[CHG] Device 00:28:F8:36:4F:29 RSSI: -51
```
![]({{ "images/2019-11-13/螢幕快照_2019-11-14_01-19-46.png" | relative_url }})   

![]({{ "images/2019-11-13/螢幕快照_2019-11-14_01-21-09.png" | relative_url }})   

再來連上藍牙網路，打開網路資訊，我們就可以使用藍牙連線到樹莓派
![]({{ "images/2019-11-13/螢幕快照_2019-11-14_01-21-41.png" | relative_url }})   
使用方式跟一般連線一樣喔，像這張圖為例，使用
```bash
ssh pi@172.20.1.1
```
帳號 `pi`， 密碼 `raspberry`
就可以連上樹莓派啦~

### 5. Python3 相關套件安裝安裝

因為之後的感測器讀取都使用 Python 3，所以基本的 GPIO 跟 pip 就先裝起來
```bash
sudo apt install python3-pip python3-rpi.gpio
```
