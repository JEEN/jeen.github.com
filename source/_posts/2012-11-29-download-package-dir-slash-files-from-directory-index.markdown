---
layout: post
title: "Download package dir/files from Directory Index"
date: 2012-11-29 20:10
comments: true
categories: [ linux ]
---

 최근에 모 오픈소스의 패키지 리포지트리가 조만간 닫힐 것이라는 뉴스가 있어서… 만일을 위해 거기 있는 패키지 파일들을 일단 긁어오기로 했습니다. -_-;

 그래서 크롤러를 하나 만들까 하다가… 이런 류의 작업은 뭔가 만들어 놓은 툴이 있을 것이라는 생각에 이리저리 뒤져보고 찾아봤지만 마땅한 키워드를 얻지 못했습니다.
 
 결국 지인들에게 수소문해본 결과 아래와 같은 커맨드를 통해서 Directory Index 구조를 취하는 페이지에서 파일들을 몽땅 긁어올 수 있다는 걸을 알게되었습니다.
 
``` bash
$ wget -e robots=off -k -r --no-parent --wait=5 http://download.virtualbox.org/virtualbox/debian/
```

 `index.html` 등의 불필요한 파일들도 같이 다운로드 되기도 하지만… 일단 다 받고나서…
 
``` bash
$ find . | grep index.html | xargs rm 
```

으로 삭제…

그리하여 조만간 끊어질지도 모르는 사태에 대비코자 미리 이렇게 준비를 끝냈습니다.

