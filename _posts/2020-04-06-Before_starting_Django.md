---
title: Django를 시작하기 전에 알아야 할 배경지식
date: 2020-04-06 18:30:00 +0900
categories: [Language, Python, Django]
tags: [python django]     # TAG names should always be lowercase
toc: true
---

#### [MVC](https://www.essenceandartifact.com/2012/12/the-essence-of-mvc.html)
초창기 웹 페이지 개발은 모든것이 한 페이지 혹은 모델로 개발되었다.
그러나 기술 스택들이 점차 고도화되고 웹퍼블리싱 등의 분야가 생기면서 이것을 분할 할 필요성이 제기되었고 1979년도에 Trygve Reenskaug에 의해 제안되었다.  
세 가지 롤에 의해서 프로그램을 구현하자는 것인데, Model, View, Control로 분할하여 프로그램을 구현한다.  
* Model은 유저의 중요한 데이터들을 보호하고 숨기는 일을 한다.  
* View는 유저들에게 보여주는 화면을 구성한다.  
* Control은 View와 Model 사이에서 유저와 상호작용하고 처리하는 역할을 한다고 볼 수 있다.    

세가지 역할들의 관계를 잘 설명한 그림은 아래와 같다.
![MVC Roles](/assets/img/20-04-06_mvc_role_diagram.png)  

#### Django vs NodeJS
참고자료: <https://youtu.be/PnhmeFakkXg>  
* Django  
    Django 프레임워크는 CRUD환경에 가장 적합한 프레임워크라고 한다.
    CRUD는 Create, Read, Update, Delete로 구성되어 게시판같은환경에 적합하게 설계되어 있다.
    또한 풀 패키지로 되어 있어서 Django를 파악하면서 필요없는것들을 제거시켜 가면서 구현한다.
    
* NodeJS  
    반면에 NodeJS는 아무것도 없는 평면에 레고같은 형식으로 블럭을 쌓아가면서 제작하는 방식이다.
    Django와 비교하여 상대적으로 더 리얼타임환경에 적합하다고 볼 수 있다.
    
* Spring
    댓글을 보다보니 Java Spring Framework에 관한 글이 있어 추려서 정리해본다.  
    한국에서의 Spring의 입지는 독보적이면서 CRUD기능과 Realtime을 요구하는 빠른 응답의 시스템을 구현하는것 모두가 가능하다.
    또한 REST API서버로도 손색이 없다.
