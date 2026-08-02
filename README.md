# 내 컴퓨터에 개발자용 '작업실' 꾸미기

Codyssey Mission 1 — 개발 워크스테이션 구축

- 저장소: https://github.com/3043382-svg/codyssey_mission1
- 제출자: qreitor

> 로컬 폴더 이름은 `coddyssey_mission1`인데 저장소 이름은 `codyssey_mission1`입니다. 처음에 폴더를 만들 때 오타를 낸 걸 나중에 알았는데, 이미 그 이름으로 계속 작업해서 그냥 뒀습니다. 스크린샷 프롬프트랑 저장소 이름이 다르게 보이는 건 이 때문입니다.

---

## 1. 프로젝트 개요

저는 개발을 모르는 상태에서 이 과제를 시작했습니다. 터미널, Docker, Git이 뭔지도 몰랐고, 명령어 하나 칠 때마다 이게 무슨 뜻인지부터 이해하려 했습니다.
크게 세 가지를 했습니다.

- 터미널로 폴더/파일을 만들고 다뤄보기, 권한 바꿔보기
- Docker 설치하고, 컨테이너 실행하고, 제 웹 서버 이미지 만들어보기
- Git이랑 GitHub 연결해서 이 문서와 캡처들을 올리기

명령어를 치고 결과가 어떻게 바뀌는지 눈으로 확인하면서 조금씩 배웁니다.

---

## 2. 실행 환경

| 항목 | 값 | 
|---|---|
| OS | macOS (Apple Silicon, arm64) |
| Shell | zsh | 
| 컨테이너 런타임 | OrbStack (Docker Context: `orbstack`) | 
| Docker Client | 29.4.0 (API 1.54) | 
| Docker Engine | 29.4.0 / linux/arm64 | 
| Git | 2.54.0 | 

과제 안내 자료에 OrbStack를 쓰라고 해서 씀. OrbStack을 켜두면 뒤에서 Docker가 같이 켜지고, 터미널에서는 그냥 `docker`로 시작하는 명령어를 똑같이 쓰면 된다고 유튜브에서 배움.

이 문서에 있는 명령어들은 제 맥북 기준으로 실행. 다른 컴퓨터에서는 8080이나 8081 포트가 이미 쓰이고 있을 수도 있는데, 그럴 땐 다른 숫자로 바꿔서 실행하면 될 것 같습니다.


---

## 3. 수행 항목 체크리스트

- [x] 터미널 기본 조작 (위치 확인, 목록 확인, 이동, 생성, 복사, 이름변경, 삭제)
- [x] 파일 내용 확인 및 빈 파일 생성
- [x] 파일 권한 변경 실습 (전/후 비교)
- [x] 디렉토리 권한 변경 실습 (전/후 비교)
- [x] Docker 설치 및 데몬 동작 점검
- [x] Docker 기본 운영 명령 (`images` / `ps` / `ps -a` / `logs` / `stats`)
- [x] `hello-world` 컨테이너 실행
- [x] `ubuntu` 컨테이너 대화형 진입 및 내부 명령 수행
- [x] 컨테이너 종료/유지 차이 관찰
- [x] Dockerfile 기반 커스텀 이미지 제작
- [x] 포트 매핑 및 브라우저 접속 검증
- [x] 바인드 마운트 — 호스트 변경 반영 확인
- [x] Docker 볼륨 — 데이터 영속성 확인
- [x] Git 사용자 정보 및 기본 브랜치 설정
- [x] GitHub 저장소 연동 및 푸시
- [x] VSCode ↔ GitHub 연동
- [x] 민감정보 마스킹

---


## 4. 수행 내용

### 4.1 터미널로 폴더/파일을 만들고 다뤄보기, 권한 바꿔보기

```bash
mkdir coddyssey_mission1
cd coddyssey_mission1
pwd
ls -la
```

`pwd`는 지금 내가 어느 폴더에 있는지 보여주는 명령어,
`ls -la`로 숨김 파일까지 다 나오는 목록을 봤습니다.

