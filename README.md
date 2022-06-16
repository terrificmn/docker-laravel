# docker-laravel 
기존의 라라벨 프레임워크와 php로 만든 프로젝트를 사용하기 위한 도커 입니다.   
(뉴 프로젝트도 가능합니다.)

스스로 공부하면서 업데이트 하므로 에러가 있을 수 있습니다. 

Docker로 PHP, laravel 프레임워크, db 등을 포함하여   
블로그를 만들기 위해 docker 사용

<br/>

## 현재 버전 정보
대부분은 컨테이너가 latest로 설정이 되어 있어서 docker-build 시 최신 이미지로 다운을 받습니다  
npm컨테이너만 16.13으로 고정   

현재 08Jun 2022 버전 정보
- web (nginx)  
    latest 1.21.6

- php  
    php:fpm (latest)  8.1.6

- composer  
    latest  2.3.5

- npm (node)  
    16.13(node)   (npm버전 8.1.2)

- mysql (mariadb)  
    latest 10.8.3-MariaDB

- phpmyadmin  
    (5.2버전)

- artisan  
    laravel 9.14.1

- certbot (추가)   
    latest  1.27.0

등의 컨테이너로 구성되어 있음


사용하고 있는 라라벨 프로젝트를 복사하거나 깃 클론을 하여 사용을 하되   
프로젝트의 디렉토리를 대개 (app으로 시작) src 로 변경해야함 (또는 docker-compose.yml에서 php 볼륨을 연결하는 디렉토리명 변경)

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

1. .env파일은 DB관련 패스워드, 아이디, DB이름 등을 적어주는데  
최초 docker-compose 빌드시에 데이터베이스, 유저등이 만들어지므로 필히 확인하여 변경해주자~ (그래야 한번에 쉽게 할 수 있음)  
그리고 비번 등은 깃허브에 안 올라가므로 안심 (.gitignore 등록됨)

2. CONF_STATUS 변수는 dev 또는 prod 로 지정해준다   
로컬에서 개발을 할 때에는 dev 로 해준다  
서버에 배포할 경우에는 prod 로 해준다. (**단, https를 사용하는 경우에 한함**)   
80번 포트 http만 사용해서 (도메인없이) ssl 인증이 안 되어있다면 dev로 그대로 사용한다   
(nginx 설정파일이 다르게 되어 있음)

```
CONF_STATUS=dev
  
  #또는

CONF_STATUS=prod
```

파일을 열어서 자신의 것으로 맞게 수정하자
```
vi .env
```

<br/>

## local에서 개발할 때 docker-compose.yml 파일에서
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

> 쿠키 등 데이터가 저장되어 있어서 css가 바로 적용이 안되는지 확인이 필요함  

<br/>

### local에서 개발 or 서버에 배포했을 경우 - web nginx 컨테이너 볼륨 부분 설정
서버에서 배포해서 사용할 때 Https 인증 관련해서   
certbot let's encrypt client 프로그램이 (컨테이너) 추가되서        

.env 파일에서 CONF_STATUS 값에 dev 또는 prod 값으로 바꿔서 사용할 것 -updated 09JUN 2022
서버 배포시는 prod  
로컬 작업시는 dev
로 설정한다

### 단, 서버에서 최초 certbot 컨테이너를 통해서 인증 받는 경우  
서버에서 최초 certbot 컨테이너를 통해서 인증을 받을 때는 http://내도메인.com 이 정상 작동을 해야   
인증 파일을 생성을 할 수 있으므로   

.env 파일에서 CONF_STATUS=dev 저장을 해서 인증서를 발급을 받은 후에~~    
.env 파일에서 CONF_STATUS=prod 다시 바꿔서 사용해야함
docker-compose build 가 필요할 수 있음

