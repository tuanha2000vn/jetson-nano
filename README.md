# CÀI ĐẶT CƠ BẢN JETSON NANO
![alt text](https://developer.nvidia.com/sites/default/files/akamai/embedded/images/jetsonNano/gettingStarted/jetson-nano-dev-kit-top-r6-HR-B01.png)

Đầu tiên khi mới mua Jetson Nano các bác cài Ubuntu theo hướng dẫn chính thức từ site 

https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit
<br>
<br>

# TUỲ CHỌN: DÙNG SSD THAY CHO THẺ NHỚ
![alt text](https://img.youtube.com/vi/53rRMr1IpWs/0.jpg)

https://www.jetsonhacks.com/2021/03/10/jetson-nano-boot-from-usb

https://www.youtube.com/watch?v=53rRMr1IpWs
<br>
<br>

# CÀI ĐẶT HOME ASSISTANT SUPERVISOR TRÊN JETSON NANO
![alt text](https://i.ytimg.com/vi/8sHXKs_zvo0/maxresdefault.jpg)
Mở Terminal của Jetson Nano và chạy các dòng lệnh này
```
sudo -i
apt-get update && apt-get upgrade -y && apt-get autoremove -y
apt-get install software-properties-common
add-apt-repository universe
apt-get update
apt-get install -y apparmor-utils apt-transport-https avahi-daemon ca-certificates curl dbus jq network-manager socat

curl -Lo installer.sh https://raw.githubusercontent.com/home-assistant/supervised-installer/master/installer.sh
bash installer.sh --machine qemuarm-64
```
<br>
<br>

# CÀI DEEPSTACK OBJECT DETECTION TRÊN DOCKER

Mở Terminal của Jetson Nano và chạy các dòng lệnh này
![alt text](https://i.ytimg.com/vi/8sHXKs_zvo0/maxresdefault.jpg)
```
sudo docker run -d --name deepstack --restart=unless-stopped --runtime nvidia -e VISION-DETECTION=True -v localstorage:/datastore -p 80:5000 deepquestai/deepstack:jetpack

```
Lưu ý:
* Không cần cài Docker vì bản Ubuntu chính thức của Jetson Nano đã có sẵn.

* dòng lệnh --runtime NVIDIA chữ viết HOA không chạy, phải viết thường  --runtime nvidia

* dòng lệnh docker run -d nghĩa là chạy ở chế độ nền

* dòng lệnh --restart=unless-stopped nghĩa là tự khởi động lại sau khi bật máy
<br>
<br>


# KIỂM TRA DEEPSTACK DOCKER LOG
![alt text](https://i.ytimg.com/vi/8sHXKs_zvo0/maxresdefault.jpg)
Khi chạy DEEPSTACK ở chế độ nền, nếu muốn xem log thì chạy dòng lệnh này trên terminal của Jetson Nano 
```
sudo docker container logs -f deepstack
```
<br>
<br>

# CẤU HÌNH HOME ASSISTANT ĐỂ SỬ DỤNG TÍNH NĂNG OBJECT DETECTION
![alt text](https://img.youtube.com/vi/vMdpLiAB9dI/0.jpg)

https://github.com/robmarkcole/HASS-Deepstack-object

https://www.youtube.com/watch?v=vMdpLiAB9dI

<br>
<br>

# TẠO AUTOMATION ĐỂ CẬP NHẬT CẢM BIẾN MỖI GIÂY MỘT LẦN
![alt text](https://www.home-assistant.io/images/getting-started/automation-editor.png)
```
- alias: "deepstack.time_based_trigger"
  mode: parallel
  trigger:
    - platform: time_pattern
      seconds: "/1"
  condition: []
  action:
    - condition: numeric_state
      entity_id: sensor.processor_use_percent
      below: 80
    - service: system_log.write
      data:
        level: info
        message: "{{ now().strftime('%S.%f') [:-4] }} Start Scannig on Processor Use Percent  {{ states.sensor.processor_use_percent.state }}"
    - service: image_processing.scan
      target:
        entity_id:
          - image_processing.camera_1
          - image_processing.camera_2
          - image_processing.camera_5
          - image_processing.camera_6
          - image_processing.e43460774
          - image_processing.e73655823
```
* Dòng này có nghĩa là ngừng chạy nếu CPU load đang trên 60%
```
    - condition: numeric_state
      entity_id: sensor.processor_use_percent
      below: 80
```
* Dòng này có nghĩa là in ra log của Home Assistant
```
    - service: system_log.write
      data:
        level: info
        message: "{{ now().strftime('%S.%f') [:-4] }} Start Scannig on Processor Use Percent  {{ states.sensor.processor_use_percent.state }}"
```

<br>
<br>

# RESET DOCKER

Delete Home Assistant

stop services
```
sudo systemctl stop hassio-supervisor.service
sudo systemctl stop hassio-apparmor.service
```

disable services
```
sudo systemctl disable hassio-supervisor.service
sudo systemctl disable hassio-apparmor.service
```

remove services
```
sudo rm -rf /etc/systemd/system/hassio-supervisor.service
sudo rm -rf /etc/systemd/system/hassio-apparmor.service
```

removing hassio folders (except the config folder)
```
sudo rm -rf /usr/sbin/hassio-supervisor
sudo rm -rf /usr/sbin/hassio-apparmor
```

Now delete the remaining docker images like you always do.

If you want to delete your Home Assistant config files,
Do the following, but be carefull! Your config is lost forever:

```
sudo rm -rf /usr/share/hassio/
```

To delete all containers including its volumes use,
```
docker rm -vf $(docker ps -a -q)
```

To delete all the images,
```
docker rmi -f $(docker images -a -q)
```
