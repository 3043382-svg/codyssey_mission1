# 내 컴퓨터에 개발자용 '작업실' 꾸미기

Codyssey Mission 1 — 개발 워크스테이션 구축

- 저장소: https://github.com/3043382-svg/codyssey_mission1
- 제출자: qreitor

> 로컬 폴더 이름은 `coddyssey_mission1`인데 저장소 이름은 `codyssey_mission1`입니다. 처음에 폴더를 만들 때 오타를 낸 걸 나중에 알았는데, 이미 그 이름으로 계속 작업해서 그냥 뒀습니다. 스크린샷 프롬프트랑 저장소 이름이 다르게 보이는 건 이 때문입니다.

---

## 1. 프로젝트 개요

저는 개발을 전혀 모르는 상태에서 이 과제를 시작했습니다. 터미널, Docker, Git이 뭔지도 몰랐고, 명령어 하나 칠 때마다 이게 무슨 뜻인지부터 찾아봐야 했습니다.

이 문서는 그 과정을 기록한 겁니다. 크게 세 가지를 했습니다.

- 터미널로 폴더/파일을 만들고 다뤄보기, 권한 바꿔보기
- Docker 설치하고, 컨테이너 실행하고, 제 웹 서버 이미지 만들어보기
- Git이랑 GitHub 연결해서 이 문서와 캡처들을 올리기

정확히 이해했다고는 못 하겠지만, 명령어를 치고 결과가 어떻게 바뀌는지 눈으로 확인하면서 조금씩 감을 잡았습니다.

---

## 2. 실행 환경

| 항목 | 값 | 증거 |
|---|---|---|
| OS | macOS (Apple Silicon, arm64) | [04](images/04_docker_version.png) |
| Shell | zsh | 스크린샷 프롬프트 전체 |
| 컨테이너 런타임 | OrbStack (Docker Context: `orbstack`) | [04](images/04_docker_version.png) |
| Docker Client | 29.4.0 (API 1.54) | [04](images/04_docker_version.png) |
| Docker Engine | 29.4.0 / linux/arm64 | [04](images/04_docker_version.png) |
| Git | 2.54.0 | [09](images/09_git_config.png) |

Docker Desktop이랑 OrbStack 중에 뭘 써야 할지 몰라서 찾아봤는데, 과제 안내 자료에 OrbStack이 같이 언급되어 있어서 그냥 이걸로 정했습니다. 더 가볍다는 얘기도 봤습니다. OrbStack을 켜두면 뒤에서 Docker가 같이 켜지고, 터미널에서는 그냥 `docker`로 시작하는 명령어를 똑같이 쓰면 됩니다.

이 문서에 있는 명령어들은 제 맥북 기준으로 실행한 겁니다. 다른 컴퓨터에서 따라 하시면 8080이나 8081 포트가 이미 쓰이고 있을 수도 있는데, 그럴 땐 다른 숫자로 바꿔서 실행하면 될 것 같습니다.

<!-- 위 버전 숫자는 실제 출력과 대조해 확인하십시오. -->

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

### 4.1 작업 디렉토리 구성

```bash
mkdir coddyssey_mission1
cd coddyssey_mission1
pwd
ls -la
```

`pwd`는 지금 내가 어느 폴더에 있는지 보여주는 명령어라고 해서 위치 확인용으로 썼고, `ls -la`로 숨김 파일까지 다 나오는 목록을 봤습니다.

![작업 폴더 확인](images/01_pwd_ls.png)

```bash
mkdir images
mkdir practice
ls -la
```

![images 폴더 생성](images/02_mkdir_images.png)
![images 폴더 확인](images/03_images_folder.png)

**절대 경로랑 상대 경로**

`pwd`가 보여준 `/Users/parkmacbook/Desktop/coddyssey_mission1`은 절대 경로라고 합니다. 맨 앞에 `/`가 있으면 컴퓨터 전체에서 유일한 위치를 가리키는 거라, 어디서 실행하든 항상 같은 곳을 가리킨다고 이해했습니다.

반대로 `mkdir images`처럼 그냥 `images`만 쓴 건 상대 경로입니다. 지금 서 있는 위치가 기준이라서, 다른 폴더에서 똑같이 치면 다른 곳에 폴더가 생긴다고 합니다.

| 표기 | 종류 | 의미 |
|---|---|---|
| `/Users/parkmacbook/Desktop/coddyssey_mission1` | 절대 | `/`부터 시작하는 전체 경로 |
| `~/Desktop/coddyssey_mission1` | 절대 (홈 확장) | `~`는 제 홈 폴더로 자동으로 바뀜 |
| `images` | 상대 | 지금 위치 바로 밑의 images |
| `./images` | 상대 | `.`은 지금 있는 폴더 |
| `../images` | 상대 | `..`은 한 칸 위 폴더 |
| `practice/test.txt` | 상대 | 지금 위치 밑에 있는 파일 |

