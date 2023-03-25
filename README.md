# DOCKER
## Docker 

<div align=center><img src="img/Docker-Logo.jpg"></div>

- แพลตฟอร์มที่ช่วยในการ สร้าง ทดสอบ ใช้งานแอปพลิเคชันจริง ที่มีเฉพาะสภาพแวดล้อมที่พร้อมสำหรับแอปพลิเคชัน เป็นเหมือนกับ container ทำให้ติดตั้งแอปพลิเคชันได้รวดเร็ว ง่ายต่อการนำมาใช้ และการจัดการแอปพลิเคชัน โดยควบคุมและทำงานภายใต้ docker engine

## Docker command basic
- ตรวจสอบเวอร์ชั่น
  ```
  docker version
  ```

- แสดงสถานะการทำงานของ docker container แบบ realtime
  ```
  docker stats
  ```

<div align=center><img src="img/dockerstats.png" width="800px"></div>

- แสดง logs ของ docker container ตั้งแต่เริ่มทำงาน
  ```t
  docker logs [ID หรือ ชื่อ container] [option]
  ```
  - option
    - -f : ดูแบบ process realtime หรือ ฟอร์กาว
    ```
    docker logs [ID หรือ ชื่อ container] -f
    ```
    
- ตรวจสอบ docker container ที่กำลังทำงาน
  ```
  docker ps [option]
  ```
  - option
    - -a : แสดง docker container ทั้งหมด ทั้งที่ทำงานอยู่ และที่เคยทำงาน
    ```
    docker ps -a
    ```

- ดาวโหลด docker image จาก dockerhub
  ```
  docker pull [image]:[tag]
  ```

- เริ่มทำงาน docker container จาก docker image โดยหากในเครื่องไม่มีจะทำการ pull จาก dockerhub
  ```
  docker run [option] [image]
  ```
  - option 
    - --name : กำหนดชื่อ docker container
    - -p : กำหนด port ในการใช้งาน service บน docker container 
	[port ที่ให้ทำงานบน Host]:[port ที่ service ทำงานบน docker container]
    - -d : demon สั่งให้ทำงานในพื้นหลัง จะตรงข้ามกับ -f
    - -v : พื้นที่เก็บข้อมูลที่ต้องการให้เชื่อมต่อกันระหว่าง host กับ docker container ป้องกันการสูญเสียข้อมูลหาก container down
	[path ใน host]:[path ใน docker container ที่ต้องการให้เชื่อมต่อ]
    - --hostname : กำหนด hostname
    ```
    docker run --name nginx -p 80:80 -d -v ./app:/var/html/www/ --hostname nginx nginx #ค่าสุดท้าย คือ image ที่ต้องการ
    ```

- สั่ง docker container ที่ปิดใช้งานอยู่ ให้เปิดใช้งาน
  ```
  docker start [ID หรือ ชื่อ container]
  ```

- สั่ง docker container ที่เปิดใช้งานอยู่ ให้ปิดใช้งาน
  ```
  docker stop [ID หรือ ชื่อ container]
  ```

- ลบ docker container
  ```
  docker rm [ID หรือ ชื่อ container] [option]
  ```
  - option 
    - -f : ลบ container ที่กำลังทำงานได้ เพราะหากสั่งลบ โดยไม่ใช้ option นี้ จะไม่สามารถลบ docker container ที่กำลังทำงานได้
    ```
    docker rm [ID หรือ ชื่อ container] -f
    ```
- แสดงรายการ image ที่อยู่ในเครื่อง
  ```
  docker images
  ```

<div align=center><img src="img/dockerimages.png" width="800px"></div>

- ลบ image ที่อยู่ในเครื่อง
  ```
  docker rmi [repo หรือ id image]
  ```

- สร้าง image จาก Dockerfile
  ```
  docker build [path Dockerfile] [option]
  ```
  - option
    - -t กำหนด repository image:tag image
      ```
      docker build Dockerfile -t [username/repository]:[tag]
      ```
      - username : username dockerhub
      - repository : repository ที่ต้องการใน dockerhub
      - tag : ห้ามซ้ำกับชื่อ repository เดียวกัน เป็นเหมือน version

<div align=center><img src="img/dockerbuild.png" width="800px"></div>

- อัปโหลด image ไปที่ repository 
  - โดยในที่นี้คือ dockerhub โดยต้องทำการ login dockerhub ก่อน
  ```
  docker push [repository image]:[tag]
  ```
  - [repository image] => username/repository
    - username : username dockerhub
    - repository : repository หากยังไม่สร้าง จะทำการสร้างโดยอัตโนมัติ
  
<div align=center><img src="img/dockerbuild.png" width="800px"></div>

- login dockerhub
  ```
  docker login
  ```

## Docker compose yml
- ช่วยให้สามารถสร้าง docker container ได้ โดยกำหนดค่าต่าง ๆ ผ่านไฟล์ .yaml และ ง่ายต่อการจัดการ docker container
- ตัวอย่างไฟล์
  ```yaml
  services: #กำหนดเพื่อสร้าง services
  web: #ชื่อ services
    build: # build image จาก docker file และนำ image มาใช้งาน
      context: app #path docker file
      target: builder
    ports: #กำหนด port การทำงาน
      - '80:80' # port บน host : port ที่ service ทำงานบน docker container
    volumes: #กำหนดที่เก็บข้อมูลให้ทำการเชื่อมต่อกัน
      - ./app:/var/www/html/ # path บน host : path บน container
  ```

- สร้าง docker container จากไฟล์ .yaml
  ```
  docker compose -f .\compose.yaml up [option]
  ```
  - -f : กำหนดให้รัน path filename .yaml
  - option
    - --build : สร้าง image ใหม่ ตามคำสั่งในไฟล์ yaml โดยหากมีแล้วจะสร้างซ้ำ
    - -d : demon รันเสร็จทำงานอยู่เบี้องหลัง

- ลบ docker container ที่สร้างจากไฟล์ .yaml โดยจะลบ container และ network ออกจากเครื่อง จึงง่ายต่อการจัดการ
  ```
  docker compose -f .\compose.yaml down
  ```
  - -f : กำหนดให้รัน path filename .yaml

## Docker swarm

