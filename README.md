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

.env 파일의 사용해서 설정 변경 후 사용
- 서버 적용시 WEB_PORT 를 80, local에서는 8000 (optional)    
- DB 비번 등 확인 후 build 할 것 (처음 빌드하고 만들어질 때 db등이 생성되므로)   

mysqldata 디렉토리가 없다면 만들어 준다   
mysql 도커 컨테이너와 연결됨   
```
cd ~/Workspace/docker-laravel
mkdir mysqldata
```

<br/>

## docker-compose 
build와 up을 차례로 실행
```
cd ~/Workspace/docker-laravel

docker-compose build

docker-compose up
```

<br/>

## 자신의 프로젝트를 다운
자신의 라라벨 프로젝트를 깃허브를 통해 클론해서 받습니다.  
그리고 그 라라벨 디렉토리명을 src로 바꿔준다

자신의 프로젝트 내에 .env-example 파일을 .env 로 카피해서 DB등의 비번을 입력해서 저장해준다     
상위 디렉토리 내의 docker 환경변수를 정의한 .env 파일에서 지정한 패스워드, db등을 똑같이 입력해줍니다
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

## 뉴 프로젝트 (테스트 못해봄)
기존의 프로젝트를 클론을 받아서 하다보니, 새 프로젝트를 만들어보지를 않았는데  
대충 아래와 같은 방법으로 하면 될 듯 하다. 물론 테스트는 안해봤기 때문에 확신은 없습니다.   

laravel 앱 어플리케이션 자체가 없으므로 처음 프로젝트를 만들려면 docker 컨테이너를 사용해서 만들어줍니다
```
docker-compose run --rm composer create-project laravel/laravel mynewapp
```

새로 만들어진 프로젝트의 디렉토리명을 바꿔줍니다   
이유는 위에서 말한 것 처럼 라라벨 앱 자체를 docker 컨테이너에 src 이름으로 연결
```
mv mynewapp src
```

<br/>

### gitignore
src, mysqldata 디렉토리는 gitignore 처리