이 문서에 쓴 명령어는 대부분 상대 경로로 썼습니다. 제 컴퓨터 이름(`parkmacbook`)이 들어간 절대 경로를 그대로 쓰면 다른 사람 컴퓨터에서는 안 맞을 것 같아서, 되도록 상대 경로 위주로 썼습니다. 4.10에서 `$(pwd)`를 쓴 것도 같은 이유인데, 이건 반드시 저장소 폴더 안에서 실행해야 제대로 됩니다.

### 4.2 빈 파일 생성 및 내용 확인

```bash
touch practice/test.txt
ls -l practice
```

![빈 파일 생성](images/06_touch_test.png)

```bash
echo 'Hello Codyssey' > practice/test.txt
cat practice/test.txt
```

처음엔 큰따옴표로 썼다가 오류가 났습니다. (6번 트러블슈팅 #1에 적어뒀습니다) 작은따옴표로 바꾸니까 됐습니다.

![내용 확인](images/07_echo_cat.png)

### 4.3 파일 조작 — 복사 / 이름변경 / 삭제

```bash
cp practice/test.txt practice/test-copy.txt
ls -la practice

mv practice/test-copy.txt practice/test-renamed.txt
ls -la practice

rm practice/test-renamed.txt
ls -la practice
```

한 줄 칠 때마다 `ls -la`로 확인하면서 진행했습니다. 안 그러면 뭐가 바뀌었는지 헷갈려서요.

![파일 조작](images/08_file_operations.png)

### 4.4 권한 실습

**파일 권한 변경 (전/후 비교)**

```bash
ls -l practice/test.txt
# -rw-r--r--   (644: 소유자 읽기·쓰기, 그룹/기타 읽기)

chmod 755 practice/test.txt
ls -l practice/test.txt
# -rwxr-xr-x   (755: 소유자 읽기·쓰기·실행, 그룹/기타 읽기·실행)
```

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

세 자리 숫자가 순서대로 소유자·그룹·다른 사람 권한이고, 각 자리는 읽기(4) + 쓰기(2) + 실행(1)을 더한 값이라고 배웠습니다.

| 표기 | 소유자 | 그룹 | 기타 | 의미 |
|---|---|---|---|---|
| 644 | rw- (6) | r-- (4) | r-- (4) | 파일 기본값 |
| 755 | rwx (7) | r-x (5) | r-x (5) | 실행파일·폴더 기본값 |
| 700 | rwx (7) | --- (0) | --- (0) | 나만 접근 가능 |

폴더에서는 `x`가 실행이 아니라 그 안으로 들어갈 수 있냐(cd 가능 여부)는 뜻이라고 해서 처음엔 좀 헷갈렸습니다. `r`만 있으면 목록은 보여도 들어가지지는 않는다고 합니다.

### 4.5 Docker 설치 및 데몬 동작 점검

```bash
docker version
```

Client랑 Server가 둘 다 나오면 잘 깔려있고 잘 켜져 있는 거라고 합니다. Context가 `orbstack`이라고 뜨는 걸로 지금 OrbStack을 쓰고 있다는 걸 확인했습니다.

![Docker 버전](images/04_docker_version.png)

```bash
docker info | head -25
```

![Docker info](images/21_docker_info.png)

### 4.6 Docker 기본 운영 명령

```bash
docker images                      # 로컬 이미지 목록
docker ps                          # 실행 중 컨테이너
docker ps -a                       # 종료된 컨테이너 포함 전체
docker logs my-web-server | tail -10   # 컨테이너 로그
docker stats --no-stream           # 리소스 사용량
```

![docker ps](images/15_browser.png)

![Docker 운영 명령 - images/ps](images/22_docker_ops1.png)
![Docker 운영 명령 - logs](images/22_docker_ops2.png)
![Docker 운영 명령 - stats](images/22_docker_ops3.png)

`docker ps`는 지금 켜져 있는 것만, `docker ps -a`는 예전에 켰다가 꺼진 것까지 다 보여줍니다. 컨테이너는 꺼져도 자동으로 없어지지 않고 `Exited`라고 표시된 채로 남아 있어서, 계속 쌓이는 걸 직접 봤습니다.

### 4.7 컨테이너 실행 실습

**hello-world**

```bash
docker run hello-world
```

이 이미지가 제 컴퓨터에 없으니까 처음엔 자동으로 다운로드부터 하고 그다음에 실행됐습니다.

![Hello World](images/05_hello_world.png)

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

직접 해보고 헷갈렸던 걸 표로 정리해봤습니다.

| 명령 | 뭘 하는지 | 컨테이너 상태 |
|---|---|---|
| `docker run -it ... bash` | 새로 만들어서 그 안에 들어감 | 나오면(exit) `Exited` |
| `docker run -d ...` | 뒤에서 계속 실행 | `Up` 상태 유지 |
| `docker start` | 꺼진 걸 다시 켬 | `Exited` → `Up` |
| `docker exec -it` | 이미 켜져 있는 것에 들어감 | 그대로 |

처음엔 `run`을 계속 쳐서 컨테이너가 자꾸 늘어났는데, 알고 보니 `run`은 매번 새로 만드는 거고 `exec`는 이미 있는 걸 다시 쓰는 거였습니다. 이거 모르고 하면 똑같은 이미지의 컨테이너가 계속 생깁니다.

### 4.8 Dockerfile 기반 커스텀 이미지 제작

nginx라는 웹서버가 이미 들어있는 이미지를 그대로 쓰고, 제 파일만 하나 넣는 방식으로 했습니다. (안내 자료에 있던 두 방식 중 이게 더 쉬워 보여서 골랐습니다.)

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

| 줄 | 하는 일 | 넣은 이유 |
|---|---|---|
| `FROM nginx:alpine` | alpine이라는 가벼운 버전의 nginx 사용 | 용량 작고, 웹서버 설치를 처음부터 안 해도 돼서 |
| `LABEL` | 이미지에 이름표 붙이기 | 나중에 이 이미지가 뭔지 알아보려고 |
| `COPY` | 제 index.html을 컨테이너 안으로 복사 | 원래 있던 nginx 화면 대신 제 화면이 뜨게 하려고 |

![Dockerfile](images/12_dockerfile.png)
![index.html](images/13_index_html.png)

**빌드**

```bash
docker build -t my-web:1.0 .
docker images | grep my-web
```

![빌드](images/14_docker_build.png)

### 4.9 포트 매핑 및 접속 검증

```bash
docker run -d -p 8080:80 --name my-web-server my-web:1.0
docker ps
curl http://localhost:8080
```

![실행 및 curl 응답](images/15_browser.png)
![브라우저 접속](images/16_browser.png)

처음에 `-p` 옵션 없이 그냥 실행했더니 브라우저에서 접속이 안 됐습니다. 찾아보니 컨테이너는 제 컴퓨터랑 네트워크가 따로 분리되어 있어서, `-p 8080:80`처럼 포트를 연결해줘야 밖에서 들어갈 수 있다고 합니다. 컨테이너 안쪽 포트(80)는 그대로 두고 바깥쪽 포트만 바꾸면 같은 이미지를 여러 포트로 동시에 띄우는 것도 된다고 하는데, 이번 과제에서는 하나만 해봤습니다.

### 4.10 바인드 마운트 — 변경 반영

```bash
docker run -d -p 8081:80 \
  -v "$(pwd)":/usr/share/nginx/html \
  --name bind-test nginx:alpine

# 브라우저에서 localhost:8081 접속 → 변경 전 화면 캡처
# 호스트에서 index.html 수정
# 브라우저 새로고침 → 변경 후 화면 캡처
```

![바인드 마운트 - 변경 전](images/24_bind_before.png)
![바인드 마운트 - 변경 후](images/25_bind_after.png)

이미지를 다시 만들지 않았는데도 제 컴퓨터에서 파일을 고치니까 바로 화면이 바뀌었습니다. 신기했습니다. 매번 빌드하지 않고 계속 고쳐가면서 볼 때 쓰는 방식인 것 같습니다.

### 4.11 볼륨 — 데이터 영속성

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

컨테이너를 완전히 지운 다음에 새 컨테이너를 하나 더 만들어서 같은 볼륨을 연결해봤는데, 아까 저장한 `hello`라는 내용이 그대로 남아 있었습니다. 컨테이너를 지워도 볼륨 안의 데이터는 안 지워진다는 걸 이렇게 직접 확인했습니다.

![볼륨 영속성](images/17_volume_persist.png)

**바인드 마운트랑 볼륨, 헷갈려서 정리해본 표**

| 구분 | 데이터가 어디 저장되는지 | 언제 쓰는지 |
|---|---|---|
| 바인드 마운트 | 제 컴퓨터의 특정 폴더 | 파일 수정하면서 바로바로 확인할 때 |
| 볼륨 | Docker가 알아서 관리하는 공간 | 데이터를 오래 보관해야 할 때 |

### 4.12 Git 설정 및 GitHub 연동

```bash
git --version
git config --global user.name "***"
git config --global user.email "***@***"
git config --global init.defaultBranch main
git config --global --list
```

이름이랑 이메일은 개인정보라서 캡처에서 가렸습니다. GitHub에 올릴 때 쓴 토큰(비밀번호 대신 쓰는 코드)도 이 문서나 스크린샷 어디에도 남기지 않았습니다.

![Git 버전](images/09_git_config.png)

![Git 설정 목록](images/26_git_config_list.png)

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

**VSCode ↔ GitHub 연동**

![VSCode GitHub 연동](images/27_vscode_github.png)

**Git이랑 GitHub가 다른 거였다는 걸 이번에 처음 알았습니다**

Git은 제 컴퓨터 안에서만 돌아가는 프로그램이고, GitHub는 그걸 인터넷에 올려서 다른 사람이랑 공유하는 사이트라고 합니다. `git commit`까지는 제 컴퓨터 안에만 기록이 남고, `git push`를 해야 GitHub에 실제로 올라간다고 이해했습니다.

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

`echo "Hello!" > test.txt`를 쳤는데 `event not found`라는 오류가 났습니다. 처음엔 뭐가 문제인지 전혀 몰랐습니다. 찾아보니 zsh라는 셸에서는 `!`를 예전 명령어를 다시 불러오는 특수 기호로 쓴다고 하는데, 큰따옴표 안에서는 이게 그대로 작동해서 생긴 문제였습니다. 작은따옴표로 바꿔서 치니까 바로 해결됐습니다. 그 뒤로는 느낌표나 특수문자 들어간 문자열은 다 작은따옴표로 감싸서 썼습니다.

### #2 GitHub에 올릴 때 비밀번호가 안 먹힘

`git push`를 했더니 아이디/비밀번호를 물어봤는데, 평소 쓰던 비밀번호를 넣었더니 인증이 거부됐습니다. 처음엔 제가 비밀번호를 잘못 기억하는 줄 알았습니다. 오류 메시지를 자세히 보니 GitHub가 비밀번호로 로그인하는 방식을 이제 안 쓴다고 하고, 대신 토큰이라는 걸 발급받아서 써야 한다고 되어 있었습니다. GitHub 사이트에서 토큰을 새로 만들어서 비밀번호 자리에 붙여넣으니까 됐습니다.

### #3 OrbStack이 꺼져 있어서 Docker가 안 켜짐

`docker info`를 쳤는데 앞부분(Client)은 잘 나오다가, 뒷부분(Server)에서 `연결할 수 없다`는 식의 오류가 났습니다. 처음엔 뭐가 문제인지 몰라서 그냥 다시 쳐봤는데 똑같았습니다. 화면 위쪽 메뉴바를 보니 OrbStack 아이콘이 꺼져 있는 걸 발견했습니다. OrbStack을 다시 켜고 좀 기다렸다가 `docker info`를 다시 치니까 이번엔 제대로 정보가 나왔습니다. Docker 명령어를 치는 프로그램(Client)이랑 실제로 컨테이너를 돌리는 프로그램(Server)이 따로 있고, 둘 다 켜져 있어야 한다는 걸 이때 처음 알았습니다.

### #4 경로 오타 때문에 한참 헤맴

바인드 마운트 명령을 칠 때 컨테이너 쪽 경로를 `/usr/shaare/nginx/html`이라고 잘못 쳤습니다(share인데 shaare로). 명령어 자체는 오류 없이 실행돼서 한동안 몰랐는데, 브라우저로 들어가보니 제가 만든 페이지가 아니라 nginx 기본 화면이 떠서 그때서야 뭔가 이상하다는 걸 알아챘습니다. 오타를 고치고 다시 실행했더니 이번엔 "이미 그 이름의 컨테이너가 있다"는 오류가 났습니다. 아까 오타 낸 컨테이너를 안 지우고 그대로 뒀던 거였습니다. `docker rm -f`로 지우고, 철자 다시 확인하고 재실행하니까 그제서야 제대로 반영됐습니다. 명령어가 오류 없이 끝났다고 무조건 제대로 된 게 아니라는 걸 배웠습니다.

### #5 ubuntu 이미지 받아오느라 시간이 걸림

볼륨 실습하다가 `docker run ... ubuntu`를 쳤는데 한참 동안 아무 반응이 없었습니다. 멈춘 줄 알고 좀 당황했는데, 자세히 보니 이미지를 다운로드하는 중이라는 문구가 있었습니다. 다운로드가 끝나니까 바로 실행됐습니다. 처음 한 번만 이렇게 오래 걸리고, 그다음부터는 빠르다는 것 같습니다.