![작업 폴더 확인](images/01_pwd_ls.png)

```bash
mkdir images
ls -la
```
이미지스 하위 디렉토리를 만들고 la -la로 확인, 바탕화면 캡춰파일 2개를 mv 명령어로 이미지스 디렉토리로 이동, ls -la로 확인
![images 폴더 생성](images/02_mkdir_images.png)
![images 폴더 확인](images/03_images_folder.png)

**절대 경로랑 상대 경로**

pwd로 나온 `/Users/parkmacbook/Desktop/coddyssey_mission1`는 절대 경로  
맨 앞이 `/`로 시작하면 어디서 실행하든 같은 위치

반대로 `mkdir images`처럼 그냥 이름만 쓴 건 상대 경로  
지금 있는 폴더 기준으로 동작

`/Users/parkmacbook/Desktop/coddyssey_mission1`  절대  `/`부터 시작하는 전체 경로 

`~/Desktop/coddyssey_mission1`  `~`는 홈 폴더로 자동으로 바뀜 

`images` 상대 지금 위치 바로 밑의 images 

 `./images`  상대  `.`은 지금 있는 폴더 

 `../images`  상대  `..`은 한 칸 위 폴더 

 `practice/test.txt`  상대  지금 위치 밑에 있는 파일 


이후 이 문서에 쓴 명령어는 대부분 상대 경로로 씀. 컴퓨터 이름(`parkmacbook`)이 들어간 절대 경로를 그대로 쓰면 다른 사람 컴퓨터에서는 안 맞을 것 같아서.

```bash
mkdir practice
touch practice/test.txt
ls -la practice
```

![빈 파일 생성](images/06_touch_test.png)
프랙티스 디렉토리 만들고 테스트라는 빈 텍스트 파일 생성. 또 확인

```bash
echo 'Hello Codyssey' > practice/test.txt
cat practice/test.txt
```

헬로 코디세이라는 글자를 에코 명령어로 텍스트파일에 넣고 캣 명령어로 내용 다시 확인

![내용 확인](images/07_echo_cat.png)

```bash
cp practice/test.txt practice/test-copy.txt
ls -la practice

mv practice/test-copy.txt practice/test-renamed.txt
ls -la practice

rm practice/test-renamed.txt
ls -la practice
```

한 줄 칠 때마다 `ls -la`로 확인하면서 진행. 

테스트라는 파일을 카피해서 테스트-카피라는 파일로 이름 바꿔 저장. 확인. 이 파일을 테스트-리네임드로 이름 변경, 확인. 

mv 예전이름.txt 새이름.txt    - 같은 폴더 안에서 이름만 바꿈 → "이름 변경"

mv 파일.txt ~/다른폴더/           # 이름은 그대로, 폴더만 옮김 → "이동"

mv 예전이름.txt ~/다른폴더/새이름.txt   # 폴더도 옮기고 이름도 바꿈 → 둘 다 동시에



![파일 조작](images/08_file_operations.png)

**파일 권한 변경 (전/후 비교)**

```bash
ls -l practice/test.txt
# -rw-r--r--   (644: 소유자 읽기·쓰기, 그룹/기타 읽기)

chmod 755 practice/test.txt
ls -l practice/test.txt
# -rwxr-xr-x   (755: 소유자 읽기·쓰기·실행, 그룹/기타 읽기·실행)
```
화면 하단 반쯤지나서 부터 (그전은 위의 명령어들)
텍스타파일의 권한을 확인. 그리고 755로 변경하고 다시 확인. 파일 이후 삭제. 다시 확인.

소유자·그룹·기타 세 그룹에 대해 각각 읽기(r=4)·쓰기(w=2)·실행(x=1) 권한을 부여하는 방식, 세 자리 숫자는 각 그룹의 값을 합산

맨 앞이 - 면 파일, d면 디렉토리, 디렉토리는 실행이 아니라 그 안으로 들어감


