---
layout: post
title: "궁금한 웹 해킹, 8가지만 알아보자! (0)환경 설정"
date: 2018-05-28
tags: [모의해킹]
excerpt: ""
comments: true
---

# 궁금한 웹 해킹, 8가지만 알아보자! (0) - 환경 설정

## Virtual Machine

칼리 리눅스를 가상 머신에 설치하는 방법은 타 블로그의 방법대로 진행하는 편이 빠르다. 본인은 MacOS를 사용하는 유저로써 윈도우에 관련된 조언을 해주기 힘드니 양해를 구한다.  [click here](http://mitny.tistory.com/205)

## XAMPP

XAMPP는 Apache와 MariaDB, PHP, and Perl를 통합한 응용프로그램이다. 우리는 XAMPP를 가지고 개발과 테스트에 필요한 간단한 로컬 웹 서버를 만들 수 있다. 이제 XAMPP을 통해 php 웹서버를 띄워놓고 웹 해킹하기 위한 설치를 해보자. 운영체제는 VM에 설치한 칼리리눅스를 대상으로 진행한다.

먼저 XAMPP를 다운받자. [click here](https://www.apachefriends.org/download.html)

다운로드를 한 뒤, 터미널을 키고 Downloads 디렉토리로 이동한다.

`cd Downloads`

Downloads 디렉토리안의 어떤 파일이 있는지 확인해보자.

`ls`

아래에 "xampp-installer.run" 이름으로 된 파일이 있을 것이다. 설치하려고 하면 permission denied가 일어 날텐데 이럴땐 파일에 접근할 수 있도록 풀어줘야한다.

`chmod +x xampp-installer.run`

그리고 설치하자.

`sudo ./xampp-installer.run`

다음은 설치창이 뜰 것이다.

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/xampp_1.png">

설치한 후, php.ini 파일 수정이 필요하다. (편집기는 vim이나 gedit 아무거나 써도 상관 없다.)

`gedit /opt/lampp/etc/php.ini`

``allow_url_include = Off` -> `allow_url_include =On`

`/opt/lampp/lampp restart `



시작할 때, 터미널 명령어

`/opt/lampp/lampp start`

종료할 때, 터미널 명령어

`/opt/lampp/lampp stop`

재시작할 때, 터미널 명령어

`/opt/lampp/lampp restart`

## DVWA

DVWA는 웹 모의해킹을 학습/연구 하기위해 개발된 환경이다. DVWA를 통해서 우리는 Brute Force, CSRF, Command Injection, 파일업로드, SQL Injection, XSS 등의 공격을 경험해 볼 수 있다.

먼저 DVWA 사용을 위한 데이터베이스 테이블을 만들어보자.

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/dvwa_php_1.png"><br/>

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/dvwa_php_2.png"><br/>

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/dvwa_php_3.png"><br/>



다음으로는 DVWA 홈페이지에서 다운로드한 후, unzip명령어로 압축파일 해제하자. [click here](http://www.dvwa.co.uk/)

`unzip DVWA-master`

웹 루트경로인 /var/www/html로 dvwa로 이름 바꿔서 이동 및 권한설정 해주자.

`mv DVWA-master/ /opt/lampp/htdocs/dvwa`

`cd /opt/lampp/htdocs`

`chmod -R 755 dvwa`

`cd dvwa/config`

`cp config.inc.php.dist config.inc.php`

`gedit config.inc.php`

gedit에디터를 켜서 그림과 같이 DB 세팅을 해준다.

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/dvwa_1.png">

그다음으로는 reCAPTCHA 생성하여 정보 입력해야한다. [click here](https://www.google.com/recaptcha/admin/create)

<br/>

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/dvwa_2.png">

reCAPTCHA를 생성하면 주어지는 **Site Key**와 **Secret key**를 recaptcha_public_key와 recaptcha_private_key에 넣어준다.

그리고 웹l 디렉토리 경로로 이동하여 다음과 같은 권한 부여 명령어를 입력한다.

`chmod 777 /opt/lampp/htdocs/dvwa/hackable/uploads/`

`chmod 777 /opt/lampp/htdocs/dvwa/external/phpids/0.6/lib/IDS/tmp/phpids_log.txt`

`chmod 777 /opt/lampp/htdocs/dvwa/config`

그리고 localhost/dvwa/setup.php에 필요한 작업이 끝났다면 create databases 버튼을 눌러준다.

세팅 끝~ 혹시 세팅에서 오류나거나 모르는 부분이 있다면, 댓글을 달아주시거나 이메일로 질문해주면 좋겠다.

