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

2. Thực hành

   - TẠO THƯ MỤC DỰ ÁN

     <img width="567" height="203" alt="image" src="https://github.com/user-attachments/assets/4103b2a8-28d3-47f7-bb6d-1c4f387950d5" />

- TẠO docker-compose.yml

<img width="1025" height="897" alt="image" src="https://github.com/user-attachments/assets/dc3baa28-2b23-4215-9b6f-8c0dec15d8c0" />

- TẠO FLASK API và app.py 

  <img width="687" height="386" alt="image" src="https://github.com/user-attachments/assets/ad89b3ed-8dbf-4a9d-83ce-0bfc5fa3edc6" />

<img width="1005" height="902" alt="image" src="https://github.com/user-attachments/assets/6856d4c6-4e4f-45d8-8aec-7683493482c8" />

- FRONTEND

  <img width="996" height="873" alt="image" src="https://github.com/user-attachments/assets/de3a47b3-bf78-464a-8c25-d9f29e602c16" />

- NGINX

  <img width="747" height="500" alt="image" src="https://github.com/user-attachments/assets/198c46e8-3231-45ad-ab41-72f06a704ad1" />

- Chạy các container

<img width="1013" height="897" alt="image" src="https://github.com/user-attachments/assets/76e53f66-8ba8-4ca4-8ee7-b27293c45367" />

- MARIADB

  <img width="987" height="765" alt="image" src="https://github.com/user-attachments/assets/cdb02a37-d5a6-48b6-973a-5c35c19c8e62" />

<img width="955" height="546" alt="image" src="https://github.com/user-attachments/assets/8b087886-2606-49e6-94fc-bbbf7d841811" />

<img width="806" height="62" alt="image" src="https://github.com/user-attachments/assets/7f67ada6-ebaa-46fe-a24b-f88067ab2bd9" />

=> Kết quả cho thấy : 

✅MariaDB hoạt động
✅ Bảng realtime_data hoạt động
✅ Flask kết nối được MariaDB
✅ API trả dữ liệu JSON thành công
✅ Docker network hoạt động

- Node-RED lấy được dữ liệu thời tiết thật

<img width="1315" height="808" alt="image" src="https://github.com/user-attachments/assets/d85d9215-eb59-410c-920d-daca2a552432" />

- LƯU DỮ LIỆU VÀO MARIADB

  <img width="992" height="720" alt="image" src="https://github.com/user-attachments/assets/b577b710-9617-4054-8e5c-12d22475a970" />

=> kết quả 

<img width="960" height="296" alt="image" src="https://github.com/user-attachments/assets/d69e2d42-ca30-485d-8f86-23c8a526a7aa" />

<img width="1858" height="917" alt="image" src="https://github.com/user-attachments/assets/96ccd91b-ce55-46f8-9d64-6cd08070b1ac" />

=> ✅ Build Influx hoạt động
✅ Dữ liệu đã được gửi sang node InfluxDB

<img width="1101" height="905" alt="image" src="https://github.com/user-attachments/assets/0fc7cf24-fe0c-459a-b489-87d4b29c44ac" />

=>✅ Đã có dữ liệu trong InfluxDB
  ✅ Có 2 đường biểu diễn:

    . Nhiệt độ khoảng 27°C
    . Độ ẩm khoảng 75-76%

<img width="1098" height="906" alt="image" src="https://github.com/user-attachments/assets/d6bd0ecf-14e4-42a1-9c1a-1ab281be54d2" />
 =>✅ Grafana kết nối được InfluxDB

 