![파일 권한 변경](images/08_file_operations.png)


**디렉토리 권한 변경 (전/후 비교)**

```bash
ls -ld practice
chmod 700 practice
ls -ld practice
chmod 755 practice
ls -ld practice
```

![디렉토리 권한 변경](images/20_chmod_dir.png)

**권한 숫자, 이해한 대로 정리**

세 자리 숫자가 순서대로 소유자·그룹·다른 사람 권한, 각 자리는 읽기(4) + 쓰기(2) + 실행(1)을 더한 값이라고 확인.

chmod 700, 755로 권한이 바뀌는 것 확인 `-rwxr-xr-x` `-rwx---`   
정확히 외워야 하는가?

폴더에서는 `x`가 실행이 아니라 그 안으로 들어갈 수 있냐(cd 가능 여부).

---

### 4.2 Docker 설치하고, 컨테이너 실행하고, 제 웹 서버 이미지 만들어보기

```bash
docker version
```

Client랑 Server가 둘 다 나오면 잘 깔려있고 잘 켜져 있는 것. Context가 `orbstack`이라고 뜨는 걸로 지금 OrbStack을 쓰고 있다는 걸 확인.

![Docker 버전](images/04_docker_version.png)

**hello-world**

```bash
docker run hello-world
```

이미지가 컴퓨터에 없으니까 처음엔 자동으로 다운로드부터 하고 그다음에 실행.
hello-world는 Docker가 공식적으로 만들어서 배포하는, 아주 작고 간단한 테스트 전용 이미지입니다. 이게 하는 일은 딱 하나입니다. 실행되면 화면에 "Hello from Docker!"라는 메시지를 출력하고, 몇 줄의 설명을 더 보여준 뒤 바로 끝납니다. 실제로 뭔가 유용한 프로그램이 들어있는 게 아니라, 순전히 "Docker가 제대로 동작하는지 확인하기 위한 용도"로만 존재

![Hello World](images/05_hello_world.png)



**Dockerfile**

```dockerfile
FROM nginx:alpine
LABEL org.opencontainers.image.title="my-custom-nginx"
COPY index.html /usr/share/nginx/html/index.html
```

**index.html**

```html
<h1>Hello from My Custom Nginx!</h1>
<p>Codyssey Mission 1 - Docker Web Server</p>
```

**뭘 넣었고 왜 넣었는지**

터치 도커파일로 빈 파일 만듬 

프롬은 베이스 이미지를 지정하는 것. 엔진엑스를 직접 설치하는게 아니라 이미 설치된 이미지를 가져다 씀

라벨은 이미지에 이름표 부여

카피는 내 컴퓨터 파일을 이미지 내부로 복사

| 줄 | 하는 일 | 넣은 이유 |
|---|---|---|
| `FROM nginx:alpine` | alpine이라는 가벼운 버전의 nginx 사용 | 용량 작고, 웹서버 설치를 처음부터 안 해도 돼서 |
| `LABEL` | 이미지에 이름표 붙이기 | 나중에 이 이미지가 뭔지 알아보려고 |
| `COPY` | 제 index.html을 컨테이너 안으로 복사 | 원래 있던 nginx 화면 대신 내 화면이 뜨게 하려고 |

![Dockerfile](images/12_dockerfile.png)
파일안에 글자 넣기
index.html에 넣은 글자 설명

<`<h1>`, `<p>`는 HTML 태그로, 브라우저에 글자를 어떻게 보여줄지 정하는 문법입니다. `<h1>`은 가장 큰 제목, `<p>`는 본문 문단을 뜻합니다.

nginx는 원래 자기만의 기본 환영 페이지("Welcome to nginx!")를 갖고 있는데, 이 index.html을 Dockerfile의 `COPY`로 컨테이너 안에 넣었기 때문에 그 기본 화면 대신 "Hello from My Custom Nginx!"라는 제 문구가 뜬 겁니다. 브라우저에서 이 화면이 보이는 게 곧 커스텀 이미지가 제대로 만들어졌다는 증거입니다.

