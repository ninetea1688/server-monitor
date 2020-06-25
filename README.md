# การทำระบบ Monitor Server * MySQL ง่าย ๆ ด้วย Docker (grafana + prometheus + node exportor + mysql exportor)
## คู่มือนี้ มี 2 แบบ 
1. clone แล้วใช้ไปเลย บ่อยากเข้าใจหยัง
2. มันเฮ็ดงานจั่งใด๋วะ

### คู่มือแบบที่ 1

git clone https://github.com/cybernude/server-monitor.git
cd server-monitor
#### จากนั้น ทำการแก้ไขทำการแก้ไขไฟล์  docker-compose.yml ด้วย editor ที่ถนัด
แก้ไขค่าการเชื่อมต่อ database ของเรา จะอยู่ประมาณบรรทัดที่ 12
    - "DATA_SOURCE_NAME=root:123456@(10.0.0.181:3333)/"
    
    root = user ของฐานข้อมูล
    123456 = password ของฐานข้อมูล
    10.0.0.181 = ip ของฐานข้อมูล
    3333 = port ที่เชื่อมต่อ database ของเรา
