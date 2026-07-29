# 내 컴퓨터에 개발자용 작업실 꾸미기

## 1. 프로젝트 개요
macOS 환경에서 개발에 필요한 기본 도구(터미널, Docker, Git)를 설치하고 웹 서버 컨테이너를 직접 만들어 실행하는 과제입니다.

## 2. 실행 환경
- OS: macOS
- Shell: zsh
- Docker: OrbStack
- Git: 설치 및 사용자 설정 완료

## 3. 수행 내용

### 3.1 작업 폴더 생성
mkdir codyssey_mission1
cd codyssey_mission1
pwd
ls -la
![작업 폴더 확인](images/01_pwd_ls.png)

### 3.2 이미지 폴더 생성
mkdir images
![이미지 폴더 확인](images/03_images_folder.png)

### 3.3 Docker 설치 확인
docker version
docker run hello-world
![Docker 버전](images/04_docker_version.png)
![Hello World](images/05_hello_world.png)

### 3.4 기본 파일 조작
mkdir, touch, echo, cat, cp, mv, chmod, rm 명령어 실습
![파일 조작](images/08_file_operations.png)

### 3.5 Git 설정
git config --global user.name / user.email 설정 완료
(개인정보 보호를 위해 실제 값은 생략)
![Git 초기화](images/10_git_init.png)

### 3.6 Dockerfile로 웹 서버 이미지 만들기
FROM nginx:alpine
LABEL org.opencontainers.image.title="my-custom-nginx"
COPY index.html /usr/share/nginx/html/index.html
![Dockerfile](images/12_dockerfile.png)
![index.html](images/13_index_html.png)

### 3.7 이미지 빌드 및 컨테이너 실행
docker build -t my-web:1.0 .
docker run -d -p 8080:80 --name my-web-server my-web:1.0
curl http://localhost:8080
![빌드](images/14_docker_build.png)
![포트 매핑](images/16_browser.png)

### 3.8 볼륨을 이용한 데이터 영속성 확인
컨테이너를 삭제해도 볼륨에 데이터가 남아있는 것을 확인했습니다.
![볼륨 영속성](images/17_volume_persist.png)

### 3.9 GitHub 연동
git remote add origin https://github.com/본인아이디/codyssey-mission1.git
git add .
git commit -m "첫 번째 커밋: 개발환경 구축 과제"
git push -u origin main
![Git Remote](images/18_git_remote.png)
![Git Push](images/19_git_push1.png)

## 4. 검증 방법
- docker ps로 컨테이너 실행 확인
- curl http://localhost:8080으로 웹 서버 응답 확인
- 볼륨 삭제 없이 컨테이너만 삭제 후 데이터 유지 확인

## 5. 트러블슈팅
- zsh에서 ! 문자 때문에 echo 오류 발생 → 작은따옴표 사용으로 해결
- GitHub 로그인 시 비밀번호 대신 Personal Access Token 사용