![index.html](images/13_index_html.png)

**빌드**

```bash
docker build -t my-web:1.0 .
docker images | grep my-web
```
docker build -t ... → tag (이름표 붙이기)

도커 빌드 명령으로 설계도를 실제 이미지로 만듬. 기존 이미지를 그대로 쓰는게 아니라, 설정은 안건드리고 컨텐츠만 바꾼 커스터마이징
이름(my-web)은 "뭘 만들었는지" 알아보기 위한 것이고, 태그(1.0)는 "몇 번째 버전인지" 구분하기 위한 것입니다. 둘 다 실제로 프로그램 동작에 영향을 주는 건 아니고, 순전히 사람이 알아보기 쉽게 붙인 이름표입니다.

docker images | grep my-web
방금 만든 이미지가 실제로 잘 만들어졌는지 확인하는 명령어입니다.

docker images = 내 컴퓨터에 저장된 이미지 전체 목록을 보여줍니다.
| grep my-web = 그 목록이 너무 많을 수 있으니, "my-web"이라는 글자가 들어간 줄만 골라서 봅니다.

![빌드](images/14_docker_build.png)

```bash
docker run -d -p 8080:80 --name my-web-server my-web:1.0
docker ps
curl http://localhost:8080
```
컨테이너는 호스트(내 컴퓨터)와 네트워크가 격리. 컨테이너 안에서 nginx가 80번 포트로 정상 동작해도, 이 격리 때문에 호스트 브라우저에서는 기본적으로 접근할 수 없음. 그래서 서로 연결하고, 이후 브라우저에서 로컬호스트 8080으로 접속하면 그 요청이 컨테이너 내부 80번 포트로 전달됨.

docker run -d -p 8080:80 --name my-web-server my-web:1.0

이제 그 이미지로 실제 컨테이너를 실행하는 명령어입니다. 옵션 4개가 붙어있습니다.

-d = detached(분리된)의 약자. 컨테이너를 화면에 붙잡아두지 않고 뒤에서 계속 돌아가게 합니다. 이게 없으면 터미널이 그 컨테이너에 묶여서 다른 명령어를 못 치게 됩니다.
-p 8080:80 = port의 약자. 앞서 배운 것처럼 내 컴퓨터의 8080번 문을 컨테이너의 80번 문에 연결하는 겁니다.
--name my-web-server = 이 컨테이너의 이름을 짓는 옵션. 이름을 안 주면 Docker가 랜덤한 이름(예: happy_einstein)을 붙여줍니다.
my-web:1.0 = 어떤 이미지로 컨테이너를 만들지 지정하는 부분. 아까 -t로 이름 붙였던 그 이미지를 여기서 씁니다.

![실행 및 curl 응답](images/15_browser.png)

docker ps

컨테이너가 진짜 실행 중인지 확인합니다. my-web-server가 Up 상태로 목록에 보이면 성공입니다.

curl http://localhost:8080

브라우저를 열지 않고, 터미널에서 곧바로 웹페이지 내용을 가져와 봅니다.

curl은 웹 요청을 보내는 도구입니다. http://localhost:8080으로 요청을 보내면, 그 요청이 포트 매핑(-p 8080:80)을 타고 컨테이너 안의 nginx로 전달됩니다.

![브라우저 접속](images/16_browser.png)

컨테이너는 컴퓨터랑 네트워크가 따로 분리되어 있어서, `-p 8080:80`처럼 포트를 연결해줘야 밖에서 들어갈 수 있다고 함. 컨테이너 안쪽 포트(80)는 그대로 두고 바깥쪽 포트만 바꾸면 같은 이미지를 여러 포트로 동시에 띄우는 것도 가능

