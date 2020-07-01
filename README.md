# การทำระบบ Monitor Server & MySQL ง่าย ๆ ด้วย Docker (grafana + prometheus + node exportor + mysql exportor)
## คู่มือนี้ มี 2 แบบ 
1. ใช้ไปเลย บ่อยากเข้าใจหยัง
2. มันมาจั่งใด๋วะ

### ความต้องการเบื้องต้นของระบบ
    docker
    docker-compose
    git

### คู่มือแบบที่ 1

    git clone https://github.com/cybernude/server-monitor.git
    cd server-monitor
    chmod -R 777 grafana
    
#### จากนั้น ทำการแก้ไขทำการแก้ไขไฟล์  docker-compose.yml ด้วย editor ที่ถนัด
แก้ไขค่าการเชื่อมต่อ database ของเรา จะอยู่ประมาณบรรทัดที่ 12

    - "DATA_SOURCE_NAME=root:123456@(10.0.0.181:3333)/"


    root = user ของฐานข้อมูล
    123456 = password ของฐานข้อมูล
    10.0.0.181 = ip ของฐานข้อมูล
    3333 = port ที่เชื่อมต่อ database ของเรา

แก้แค่นี้แหละครับ จากนั้น save ครับ

หมายเหตุ : ถ้าไม่อยากใช้ User root หรือ user เดิมในระบบ ให้สร้าง user ขึ้นมาใหม่ครับ

    CREATE USER 'exporter'@'%' IDENTIFIED BY 'XXXXXXXX' WITH MAX_USER_CONNECTIONS 3;
    GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'%';
    
   

