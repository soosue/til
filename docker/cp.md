## docker container에서 로컬로 파일 복사하기

1. docker ps를 이용해 container의 id를 찾아낸다.

![image](https://user-images.githubusercontent.com/54942017/165113504-7fe43c4d-75cc-4be5-8f7c-c4f2c4444f7e.png)

2. container에서 복사하고 싶은 파일의 위치를 파악한다.

![image](https://user-images.githubusercontent.com/54942017/165113823-48457004-d211-4dc0-8dde-26fa5e062893.png)

3. `docker cp 컨테이너id:컨테이너파일위치 로컬파일위치`명령어를 이용해서 복사한다.

![image](https://user-images.githubusercontent.com/54942017/165114427-57043b9b-cd18-4b23-a7ff-e8b136669d66.png)
