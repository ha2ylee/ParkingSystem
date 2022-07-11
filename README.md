# AWS를 이용한 주차관리시스템
<br/>

### 💡 프로젝트 소개
본 프로젝트는 '클라우드IoT서비스'에서 진행한 프로젝트입니다.

1) 초음파 센서를 이용하여 다가오는 차량을 감지하고 카메라 모듈을 이용하여 차량의 번호판을 촬영한 후 이미지로 S3 버킷에 저장

2) AWS Rekognition을 사용하여 촬영된 번호판 이미지의 텍스트 추출

3) 추출된 텍스트를 데이터베이스와 비교하여 같은 번호가 존재하면 출차, 없으면 입차 처리

### 💡 시스템 구성도

<img src="https://user-images.githubusercontent.com/76219962/178237775-dbb801b3-7f07-43af-be2e-c40f82e98054.png"  width="600" height="300">

### 💡 라즈베리파이 환경세팅
<img src="https://user-images.githubusercontent.com/76219962/178238057-da21f20b-d567-465c-8a6e-7a75f3f13c49.png"  width="600" height="300">