```bash
docker volume create mydata

docker run -d --name vol-test -v mydata:/data ubuntu sleep infinity
docker exec vol-test bash -c "echo hello > /data/hello.txt"
docker exec vol-test cat /data/hello.txt
# hello

docker rm -f vol-test

docker run -d --name vol-test2 -v mydata:/data ubuntu sleep infinity
docker exec vol-test2 cat /data/hello.txt
# hello
```

컨테이너 자체의 파일 시스템은 컨테이너가 삭제되면 같이 사라짐, 볼륨은 이 컨테이너와 분리된 도커가 관리하는 별도의 공간임.

컨테이너를 완전히 지운 다음에 새 컨테이너를 하나 더 만들어서 같은 볼륨을 연결해봤는데, 아까 저장한 `hello`라는 내용이 그대로 남아 있음. 컨테이너를 지워도 볼륨 안의 데이터는 안 지워진다는 걸 확인.

docker volume create mydata

mydata라는 이름의 저장 공간(볼륨)을 만드는 명령어입니다.

docker volume create = "볼륨을 만들어줘"라는 뜻이고, 그 뒤에 붙는 mydata가 이 볼륨의 이름입니다.
이 시점에는 아직 아무 데이터도 안 들어있는 빈 저장 공간이 하나 생긴 것뿐입니다.

docker run -d --name vol-test -v mydata:/data ubuntu sleep infinity

이 볼륨을 실제로 컨테이너에 연결해서 실행하는 명령어입니다. 새로운 옵션이 하나 있습니다.

-d = 앞서 배운 detached, 뒤에서 계속 실행
--name vol-test = 이 컨테이너 이름
-v mydata:/data = volume의 약자. mydata(방금 만든 볼륨)를 컨테이너 안의 /data라는 폴더에 연결합니다. 형식은 볼륨이름:컨테이너경로입니다.
ubuntu = 사용할 이미지
sleep infinity = "영원히 잠들어 있어라"라는 명령. 이게 왜 필요하냐면, 우분투 컨테이너는 딱히 할 일이 없으면 명령어를 다 실행하고 바로 꺼져버립니다. sleep infinity로 일부러 아무것도 안 하고 계속 켜져 있게 만든 겁니다. 이게 없으면 컨테이너가 바로 Exited 상태가 돼서 뒤에 나오는 docker exec 명령을 칠 수가 없습니다.

docker exec vol-test bash -c "echo hello > /data/hello.txt"

vol-test 컨테이너 안에 접속해서, /data 폴더(아까 볼륨을 연결해둔 그 폴더) 안에 hello.txt라는 파일을 만들고 그 안에 hello라는 글자를 씁니다.

docker exec vol-test = vol-test 컨테이너 안에 들어가서
bash -c "..." = "이 명령어 한 줄만 실행하고 나와라"
echo hello > /data/hello.txt = hello라는 글자를 그 파일에 씀

이 단계에서 볼륨 안에 실제 데이터가 하나 심어진 겁니다.

docker exec vol-test cat /data/hello.txt

방금 넣은 내용이 잘 저장됐는지 바로 열어서 확인합니다. 화면에 hello가 나오면 정상입니다.


![볼륨 영속성](images/17_volume_persist.png)
docker rm -f vol-test

이게 이 실습 전체의 핵심 순간입니다. vol-test 컨테이너를 강제로(-f, force) 완전히 삭제합니다.

컨테이너를 통째로 지워버리는 거라, "컨테이너가 사라지면 그 안의 데이터도 같이 사라지는지"를 시험하는 단계입니다.

docker run -d --name vol-test2 -v mydata:/data ubuntu sleep infinity

아까와 완전히 똑같은 명령어인데, 이름만 vol-test2로 바뀌었습니다. 중요한 건 같은 볼륨(mydata)을 다시 연결한다는 점입니다. vol-test는 이제 이 세상에 없고, vol-test2는 완전히 새로 만든 별개의 컨테이너입니다.

docker exec vol-test2 cat /data/hello.txt

