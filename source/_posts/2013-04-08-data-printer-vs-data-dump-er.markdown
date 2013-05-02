---
layout: post
title: "Data::Printer vs Data::Dump(er)"
date: 2013-04-08 12:59
comments: true
categories: [ perl ]
---

 여태껏 많은 Perl 코드에서 습관적으로 `Data::Dumper` 를 사용해왔습니다.
 
> 안되면 찍어봐라
 
 라는 가장 기본적인 디버깅 방법론을 `Data::Dumper` 와 함께 해왔었죠. 물론 코어모듈이기때문에 별다른 모듈 설치없이 그냥 사용할 수 있는 점이 가장 큰 매력이었습니다.
 
 단순한 데이터구조에서는 빛을 발하지만, 이런저런 데이터들이 서로 어우러지고 구겨지고 하는 구조에 한글과 같은 멀티바이트문자가 들어갔을 때는 사정없이 깨져버리는 문제가 있습니다. 물론 회피책도 있기는 하지만 매번 그러기도 쉽지가 않죠. 기존의 많은 `Data::Printer` 에도 언급되었다시피, `DBIx::Class` 오브젝트를 찍어보는 것에 매우 효과적입니다. `DateTime` 도 마찬가지 입니다. 이는 `DBIx::Class` 나 `DateTime` 을 `Data::Dumper` 로 찍어본 사람이라면 누구나 느껴봤을 법한 것이죠.
 
 아무튼 그래서 `Data::Printer` 를 사용하기 시작했습니다. 현재 회사에서는 이전과는 달리 웹개발에 메인으로 Perl 을 사용하지 않고 주로 여러가지 배치작업이나 스크래핑 작업 용도로 사용하고 있어서 주로 사용하는 모듈을 그냥 처음부터 집어넣고 시작하고 있습니다.
 
![https://dl.dropboxusercontent.com/u/262117/blog-assets/screenshot-201304112.png](https://dl.dropboxusercontent.com/u/262117/blog-assets/screenshot-201304112.png)

 그리고 결정적으로 키와 값의 색 구분이라든가, 배열의 인덱스 번호를 좌르륵 이라든가, 한글이 안깨진다든가…
 
 단점은 없는 게 아닙니다. 그러니까 좀 규모가 있는 데이터를 `Data::Printer` 로 덤프하면 그 나름대로 치장하는 시간이 좌르르륵 올라갑니다. 근데 뭐 그런 규모있는 데이터를 `Data::Printer` 로 뽑는 것 자체가 이상한 일이라 -_-;;
 
 아 그리고 `Data::Printer` 의 `p` 함수는 기본적으로 인자를 기본형만 받을 수 있습니다.

```
p [{ a => 1 }]
```

이라고 했을 때, 

```
Type of arg 1 to Data::Printer::p must be one of [@$%&]
```

라는 에러메시지가 나옵니다.

 아 그리고 `Data::Printer` 의 출력은 기본적으로 STDERR 로 나옵니다. 물론 뭐 하고 싶다면야 STDOUT 으로 지정할 수 있습니다.
 
- [https://metacpan.org/module/Data::Printer#Changing-output-targets](https://metacpan.org/module/Data::Printer#Changing-output-targets)

 `Data::Printer` 와 함께 깔끔한 디버깅을...