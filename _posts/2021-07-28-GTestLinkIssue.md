---
title: Google Test Link Error(LNK2019) 해결방법
date: 2021-07-28 17:00:00 +0900
categories: [Language, Cpp]
tags: [unit test, gtest]     # TAG names should always be lowercase
toc: true
---

Visual studio 2017부터 Google test가 IDE에서 정식 지원합니다.
이전 포스트한 [Google test 설치하기](https://jooss287.github.io/posts/Cpp_GTest/)는 VS 이외의 IDE에서 사용하면 되겠습니다.

## Link 2019 Error 발생 배경

C++에서의 Gtest는 일반적으로 같은 Solution안의 별개의 Project에서 작업을 진행합니다. 아래와 같이 프로젝트를 구성하고 Test case를 작성하였습니다.

```shell
Foo solution dir
    ㄴ Foo
        ㄴ Foo.h
        ㄴ Foo.cpp
    ㄴ FooTest
        ㄴ FooTest.cpp
```

```cpp
# Foo project
# Foo.h
class Foo
{
    Foo() {};
    int makeSomething();
}

# Foo.cpp
int Foo::makeSomething()
{
    /* make something */
    return 1;
}
```

위의 Foo class 구조 에서 makeSomething을 테스트하기 위한 GTest 코드는 아래와 같습니다.

```cpp
# FooTest project
# FooTest.cpp
#include "gtest/gtest.h"
#include "../Foo/Foo.h"
TEST(fooTestcase, make_something_test)
{
    Foo foo;
    EXPECT_EQ(foo.makeSomething(), 1);
}
```

Test의 대상이 될 Project가 library형태의 파일이라면 build event를 사용하여 library를 복사하고 Library를 추가하여 사용하면 되겠지만 Library를 추가하지 않은 상황에서 Header file만 링크하여 사용하면 아래의 이미지와 같은 LNK2019를 볼 수 있습니다.

![LNK Error](/assets/img/21-07-28-lnk2019_error.jpg)

---

## 고민 내용

초기에는 단순히 Link의 방식이 잘못되었나 고민했으나 처음 gtest를 쓰던 것이 아니라 이미 몇가지의 Test Suite 를 구성하고 사용하다가 추가적으로 발생한 것들이라 Header 를 추가하는 방법에서는 문제가 없다는것을 전제로 파악하기 시작했습니다.

Google test LNK2019 관련 검색들은 대부분 Library를 추가하라는 방식의 내용이었고 이 방식은 Target Project의 output을 exe와 lib를 둘 다 만들어서 적용시키는 방식이었습니다.  

* [Gtest LNK2019-stackoverflow](https://stackoverflow.com/questions/25332280/google-test-error-lnk2019-unresolved-external-symbol-with-visual-studio-2013)

기존에 작성한 Test Suite들과 비교하여 분석 결과 Header only Controller이고 새로 추가한 Test Suite는 header, cpp 모두 사용하는 방식으로 header는 link되었으나 cpp 내용이 link되지 않아 발생하는 문제였습니다.

일반적으로 header만을 연결하면 잘 동작하던것이라 cpp파일과 header파일을 어떻게 연결하는지에 대해 고민하지 않은 탓에 꽤 오래 헤메었네요.

---

## cpp - header 파일은 어떻게 연동할까

header와 cpp 를 compile하여 각각 생성된 obj을 이용하여 Link하는 방식입니다. 길게 설명하기보다는 링크로 대체해 두겠습니다.  
[Header file compile](https://boycoding.tistory.com/144)

## Obj 파일을 Link하려면?

Project를 통째로 Library 로 만들어 lib를 Link한다면 가장 좋겠지만 현재 exe만을 output으로 사용하고 있어 변경하기엔 공부해야할것이 좀 더 남아 있습니다.
결국 생성된 Obj파일을 하나하나 Link해줘야 한다는 의미가 됩니다. 다행히 Visual studio IDE에서 Library를 링크하듯 Obj를 링크하여도 똑같은 방식으로 작동합니다.

![object file link](/assets/img/21-07-28-obj_linking.jpg)

## Reference

Object Link 관련해서 검색을 하다보니 제가 처한 상황을 똑같이 해결한 사람을 발견했습니다.

* [gtest vs2019 empty project 구글테스트 사용법](https://jhnyang.tistory.com/448)