새로 만든 vol-test2 안에서 /data/hello.txt를 열어봅니다. 여기서 hello가 그대로 나왔다는 게 결론입니다. 컨테이너(vol-test)는 완전히 사라졌는데, 볼륨(mydata) 안의 데이터는 새 컨테이너(vol-test2)에서도 그대로 보인다 — 이게 "볼륨은 컨테이너의 생애주기와 분리돼 있다"는 걸 직접 증명한 부분입니다.


**바인드 마운트랑 볼륨**

| 구분 | 데이터가 어디 저장되는지 | 언제 쓰는지 |
|---|---|---|
| 바인드 마운트 | 제 컴퓨터의 특정 폴더 | 파일 수정하면서 바로바로 확인할 때 |
| 볼륨 | Docker가 알아서 관리하는 공간 | 데이터를 오래 보관해야 할 때 |

> 여기까지는 처음 진행했던 날 한 것이고, 아래는 요구사항을 다시 확인하고 나중에 추가로 한 부분입니다.

```bash
docker info | head -25
```
한번 실패해서 다시 켜고 도커인포 요청, 25줄만 나오게

![Docker info](images/21_docker_info.png)

```bash
docker images                      # 로컬 이미지 목록
docker ps                          # 실행 중 컨테이너
docker ps -a                       # 종료된 컨테이너 포함 전체
docker logs my-web-server | tail -10   # 컨테이너 로그
docker stats --no-stream           # 리소스 사용량
```
피에스만 하면 실행중인 이미지만, -a를 붙이면 종료된 것까지 전부 보여줌. 컨터이너는 꺼져도 자동삭제되지 않고 exited 상태로 남음

이미지는 컨테이너를 만들기 위한 설계도(읽기 전용 템플릿)이고, 컨트이너는 그 이미지로 실제 실행되는 인스턴스. 같은 이미지로 여러개 컨테이너 동시에 만들 수 있음.

![docker ps](images/15_browser.png)
도커 런을 하고 피에스를 함

![Docker 운영 명령 - images/ps](images/22_docker_ops1.png)
도커 이미지스를 명령함

![Docker 운영 명령 - logs](images/22_docker_ops2.png)
![Docker 운영 명령 - stats](images/22_docker_ops3.png)

`docker ps`는 지금 켜져 있는 것만, `docker ps -a`는 예전에 켰다가 꺼진 것까지 다 보여줍니다. 컨테이너는 꺼져도 자동으로 없어지지 않고 `Exited`라고 표시된 채로 남아 있어서, 계속 쌓임.

docker images — 이미지 목록

docker ps — 실행 중인 컨테이너만

docker ps -a — -a(all)로 꺼진 것까지 전부

docker logs my-web-server | tail -10 — my-web-server의 기록을 보되, tail -10으로 마지막 10줄만

docker stats --no-stream — CPU·메모리 사용량을 한 번만 찍고 멈춤(--no-stream 없으면 계속 갱신되며 안 멈춤)

**ubuntu 안에 들어가보기**

```bash
docker run -it --name ub-test ubuntu bash
# 컨테이너 안에서
ls /
echo "hello from container"
exit
```

**꺼진 거랑 켜진 거, 뭐가 다른지**

```bash
docker ps -a | grep ub-test        # STATUS: Exited
docker start ub-test               # 다시 켜기
docker exec -it ub-test bash -c "echo restarted"
```

![ubuntu 대화형 진입](images/23_ubuntu_interactive.png)

-it의 아이는 인터랙티브(대화형 입력 전달), -t는 티티와이(터미널 화면 할당)임. 둘을 합치면 컨테이너 안에 직접 들어가 있는 것처럼 명령어 입력 가능.

직접 해보고 헷갈렸던 걸 표로 정리

