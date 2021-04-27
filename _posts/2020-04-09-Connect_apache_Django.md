---
title: Django와 Tomcat 연동하기
date: 2020-04-09 18:30:00 +0900
categories: [Language, Python, Django]
tags: [tomcat, env setting]     # TAG names should always be lowercase
toc: true
---

# Connect_Apache_Django
아파치 톰캣을 정상적으로 설치 한 뒤 Django와 연결하기 위해서 WSGI(Web Server Gateway Interface)를 사용하여야 한다.
WSGI는 파이썬에서 어플리케이션이 웹 서버와 통신하기 위한 인터페이스로 웹 서버와 웹 어플리케이션 중앙에서 미들웨어로써 동작한다.  
```
요청 → 웹서버 → WSGI Server → Django
```
Django는 mod_wsgi를 사용하여 WSGI Server를 구현한다.


## windows+Anaconda+mod_wsgi
mod_wsgi의 윈도우용 버전은 컴파일버전을 더이상 지원하지 않는다.  
비공식으로 [깃허브](https://github.com/GrahamDumpleton/mod_wsgi/blob/develop/win32/README.rst)
에서 다운받아 컴파일하여 사용한다.

컴파일을 위해 [빌드 도구](https://visualstudio.microsoft.com/ko/visual-cpp-build-tools/)
를 다운받아 설치한다. 빌드도구의 설치가 왼료되지 않으면 mod-wsgi의 설치가 불가능하다.

mod-wsgi의 설치를 위해 [홈페이지](https://pypi.org/project/mod-wsgi/)에서 Release파일을 받는다.  
다운받은 압축파일의 압축을 풀고 Anaconda의 가상환경에서 mod_wsgi를 설치한다
압축파일은 ```c:\download```에 있다고 가정한다. 
```
(conda vEnv) C:\download> python setup.py install
```
성공적으로 설치되었으면 ```mod_wsgi-express module-config```를 입력하여 설정파일에 등록할 구문을 확인한다.  
![config](/assets/img/20-04-09_mod_wsgi_config.png)  
LoadFile, LoadModule, WSGIPythonHome부분을 모두 복사해서 ```C:\Apache24\conf\httpd.conf```의 마지막 부분에 아래와 같이 추가한다
```
# Custom server
LoadFile "C:/Users/funta/anaconda3/envs/web_server_env/python37.dll"
LoadModule wsgi_module "C:/Users/funta/anaconda3/envs/web_server_env/lib/site-packages/mod_wsgi-4.7.1-py3.7-win-amd64.egg/mod_wsgi/server/mod_wsgi.cp37-win_amd64.pyd"
WSGIPythonHome "C:\Users\funta\anaconda3\envs\web_server_env"
WSGIScriptAlias \ "C:\Github_Project\API-Server\API_Server\wsgi.py"
WSGIPythonPath "C:\Github_Project\API-Server"

<Directory "C:\Github_Project\API-Server\API_Server">
   <Files wsgi.py>
      Require all granted
   </Files>
</Directory>

Alias /static/ C:\Github_Project\API-Server/static/
<Directory C:\Github_Project\API-Server/static/>
   Require all granted
</Directory>

Alias /media/ C:\Github_Project\API-Server/media/
<Directory C:\Github_Project\API-Server/media/>
   Require all granted
</Directory>
```

또한 Django에서 ```setting.py```와 ```wsgi.py```에서 아래와 같이 추가한다.
```
setting.py

STATIC_URL = '/static/'
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)
STATIC_ROOT = os.path.join(BASE_DIR, 'pubstatic')
```
```
wsgi.py

path = os.path.abspath(__file__+'/../..')
if path not in sys.path:
    sys.path.append(path)

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'API_Server.settings')
sys.path.append('C:/Apache24/htdocs')

application = get_wsgi_application()
```

여기까지 진행 한 뒤 Apache 서버를 재시작하고 ```localhost```에서 동작을 확인한다.
```
httpd -k restart
```

### Reference
[윈도우에서 장고 배포하기](http://orashelter.tistory.com/55)  
[Windows에서 Apache와 Python django앱을 WSGI모듈로 연동](https://gist.github.com/cr3ux53c/ad7fab5b09c2c80239c403c18b043f9b)  
[mod_wsgi 연동](https://opentutorials.org/course/3647/25080)

[아나콘다 wsgi](https://idlecomputer.tistory.com/7)  
[wsgi 이해](https://brownbears.tistory.com/350)
