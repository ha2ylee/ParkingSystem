# AWS를 이용한 주차관리시스템
<br/>

# 💡 프로젝트 소개
본 프로젝트는 <클라우드IoT서비스>에서 진행한 프로젝트입니다.

1) 초음파 센서를 이용하여 다가오는 차량을 감지하고 카메라 모듈을 이용하여 차량의 번호판을 촬영한 후 이미지로 S3 버킷에 저장

2) AWS Rekognition을 사용하여 촬영된 번호판 이미지의 텍스트 추출

3) 추출된 텍스트를 데이터베이스와 비교하여 같은 번호가 존재하면 출차, 없으면 입차 처리

# 💡 시스템 구성도

<img src="https://user-images.githubusercontent.com/76219962/178237775-dbb801b3-7f07-43af-be2e-c40f82e98054.png" width="600px" height="300px">

# 💡 라즈베리파이 환경세팅
사용 부품 : 라즈베리 파이 3 Model B, 초음파 센서(HC-SR04), 카메라 모듈(Camera V2.1)<br/>
<img src="https://user-images.githubusercontent.com/76219962/178239183-7448a8ae-1f20-49a6-9f24-edcce08592a9.png" width="600px" height="600px">

# 💡 프로젝트 구현
### 1. AWS 세팅
- 사물(camera,manage)생성후 정책 연결
<img src = "https://user-images.githubusercontent.com/76219962/178241042-36064dfb-89e6-4248-be4a-f9a8e584d84e.png" width="600px" height="300px">
<img src="https://user-images.githubusercontent.com/76219962/178241121-0c342c7a-2cc6-4f35-a5c1-12533ba17745.png" width="600px" height="300px">
<br/>

- Role 생성 : IAM
람다에게 접근 권한을 부여하기 위한 role을 생성함 -> rekognition, s3, iot등에 접근권한 부여 
<img src="https://user-images.githubusercontent.com/76219962/178241462-f7fd1308-901e-44f8-931b-7811c84f269c.png" width="600px" height="300px">
<br/>

- 람다 생성
<img src="https://user-images.githubusercontent.com/76219962/178241826-d861b277-d3ce-4525-9d24-c1ec158c9698.png" width="600px" height="400px">
<br/>

- Rule 생성
‘carRecog/request’ 토픽에서 메시지를 읽어오기 위한 Rule(SQL SELECT Clause)을 생성함
<img src="https://user-images.githubusercontent.com/76219962/178242089-e3891755-106b-483f-8668-8cc18ffec653.png" width="600px" height="300px">
<br/>

- 트리거 생성
‘carRecog/request’ 토픽에 메시지가 들어오면 람다함수를 호출 할 트리거를 추가
<img src="https://user-images.githubusercontent.com/76219962/178243117-ceea0099-9f22-4bc6-9df2-8a2c59100d59.png" width="600px" height="300px">
<br/>
<br/>

### 2. 코드 

- camera.js :
라즈베리 파이의 초음파 센서로 차량과의 거리를 측정하여 20cm 이내가 되면 카메라 모듈로 번호판을 촬영한다.<br/>
촬영 된 이미지는 S3의 버킷으로 올라가며 이때 ‘carRecog/request’ 토픽으로 ‘carRecog/detect/car’라는 토픽명과 이미지 파일명을 publish한다. 
- index.js : 
camera.js에서 ‘carRecog/request’ 토픽으로 Publish한 메시지가 들어오면 SQL SELECT Clause로 모든 값을 읽고, 트리거를 통해 람다 함수를 호출한다.<br/>
S3에 업로드 된 이미지를 사용해서 Rekognition 진행 후 감지 된 텍스트를 ‘carRecog/detect/car’ 토픽으로 publish한다.
- manage.js :
‘carRecog/detect/car’ 토픽을 subscribe하고 있다가 람다 함수가 publish한 메시지가 토픽에 들어오면 메시지에서 차량 번호를 읽어 데이터베이스와 비교한다.<br/>
이때 데이터베이스에 해당 차량 번호가 있다면 번호 삭제 후 출차 처리, 없다면 차량 번호 등록 후 입차 처리한다.

### 3. 결과
- camera.js
<img src="https://user-images.githubusercontent.com/76219962/178243609-295f0106-1806-40d4-a947-0205defcb85f.png" width="600px" height="300px">

- manage.js
<img src="https://user-images.githubusercontent.com/76219962/178243625-d482bd2a-2a91-45f9-9073-1e6e6c2f81d9.png" width="500px" height="200px">

# 🛠️ 기술

- AWS IOT Core
- AWS Lambda
- AWS S3
- AWS Rule
- AWS IAM
- AWS Rekognition
- AWS RDS
- Node.js

