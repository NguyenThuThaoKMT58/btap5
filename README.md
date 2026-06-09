# btap5

bt5:
  - lý thuyết: 
    + docker là gì? 
    + các keyword được sử dụng trong docker-compose.yml
      để mô tả 1 service, network, volume,...
      liệt kê + ý nghĩa của từ khoá đó + ví dụ minh hoạ
    + ưu điểm khi triển app sử dụng docker là gì?
    + dùng docker: tạo app, test app OK trên laptop cá nhân
      giờ muốn triển khai app này trên máy chủ thật ko có internet
      thì các bước cần làm là?
  - thực hành áp dụng: APP MONITOR + ALERT DATA REALTIME
    sử dụng docker compose có nhiều serivce 
    và các thành phần cần thiết để tạo thành ứng dụng:
     + nodered liên tục lấy dữ liệu từ nguồn nào đó (chứng khoán, thời tiết, giá vàng,...)
       nguồn thực tế, số liệu luôn động sau thời gian ngắn
     + nodered lưu trữ dữ liệu vào 2 database: mariadb để lưu giá trị tức thời
       lưu lịch sử vào influxdb
     + sử dụng grafana để trực quan hoá dữ liệu: vẽ biểu đồ
     + sử dụng nginx để làm webserver
       chạy 1 trang web html+js+css làm front-end
       js: lấy dữ liệu tức thời trong mariadb qua (ajax | socket) 
           gọi api (api tự build bằng Flask giống bt1)
           api trả về giá trị tức thời trong mariadb
           hiển thị lên web, auto hiển thị số mới khi thay đổi
       sử dụng iframe để gọi grafana
       hiển thị biểu đồ dữ liệu lịch sử của thông số đã lưu
     + QUAN SÁT DỮ LIỆU LỊCH SỬ => GIÁ TRỊ BẤT THƯỜNG
       (VD MIỀN A..B: OK, DƯỚI A: ALERT LOW, TRÊN B: ALERT HIGH)
     + nodered: kết hợp bot Telegram
       khi dữ liệu not OK, thì gửi tin nhắn từ bot => group trên telegram
       group đã add bot vào: (nhóm đã có 2 người), add thêm 1875746636 thành 3 người
       mỗi khi bot gửi dữ liệu vào nhóm: mọi member of group đều nhận đc
       nội dung alert: tường minh, có value gây alert

     xuất tất cả các container ra file nén.
     xoá mọi container đang chạy
     load lại các container  từ file nén để khôi phục các container đã xoá
=========
quá trình làm: chụp ảnh lại, mô tả cho ảnh
  lưu vào trong github => paste link access public của repo: vào file excel online

-------------------------------------------------------------------------------
- lý thuyết

  1. Docker là gì?

Docker là nền tảng giúp đóng gói ứng dụng cùng toàn bộ môi trường chạy vào một Container.

2. Docker Compose là gì?

Docker Compose giúp chạy nhiều container cùng lúc.

Ví dụ:

Flask
MariaDB
Grafana
InfluxDB
Nginx
Node-RED

thay vì chạy:

docker run ...
docker run ...
docker run ...
docker run ...

chỉ cần:

docker compose up -d

3. Các keyword trong docker-compose.yml
   
- version

Xác định phiên bản compose

Ví dụ

  version: '3.9'
  
- services

Khai báo container

services:

Ví dụ

  services:
    flask:
    
- image

Image sử dụng

  image: nginx
  
- build

Build từ Dockerfile

  build: .
  
- container_name

Đặt tên container

  container_name: myflask
  
- ports

Map cổng

  ports:
  
   - "5000:5000"

  Ý nghĩa
  
  Máy thật : Container

- volumes

Mount dữ liệu

  volumes:
  
   - ./data:/data
     
- environment

Biến môi trường

  environment:
  
   MYSQL_ROOT_PASSWORD: 123456
   
- restart

Tự khởi động lại

  restart: always
  
- depends_on

Container phụ thuộc

  depends_on:
  
   - mariadb
     
- networks

Kết nối mạng

  networks:
  
   - monitor_net
     
- volumes (khai báo global)
  
  volumes:
  
   mariadb_data:
  
- networks (khai báo global)
  
  networks:
  
   monitor_net:

4. Ưu điểm khi triển khai bằng Docker
   
- Dễ triển khai
Laptop -> Server

giống hệt nhau.

- Không xung đột thư viện

App A

    Python 3.8

App B

    Python 3.12

vẫn chạy chung.

- Backup dễ
  
  docker save
  
- Scale dễ
  
  docker compose up --scale
  
- Di chuyển dễ

Copy image sang máy khác.

5. Server không có internet thì triển khai thế nào?

Ví dụ:

Laptop có internet

Server không có internet.

- Bước 1

Build image

docker compose build

- Bước 2

Xuất image

docker save nginx > nginx.tar

docker save grafana/grafana > grafana.tar

docker save nodered/node-red > nodered.tar

- Bước 3

Copy

USB

sang server.

- Bước 4

Import

docker load < nginx.tar

docker load < grafana.tar

docker load < nodered.tar

- Bước 5

Chạy

docker compose up -d