| 명령 | 뭘 하는지 | 컨테이너 상태 |
|---|---|---|
| `docker run -it ... bash` | 새로 만들어서 그 안에 들어감 | 나오면(exit) `Exited` |
| `docker run -d ...` | 뒤에서 계속 실행 | `Up` 상태 유지 |
| `docker start` | 꺼진 걸 다시 켬 | `Exited` → `Up` |
| `docker exec -it` | 이미 켜져 있는 것에 들어감 | 그대로 |

 `run`은 매번 새로 만드는 거고 `exec`는 이미 있는 걸 다시 쓰는 것.

```bash
docker run -d -p 8081:80 \
  -v "$(pwd)":/usr/share/nginx/html \
  --name bind-test nginx:alpine

# 브라우저에서 localhost:8081 접속 → 변경 전 화면 캡처
# 호스트에서 index.html 수정
# 브라우저 새로고침 → 변경 후 화면 캡처
```
바인드마운트는 호스트의 특정 폴더를 그대로 연겨래서 파일수정이 실시간 반영되는 것(개발용), 볼륨은 도커가 관리하는 영역에 데이터를 오래 보관하는 것(운영용)

![바인드 마운트 - 변경 전](images/24_bind_before.png)
![바인드 마운트 - 변경 후](images/25_bind_after.png)

컴퓨터에서 파일을 고치니까 바로 화면이 바뀜. 매번 빌드하지 않고 계속 고쳐가면서 볼 때 쓰는 방식?.

---

### 4.3 Git이랑 GitHub 연결해서 이 문서와 캡처들을 올리기

```bash
git --version
git config --global user.name "***"
git config --global user.email "***@***"
git config --global init.defaultBranch main
```

![Git 버전](images/09_git_config.png)

```bash
git init
git add .
git commit -m "첫 번째 커밋: 개발환경 구축 과제"
```

![Git 초기화](images/10_git_init.png)
![README 작성](images/11_readme-create.png)

```bash
git remote add origin https://github.com/3043382-svg/codyssey_mission1.git
git branch -M main
git push -u origin main
```

![Git Remote](images/18_git_remote.png)
![Git Push](images/19_git_push1.png)
![Git Push 결과](images/19_git_push2.png)

> 여기까지는 처음 진행했던 날 한 것이고, 아래는 나중에 추가로 한 부분입니다.

```bash
git config --global --list
```

이름이랑 이메일은 개인정보라서 캡처에서 가림. GitHub에 올릴 때 쓴 토큰(비밀번호 대신 쓰는 코드)도 이 문서나 스크린샷 어디에도 남기지 않음.

![Git 설정 목록](images/26_git_config_list.png)

**VSCode ↔ GitHub 연동**

![VSCode GitHub 연동](images/27_vscode_github.png)

**Git이랑 GitHub 차이**

Git은 제 컴퓨터 안에서만 돌아가는 프로그램이고, GitHub는 그걸 인터넷에 올려서 다른 사람이랑 공유하는 사이트. `git commit`까지는 컴퓨터 안에만 기록이 남고, `git push`를 해야 GitHub에 실제로 올라감.

---

---

## 5. 검증 방법 및 증거 위치

