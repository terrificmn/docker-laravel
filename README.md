# docker-laravel 
기존의 라라벨로 만든 프로젝트를 사용하기 위한 도커 입니다

Docker로 PHP, laravel 프레임워크, db 등을 포함하여   
블로그를 만들기 위해 docker 사용

- nginx
- php
- composer
- node
- mariadb
- phpmyadmin
- artisan

등의 컨테이너로 구성되어 있음

사용하고 있는 라라벨 프로젝트를 복사하거나 깃 클론을 하여 사용을 하되   
프로젝트의 디렉토리를 src 로 변경해야함 (또는 docker-compose.yml에서 php 볼륨을 연결하는 디렉토리명 변경)

<br/>

## 설치
클론 할 위치로 이동 (특정 디렉토리로 이동)  
```
cd ~/Workspace
```

깃 클론을 합니다  
```
git clone https://github.com/terrificmn/docker-laravel.git
```

.env-example 파일을 카피한 후 수정해서 사용해야함   
- 서버 적용시 WEB_PORT 를 80, local에서는 8000 or 다른포트 번호(optional)    
- DB 비번 등 확인 후 build 할 것 (처음 빌드하고 만들어질 때 db등이 생성되므로)   

mysqldata 디렉토리가 없다면 만들어 준다   
mysql 도커 컨테이너와 연결됨   
```
cd ~/Workspace/docker-laravel
mkdir mysqldata
```

<br/>

## .env 파일 카피 및 수정하기 
기존의 .env파일을 수정하는 것에서 .gitignore에 추가시킴 - May31 2022   

먼저 .env-example 파일을 복사 한후 수정해서 사용   
```
cp .env-example .env
``` 

.env파일은 DB관련 패스워드, 아이디 등을 적어주는데
최초 빌드시에 데이터베이스, 유저등이 만들어 주므로 필히 확인하여 변경해주자~

파일을 열어서 자신의 db내용으로 수정
```
vi .env
```

<br/>

## local에서 개발할 때
local에서 개발할 때에는 docker-compose.yml 파일의 npm 의 command를 주석해제하고 사용할 것    
그래야 Laravel Mix가 계속 compile을 해서 tailwindcss가 바로바로 적용이 됨   

```
command: ['run', 'watch'] 
```
단, 서버쪽에서는 더 이상 컴파일해줄 필요가 없기 때문에, 주석처리되어 있음   

로컬에서 작업할 때 해당 라인을 주석을 해제한 후 `docker-compose build`를 해준다  

또는 터미널을 새로 열어서 도커 컨테이너를 실행시키는 방법도 있다
```
docker-compose run --rm npm run watch
```

> ^c (컨트롤+c)를 눌러서 종료하게 되면 더이상 컴파일을 하지않는다.   
백그라운드에서 계속 돌아가야하는데 종료가 되서 그런듯 하다   

로컬에서 작업 하려면 포트번호는 8000이나 다른 무작위 번호로 하는 것을 추천

<br/>

## docker-compose 빌드
이제 build와 up을 차례로 실행
```
cd ~/Workspace/docker-laravel

docker-compose build

docker-compose up
```

<br/>

## 자신의 프로젝트를 다운
도커가 잘 실행되는 것을 확인했으면 컨트롤 C를 눌러서 종료  

[새로운 프로젝트를 만들려면 스킵하고 여기를 클릭](#뉴-프로젝트-만들기)    

자신의 라라벨 프로젝트를 깃허브를 통해 클론해서 받습니다.  
그리고 그 라라벨 디렉토리명을 src로 바꿔준다

자신의 라라벨 프로젝트 내에 .env.example 파일을 .env 로 카피해서 DB등의 비번을 입력해서 저장해준다   
(Dockerfile이 있던 .env 파일과는 다른 파일임에 주의)
```
cd src
cp .env.example .env
```
상위 디렉토리 내의 docker 환경변수를 정의한 .env 파일에서 설정한 내용을   
라라벨 프로젝트 내의 .env파일에도 똑같이 지정한 패스워드, db등을 똑같이 입력해줍니다   
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=database
DB_USERNAME=app
DB_PASSWORD=
```
주의할 점은 *DB_HOST*를 *mysql*로 변경해줘야한다  (docker의 mysql 컨테이너)

migrate 이나 key generate 를 해야할 경우가 생길 수 있습니다.

<br/>

## 컨테이너 개별 작동하기
docker 컨테이너를 활용합니다. npm, artisan, composer 등을 사용하려면
```
docker-compose run --rm artisan key:generate
```
또는
```
docker-compose run --rm artisan migrate
```
또는
```
docker-compose run --rm npm run watch
```

기존의 사용하던 명령 그대로 뒤에 붙여서 사용하면 됩니다. 

<br/>

## 뉴 프로젝트 만들기
~~기존의 프로젝트를 계속 이어서 하다보니, 새 프로젝트를 만들어 적용을 안해봤는데   
대충 아래와 같은 방법으로 하면 될 듯 하다. 물론 테스트는 안해봤기 때문에 확신은 없습니다.~~   

테스트 해봄   
laravel 앱 어플리케이션 자체가 없으므로 처음 프로젝트를 만들려면 docker 컨테이너를 사용해서 만들어줍니다
```
docker-compose run --rm composer create-project laravel/laravel mynewapp
```
그러면 현재 경로에 src/mynewapp 으로 새로 라라벨 페이지를 만들어 준다

경로를 src로 한 단계 올려야 하기 때문에 조금 귀찮은 작업을 해야한다

라라벨 만들어진 프로젝트로 이동한 후에 모든 디렉토리와 파일들을 상위 디렉토리 src로 옮겨준다  
```
cd src/mynewapp
mv ./{.,}* ../
```

> {.,}* 숨겨진 파일까지 다 옮겨준다. * 이 모든 파일이지만 확인해보니 .숨긴파일은 이동이 안됨  
그래서 mv ./* ../ 을 한번 하고 다시 mv ./.* ../ 을 해주는 방법도 있다    

상위 디렉토리 src로 이동해보면 파일들이 잘 옮겨진것을 확인한 후에 덩그러니 남은 mynewapp 만 지워준다
```
cd ..
rm -rf mynewapp
```

그리고 도커 컨테이너 웹서버가 해당 디렉토리를 사용할 수 있게 권한을 줘야함  
```
cd ~/Workspace/docker-laravel-blog
sudo chown -R 33:1000 src/
```

> 권한을 쪼개서 사용, 안그러면 웹페이지가 안뜨고, uid, gid 쪽 권한을 다 주면 코드를 수정할 떄 저장을 또 못한다

<br/>

## docker-compose 실행하기
```
docker-compose up
```

브라우저에 localhost:8000 입력을 해주면 라라벨이 잘 작동하는 것을 볼 수 있다

<br/>

### gitignore
src, mysqldata 디렉토리는 gitignore 처리