[최초 Let's encrypt로 인증 발급 받는 경우에는 여기로 점프해서 참고하세요](#https-인증-받기)

<br/>

## docker-compose 빌드
.env 파일과 docker-compose.yml 파일의 설정이 마무리 되었다면  
이제 build와 up을 차례로 실행해준다
```
cd ~/Workspace/docker-laravel

docker-compose build
```
그리고
```
docker-compose up
```

<br/>

## 자신의 프로젝트를 깃 클론해준다
도커가 잘 실행되는 것을 확인했으면 컨트롤 C를 눌러서 종료를 해주자

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

라라벨 새로운 프로젝트를 만들려면 여기를..   
[새로운 프로젝트를 만들려면 아래는 스킵하고 여기를 클릭해서 점프](#뉴-프로젝트-만들기)    

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

> 서버에서는 .env WEB_PORT=80 그대로 사용해주면 된다

<br/>

## https 인증 받기
최초에는 http에서도 A 도메인과 사이트가 제대로 동작을 해야하기 때문에    
일단은 기존 docker-comopse.yml 파일의 nginx 컨테이너의 volume에서 default_dev.conf 파일과 연결을   
해둔 상태에서 진행한다

먼저 필수 조건은
1. 도메인 네임이 있어야 한다.  
    유로로 구입을 하거나 무료로 도메인 네임이 있어야 한다  
    유료: GoDaddy, namecheap  
    무료: freenom  (.tk 같은 도메인)  

2. 서버의 A DNS A Record 에 서버 public IP로 지정되어 있어야 한다   
    구입한 도메인네임에서 서버의 name server가 지정    
    서버에서는 name server가 내 ip로 향하게 되어 있어야 함    

3. 방화벽 https 443이 열려있어야 함


대충 이 정도 인 듯 하다. 

이제 certbot 컨테이너로 통해서 실행 (최초 인증받을 때)

먼저 위에서 설명한 것 처럼  
**.env 파일에서 CONF_STATUS=dev** 으로 되어 있는지 확인한다.   
최초에 http 80번 포트로 내 도메인주소가 잘 작동해야지 certbot에서 인증서 발급이 완료가 된다

```
docker-compose run --rm certbot certonly --webroot --webroot-path /var/www/certbot/ -d example.com
```
-d 옵션 이하 example.com은 자신의 도메인으로 수정해준다

> 옵션 certonly는 인스톨 없이 인증서를 생성  
--webroot 은 nginx의 웹서버를 이용   
--webroot-path 는 challenges를 위한 certbot의 루트 디렉토리  
-d 옵션은 도메인 이름 (인증을 받은 대상)

email 주소와 동의를 Y해서 입력한다면 발급을 시작한다.

### 발급 성공 후 
발급이 성공적으로 끝났다면    
본격 https에서 잘 작동할 수 있도록 nginx의 환경설정 파일을 다시 지정해줘야한다

이번에는   
**.env 파일의 CONF_STATUS=prod** 다시 바꿔서 사용해야줘야 한다

> docker-compose.yml 에서 nginx 컨테이너의 불륨이 default_prod.conf 파일로 지정된다

```
vi .env
```
```
CONF_STATUS=prod
```
로 저장 후 빠져나온다. (docker-compose build 가 필요할 수 있음)


> 혹시 build 시 만약 mysqldata 쪽에서 권한 때문에 에러가 나면   
sudo chown -R 1000:1000 mysqldata 일시적으로 권한을 바꿔주기   
build되면 다시 소유자 바뀜

최종적으로 docker-compose up을 해서 실행해보면 잘 되는 것 확인   

이제 웹 브라우저에서 자신 도메인을 http:// 상태에서 입력하면  
nginx는 http는 https로 돌려보내고  
443포트로 들어온 요청에서 ssl_certificate, serificate_key를 통해서 인증이 이루어질 수 있게 된다.

<br/>

## 컨테이너 포트번호 맵핑 변경 
phpmysql과 mysql이 도커 컨테이너로 연결 맴핑이 되어서 외부로 노출이 되는데   
일단 phpmyadmin은 웹 브라우저에서 접속을 해서 사용을 했는데   

도커를 up 시켜놓은 상태에서 있다 보면 외부 ip에서 phpmyadmin으로도 꽤 접속을 많이 시도하는게 보인다

포트연결을 조금 변경함

0.0.0.0 과 127.0.0.1 차이가 있는데   
도커에서 -p 옵션으로 run 명령을 실행해서 포트를 연결할 때 (yml파일에서는 ports 부분)   
기본값으로 0.0.0.0로 연결이 되게 되어 있다고 한다. 
즉, 모든 인터페이스를 뜻한다. 외부로부터 접속이 가능하게 된다. 

반면 127.0.0.1 은 서비스가 loopback 주소에 묶이게 되고 외부에서는 접속이 안되게 된다. 

그래서 아무래도 보안상 mysql과 phpmyadmin은 로컬에서만 사용을 하고      
서버에서는 사용을 못하게 포트 연결 방식을 바꿈

docker-compose.yml 파일 중
```yml
mysql:
    # 생략...
    ports:
      - 127.0.0.1:3306:3306

phpmyadmin:
    # 생략..
    ports:
      - 127.0.0.1:8000:80
```

다행히 로컬에서도 잘 되고, database 쿼리에도 이상없이 잘 작동함 - 16JUL 2022

<br/>

### gitignore
src, mysqldata, cerbot 디렉토리는 .env 파일  