| 검증 항목 | 검증 명령 | 확인 내용 | 증거 |
|---|---|---|---|
| 터미널 조작 | `pwd` / `ls -la` | 위치·숨김 파일 포함 목록 | [01](images/01_pwd_ls.png) |
| 파일 조작 | `cp` / `mv` / `rm` + `ls -la` | 각 단계 목록 변화 | [08](images/08_file_operations.png) |
| 파일 권한 | `chmod 755` 전후 `ls -l` | `-rw-r--r--` → `-rwxr-xr-x` | [08](images/08_file_operations.png) |
| 디렉토리 권한 | `chmod 700` 전후 `ls -ld` | 권한 문자열 변화 | [20](images/20_chmod_dir.png) |
| Docker 설치 | `docker version` | Client·Server 동시 출력 | [04](images/04_docker_version.png) |
| Docker 데몬 | `docker info` | 데몬 응답 및 설정 | [21](images/21_docker_info.png) |
| 운영 명령 | `images`/`ps -a`/`logs`/`stats` | 목록·상태·로그·리소스 | [22-1](images/22_docker_ops1.png), [22-2](images/22_docker_ops2.png), [22-3](images/22_docker_ops3.png) |
| 컨테이너 실행 | `docker run hello-world` | 실행 성공 메시지 | [05](images/05_hello_world.png) |
| 컨테이너 진입 | `docker run -it ubuntu bash` | 내부 `ls`·`echo` 결과 | [23](images/23_ubuntu_interactive.png) |
| 이미지 빌드 | `docker build` | 빌드 성공 및 이미지 생성 | [14](images/14_docker_build.png) |
| 포트 매핑 | `curl localhost:8080` + 브라우저 | HTML 응답 및 주소창 | [15](images/15_browser.png), [16](images/16_browser.png) |
| 바인드 마운트 | 호스트 파일 수정 + 새로고침 | 재빌드 없이 반영 | [24](images/24_bind_before.png), [25](images/25_bind_after.png) |
| 볼륨 영속성 | `rm -f` 후 신규 컨테이너 조회 | 파일 유지 확인 | [17](images/17_volume_persist.png) |
| Git 설정 | `git config --global --list` | 사용자·기본 브랜치 설정 | [26](images/26_git_config_list.png) |
| GitHub 연동 | `git push -u origin main` | 원격 반영 성공 | [18](images/18_git_remote.png), [19](images/19_git_push1.png) |
| VSCode 연동 | VSCode 소스 제어 패널 | GitHub 로그인·저장소 연결 | [27](images/27_vscode_github.png) |

---

## 6. 트러블슈팅

### #1 zsh에서 느낌표 넣었더니 오류
 
index.html 파일을 만들면서 `echo "<h1>Hello from My Custom Nginx!</h1>" > index.html`를 쳤는데 `zsh: event not found: </h1>`라는 오류. zsh라는 셸에서는 `!`를 예전 명령어를 다시 불러오는 특수 기호, 큰따옴표 안에서는 이게 그대로 작동해서 생긴 문제. `echo '<h1>Hello from My Custom Nginx!</h1>' > index.html`처럼 작은따옴표로 바꿔서 치니까 바로 해결

### #2 GitHub에 올릴 때 비밀번호가 안 먹힘

`git push`를 했더니 아이디/비밀번호를 물어봤는데, 평소 쓰던 비밀번호를 넣었더니 인증이 거부. GitHub가 비밀번호로 로그인하는 방식을 이제 안 쓴다고 하고, 대신 토큰이라는 걸 발급받아서 써야 한다고 되어서, 코디세이에 넣었던 토큰을 넣어서 해결.

### #3 OrbStack이 꺼져 있어서 Docker가 안 켜짐

`docker info`를 쳤는데 앞부분(Client)은 잘 나오다가, 뒷부분(Server)에서 `연결할 수 없다`는 식의 오류. OrbStack을 다시 켜고 좀 기다렸다가 `docker info`를 다시 치니까 이번엔 제대로 정보가 나옴. Docker 명령어를 치는 프로그램(Client)이랑 실제로 컨테이너를 돌리는 프로그램(Server)이 따로 있고, 둘 다 켜져 있어야 함

### #4 경로 오타 때문에 한참 헤맴

바인드 마운트 명령을 칠 때 컨테이너 쪽 경로를 `/usr/shaare/nginx/html`이라고 잘못 쳤습니다(share인데 shaare로). 명령어 자체는 오류 없이 실행돼서 한동안 몰랐는데, 브라우저로 들어가보니 제가 만든 페이지가 아니라 nginx 기본 화면. 오타를 고치고 다시 실행했더니 이번엔 "이미 그 이름의 컨테이너가 있다"는 오류. 아까 오타 낸 컨테이너를 안 지우고 그대로 뒀던 것. `docker rm -f`로 지우고, 철자 다시 확인하고 재실행하니까 그제서야 제대로 반영. 