[![2020-06-25-7-18-52.png](https://i.postimg.cc/s2xQY8s7/2020-06-25-7-18-52.png)](https://postimg.cc/hJFtg2Jt)

#### จากนั้นทดสอบ ว่ามันทำงานได้ไหม

    docker-compose up

จะเจอหน้าจอดำ ๆ กะข้อความอิหยังบุ๊ แหล่นล้าย ๆ ประมาณนี้

[![2020-06-25-7-25-00.png](https://i.postimg.cc/cLH42n2N/2020-06-25-7-25-00.png)](https://postimg.cc/crpS8rdD)


จากนั้นนั้น เปิด browser ที่เราถนัด ใส่ url = http://ip_server:3000

    ip_server = ip ของ server ที่เราติดตั้งตัว server monitor นี้

จะเจอหน้าจอ login
    
    user = admin
    password = admin
    
จากนั้น เลือกกราฟ ที่จะเราจะ monitor ครับ ผมใส่มาให้แล้ว 2 กราฟคือ
1. MySQL Overview สำหรับการ monitor mysql server ของเราครับ
2. Node Exporter Full ไว้สำหรับ monitor ตัวเครื่อง server ของเราครับ

การเลือกแสดงกราฟ ครั้งแรก ไปที่ 

    เครื่องหมายแว่นขยาย ---> Search

[![2020-06-25-7-47-10.png](https://i.postimg.cc/66b9R3Tj/2020-06-25-7-47-10.png)](https://postimg.cc/9zT5HcdZ)


จะเจอกราฟสองกราฟ ที่ว่าด้านบนครับ เลือกกดดูได้เลย (ครั้งต่อ ๆ ไป เรา Login เข้ามา มันจะอยู่ที่หน้าแรกเลย)

[![2020-06-25-7-32-33.png](https://i.postimg.cc/MZdC4FSv/2020-06-25-7-32-33.png)](https://postimg.cc/0rKXKZrs)


เข้าไปดูแรก ๆ อาจจะ ไม่เห็นอะไรขยับ ไม่ต้องตกใจ ให้ไปเปลี่ยน Time Rang ของมันก่อน อยู่ที มุมขวาบน ตรงคำว่า

    ถ้าเป็น Mysql Overview = Last 12 Hours
    ถ้าเป็น Node Exportor Full = Last 24 House

เลือกเป็น Last 5 Minutes ก็ได้ครับ จะได้มองเห็นว่ามันขยับ

[![2020-06-25-7-49-42.png](https://i.postimg.cc/TYWXDJz1/2020-06-25-7-49-42.png)](https://postimg.cc/N5YVhmfv)

[![2020-06-25-7-49-12.png](https://i.postimg.cc/3RwsJXjK/2020-06-25-7-49-12.png)](https://postimg.cc/PLB3S8Fc)

ถ้ากราฟ ทั้งสองตัว ทำงานได้ ถูกต้อง ก็เรียบร้อย ยินดีด้วยครับ คุณได้ ระบบ Monitor Server แบบชิว ๆ แล้ว ให้เรากลับมาที่หน้าจอ Console ที่เราเปิดค้างไว้ตะกี๊ (ที่มีข้อความแหล่น ล้าย ๆ นั่นหล่ะ) จากนั้นกด 

    Ctrl + c

เพื่อออกจากการรัน docker-compose up ครับ เพราะถ้ารันแบบนี้ มันจะค้างหน้าจอแบบนี้อยู่ อันนี้เราทดสอบรันเพื่อเช็คว่ามันทำงานได้ถูกต้องไหมครับ จากนั้นสั่ง ให้ระบบมันรันอีกรอบ แต่ให้มันรันเป็น daemon อยู่เบื้องหลังด้วยคำสั่ง

    docker-compose up -d

จากนั้น ลองเรียกใช้งานอีกทีครับผม

##### เสร็จเรียบร้อย แล้ว เล่าให้ยืดยาวเฉย ๆ หลัก ๆ มีแค่แก้ config การเชื่อมต่อ database ของเราเฉย ๆ กระบวนการทั้งหมดนี้ ถ้าอ่าน ทำความเข้าใจ ก่อนเริ่มลงมือทำ ใช้เวลา ไม่ถึง 5 นาที เราก็มีระบบ Monitor Server ดี ๆ ไว้ใช้แล้วครับ

##### จบ คู่มือ แบบ ที่ 1

## หากท่านไม่ต้องการอะไรเพิ่ม ไม่ต้องการเข้าใจอะไร ก็เสร็จเรียบร้อยแล้วครับ ไม่ต้องเลื่อนลงไปอ่าน คู่มือแบบที่ 2 

## หากท่านต้องการเข้าใจการทำงาน ของ grafana , prothemeus เป็นยังไง เพิ่มกราฟ มายังไง จะ monitor mysql มากกว่า 1 server ทำแบบไหน จะอยู่ในคู่มือ แบบที่ 2 นะครับ



### คู่มือแบบที่ 2

    git clone https://github.com/cybernude/server-monitor.git
    cd server-monitor
    chmod -R 777 grafana
    
#### จากนั้น ทำการแก้ไขทำการแก้ไขไฟล์  docker-compose.yml ด้วย editor ที่ถนัด
แก้ไขค่าการเชื่อมต่อ database ของเรา จะอยู่ประมาณบรรทัดที่ 12

    - "DATA_SOURCE_NAME=root:123456@(10.0.0.181:3333)/"


    root = user ของฐานข้อมูล
    123456 = password ของฐานข้อมูล
    10.0.0.181 = ip ของฐานข้อมูล
    3333 = port ที่เชื่อมต่อ database ของเรา
    
หมายเหตุ : ถ้าไม่อยากใช้ User root หรือ user เดิมในระบบ ให้สร้าง user ขึ้นมาใหม่ครับ

    CREATE USER 'exporter'@'%' IDENTIFIED BY 'XXXXXXXX' WITH MAX_USER_CONNECTIONS 3;
    GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'%';
    
    

## ส่วนนี้ เป็นการ config เพื่อให้สามารถ monitor mysql server ได้มากกว่า 1 ตัว ถ้าเรามีแค่ตัวเดียว ข้ามไปได้เลย
และหากเราต้องการจะ monitor mysql มากกว่า 1 server ให้เอาเครื่องหมาย # หน้าบรรทัดที่ 13 - 20 ออก (ดูการจัดตำแหน่ง ให้ตรงกันด้วย มีผล) และแก้ค่าการเชื่อมต่อ database ครับ

      # mysqlexporter2:
      #   image: prom/mysqld-exporter
      #   networks:
      #     - grafana-network
      #   ports: 
      #   - "9104:9104"
      #   environment: 
      #   - "DATA_SOURCE_NAME=root:212224@(mariadb-slave:3306)/"

มีกี่ server ก็ Copy ข้อความด้านบน วางลงต่อกันลงมาเรื่อย ๆ เลยครับ สิ่งสำคัญที่ต้องแก้คือ ตรง mysqlexporter2 เปลี่ยนเป็น  mysqlexporter3 mysqlexporter4 อะไรก็ว่าไปครับ 
แต่ต้องเปลี่ยน port ที่ map ออกมาด้านนอกด้วยนะครับ ห้ามไม่ให้ชนกัน ตรงนี้ครับ

      #   ports: 
      #   - "9105:9104"
เป็นประมาณนี้ ไม่ให้มันชนกัน เครื่องที่ 3 ก็

      #   ports: 
      #   - "9106:9104"

จริง ๆ มันเอาไว้ เทสเฉย ๆ เรา comment ไว้ก็ได้ครับ เปิดแต่ส่วนอื่น

จากนั้น ทำการแก้ไขไฟล์ prothemeus.yml ด้วย editor ที่เราถนัด

      # - job_name: mysql_server2
      #   metrics_path: /metrics
      #   scrape_interval: 10s
      #   scrape_timeout: 10s
      #   static_configs:
      #     - targets: ["mysqlexporter2:9104"]
  
  
  [![2020-06-25-9-52-03.png](https://i.postimg.cc/90FKfxX4/2020-06-25-9-52-03.png)](https://postimg.cc/Q9w04JTs)
  
  
  เอาเครื่องหมาย # หน้า บรรทัดที่ 12 - 17 ออก
  เราจะ monitor กี่เครื่อง ตัว mysql exportor มีกี่เครื่อง ก็ copy ตามด้านบน มาวาง ตามจำนวนเครื่องที่จะ monitor ครับ 
  โดยส่วนที่เราจะต้องเปลี่ยนคือ
  
  1. job_name ตั้งเป็นอะไรก็ได้ ให้เราเข้าใจครัรบ
  
    - job_name: mysql_server2
  
  2. targets
  
    - targets: ["mysqlexporter2:9104"]
  
  จะต้องตรงกันกับชื่อ Container ของ mysql_exportor ที่เรากำหนดไว้ในไฟล์ docker-compose.yml ครับผม
  
  จากนั้น save ครับ

## จบส่วนของการ config เพื่อให้สามารถ monitor mysql server ได้มากกว่า 1 ตัว


แก้แค่นี้แหละครับ จากนั้น save ครับ

[![2020-06-25-7-18-52.png](https://i.postimg.cc/s2xQY8s7/2020-06-25-7-18-52.png)](https://postimg.cc/hJFtg2Jt)

#### จากนั้นทดสอบ ว่ามันทำงานได้ไหม

    docker-compose up

จะเจอหน้าจอดำ ๆ กะข้อความอิหยังบุ๊ แหล่นล้าย ๆ ประมาณนี้

[![2020-06-25-7-25-00.png](https://i.postimg.cc/cLH42n2N/2020-06-25-7-25-00.png)](https://postimg.cc/crpS8rdD)


จากนั้นนั้น เปิด browser ที่เราถนัด ใส่ url = http://ip_server:3000

    ip_server = ip ของ server ที่เราติดตั้งตัว server monitor นี้

จะเจอหน้าจอ login
    
    user = admin
    password = admin
    
จากนั้น เลือกกราฟ ที่จะเราจะ monitor ครับ ผมใส่มาให้แล้ว 2 กราฟคือ
1. MySQL Overview สำหรับการ monitor mysql server ของเราครับ
2. Node Exporter Full ไว้สำหรับ monitor ตัวเครื่อง server ของเราครับ

แต่เดี๋ยวเรามาดูกันว่า ไอ้สองกราฟนี้ มันมายังไงครับ หลังจากเรา Login เข้ามา ที่หน้าแรก
1. เริ่มแรก ผม add Data Source เข้ามาก่อน ตามภาพ

[![2020-06-25-8-50-35.png](https://i.postimg.cc/ryW4THWR/2020-06-25-8-50-35.png)](https://postimg.cc/qgJqXj1p)

หรือไปที่ รูปเครื่องหมายฟันเฟือง ตั้งค่า Setting -> Data Source

[![2020-06-25-8-54-09.png](https://i.postimg.cc/cLDgTGq2/2020-06-25-8-54-09.png)](https://postimg.cc/VJCkLTkW)

ที่หน้า Add Data Source เลือก Prothemeus 

[![2020-06-25-8-55-37.png](https://i.postimg.cc/prd3fjKg/2020-06-25-8-55-37.png)](https://postimg.cc/ygwfBkQP)

จากนั้นในหน้า 

    Data Sources / Prometheus



ในส่วนของ HTTP ตรงช่อง url ใส่ 
    http://prometheus:9090


[![2020-06-25-8-59-38.png](https://i.postimg.cc/3JNdRb0Z/2020-06-25-8-59-38.png)](https://postimg.cc/hfFSZsrJ)

โดยที่ prometheus เป็นชื่อของ container (เปรียบเสมือนชื่อเครื่อง ในวงของ docker)  ผมกำหนดไว้ใน docker-compose.yml บรรทัดที่ 29

จากนั้นเลื่อนลงมา คลิ๊กที่  Save & Test ครับ ถ้าเราตั้งค่าถูก มันจะขึ้น

    Data source is working

เป็นสีเขียว ๆ ครับ

[![2020-06-25-9-05-00.png](https://i.postimg.cc/Gpw2XL7M/2020-06-25-9-05-00.png)](https://postimg.cc/nX2pzJt7)

เสร็จแล้วครับ การ Set Datasource ของ grafana เพื่อเรียกใช้งาน prothemeus

ต่อไป เราจะทำการ Import Graph Dashboard ที่เราต้องการจะ Monitor เข้ามาครับ ในที่นี้ จะประกอบไปด้วยกราฟ 2 แบบ คือ Node Exportor สำหรับ Monitor ตัว Server ของเรา และ MySQL ครับ
1. ไปที่ เครื่องหมาย + เลือก Import

[![2020-06-25-9-08-14.png](https://i.postimg.cc/3RNPppXz/2020-06-25-9-08-14.png)](https://postimg.cc/MMkPJnrY)

ในหน้า Import ตรงส่วนของ Import via grafana.com ให้ใส่เลข 1860 เพื่อ โหลด กราฟของ Node Exporter Full เข้ามาครับ

[![2020-06-25-9-15-00.png](https://i.postimg.cc/hjmNpnY0/2020-06-25-9-15-00.png)](https://postimg.cc/R6MPZk43)

จากนั้น คลิ๊กที่ load ครับ ระบบจะพาเรามาที่หน้า การตั้งค่าการ Import อีกหน้า ในส่วนของ Prometheus ให้เราเลือก Datasource ของ Prothemeus ที่เรากำหนดไว้ตะกี๊ครับ จากนั้น คลิ๊กที่ Import

[![2020-06-25-9-19-52.png](https://i.postimg.cc/JzB3yhHK/2020-06-25-9-19-52.png)](https://postimg.cc/v4bxkbz6)


เสร็จแล้วครับ ระบบพาเรามาที่หน้า Graph Daseboard Monitor ตัว Server  ของเราด้วย Node Exportor

ต่อไป เราจะ Import Graph Dashboard ของตัว MySQL เข้ามาครับ

2. ไปที่ เครื่องหมาย + เลือก Import

[![2020-06-25-9-08-14.png](https://i.postimg.cc/3RNPppXz/2020-06-25-9-08-14.png)](https://postimg.cc/MMkPJnrY)

ในหน้า Import ตรงส่วนของ Import via grafana.com ให้ใส่เลข 7362 เพื่อ โหลด กราฟของ Node Exporter Full เข้ามาครับ

[![2020-06-25-9-26-00.png](https://i.postimg.cc/xd6dnvjZ/2020-06-25-9-26-00.png)](https://postimg.cc/JyB8QBk3)

จากนั้น คลิ๊กที่ load ครับ ระบบจะพาเรามาที่หน้า การตั้งค่าการ Import อีกหน้า ในส่วนของ Prometheus ให้เราเลือก Datasource ของ Prothemeus ที่เรากำหนดไว้ตะกี๊ครับ จากนั้น คลิ๊กที่ Import

[![2020-06-25-9-26-35.png](https://i.postimg.cc/25SZQ8VP/2020-06-25-9-26-35.png)](https://postimg.cc/Xrtq3WK8)

เสร็จแล้วครับ ระบบพาเรามาที่หน้า Graph Daseboard Monitor ตัว Server MySQL  ของเราด้วย MySQL Overview

### แต่เด๋วก่อนนนนนนนนนนน บัก 1860 กับ 7362 มันคืออิหยัง มาแต่ไสวะ 

1860 กับ เลข 7362 dashboard ที่บรรดาเหล่านักพัฒนา dashboard grafana เอาขึ้นมาแจก เพื่อให้เรา สามารถ download มาใช้ได้ ฟรี ๆ ครับ เราสามารถ ไป search เพื่อเอากราฟ รูปแบบอื่น ๆ มาใช้ได้เลย หรือ เอามาตัดแปลงเองก้ได้ โดยสามารถเข้าไปเลือกดูได้ที่

    https://grafana.com/grafana/dashboards
 
แต่เราต้องดูด้วยว่า dashboard นั้น supports datasource ที่เป็น prothemeus ไหม

[![2020-06-25-9-42-33.png](https://i.postimg.cc/KjMLtb8C/2020-06-25-9-42-33.png)](https://postimg.cc/CnwzVWD4)


เข้าไปดูแรก ๆ กราฟอาจจะ ไม่เห็นอะไรขยับ ไม่ต้องตกใจ ให้ไปเปลี่ยน Time Rang ของมันก่อน อยู่ที มุมขวาบน ตรงคำว่า

    ถ้าเป็น Mysql Overview = Last 12 Hours
    ถ้าเป็น Node Exportor Full = Last 24 House

เลือกเป็น Last 5 Minutes ก็ได้ครับ จะได้มองเห็นว่ามันขยับ

[![2020-06-25-7-49-42.png](https://i.postimg.cc/TYWXDJz1/2020-06-25-7-49-42.png)](https://postimg.cc/N5YVhmfv)

[![2020-06-25-7-49-12.png](https://i.postimg.cc/3RwsJXjK/2020-06-25-7-49-12.png)](https://postimg.cc/PLB3S8Fc)

ถ้ากราฟ ทั้งสองตัว ทำงานได้ ถูกต้อง ก็เรียบร้อย ยินดีด้วยครับ คุณได้ ระบบ Monitor Server แบบชิว ๆ แล้ว ให้เรากลับมาที่หน้าจอ Console ที่เราเปิดค้างไว้ตะกี๊ (ที่มีข้อความแหล่น ล้าย ๆ นั่นหล่ะ) จากนั้นกด 

    Ctrl + c

เพื่อออกจากการรัน docker-compose up ครับ เพราะถ้ารันแบบนี้ มันจะค้างหน้าจอแบบนี้อยู่ อันนี้เราทดสอบรันเพื่อเช็คว่ามันทำงานได้ถูกต้องไหมครับ จากนั้นสั่ง ให้ระบบมันรันอีกรอบ แต่ให้มันรันเป็น daemon อยู่เบื้องหลังด้วยคำสั่ง

    docker-compose up -d

จากนั้น ลองเรียกใช้งานอีกทีครับผม

กรณี ที่เราจะ monitor mysql มากกว่า 1 server ในหน้า mysql overviews ให้เราเลือกตรงส่วนของ host ครับ มันจะมี host มาให้เลือก ตามที่เรา config ไว้ในไฟล์ prothemeus.yml ครับผม

[![Screen-Shot-2563-06-25-at-10-03-21.png](https://i.postimg.cc/9FcZr6jg/Screen-Shot-2563-06-25-at-10-03-21.png)](https://postimg.cc/HjPVNPnb)

[![Screen-Shot-2563-06-25-at-10-03-36.png](https://i.postimg.cc/90CRw7sw/Screen-Shot-2563-06-25-at-10-03-36.png)](https://postimg.cc/njSc6CFn)



#####เสร็จเรียบร้อยแล้วครับ คู่มือแบบที่ 2
