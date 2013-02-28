---
layout: post
title: "cpanm 1.6 is here for you"
date: 2013-02-28 09:34
comments: true
categories: [ perl, cpanm ]
---

 엊그제 cpanm 1.6 이 릴리즈되었습니다. 그동안 개발버젼으로 1.6에 포함될 기능들이 일부 소개되어 왔지만, 아무튼 뭐 @miyagawa 씨가 친히 Screencast 까지 준비해서 소개할 정도로 많은 기능들이 추가되었습니다.
 
 <iframe width="560" height="315" src="http://www.youtube.com/embed/6Vglyf7X2S8" frameborder="0" allowfullscreen></iframe>
 
버젼지정 설치
---------
 
```
$ cpanm MIYAGAWA/Plack-1.0015.tar.gz
```

위처럼 번거로운 버젼지정 설치방식이

```
$ cpanm Plack@1.0015
```

이렇게 간단해졌습니다.

예전에 `Mail::Sender` 의 마이너버젼에 따라서 인코딩지정이 좀 엉망이 되어서 우회코드를 쓸 까 하다가

그냥 증상이 나타나기 이전버젼으로 땜빵으로 버티던 기억이 있습니다. (물론 해당 버젼의 동작이 이상해서 뭐 금방 업데이트 되었었지만…)

개발버젼 설치
----------

```
$ cpanm --dev Plack
```

`--dev` 옵션을 이용하여 개발버젼의 모듈을 설치할 수 있습니다.

저같은 경우는 요즘 거의 개발버젼을 보고 사용하는 경우가 없어서 쓸 일은 없을 것 같습니다.

  - 버젼범위 지정

 1.0000 이상 2.0000 미만의 Plack 모듈 설치
 
```
$ cpanm Plack~">= 1.0000, < 2.0000"
```

 1.0000 버젼의 Plack 설치 (`cpanm Plack@1.0000` 과 동일합니다.)

```
$ cpanm Plack~"== 1.0000"
```

 1.0000 이상이지만 1.0016 버젼은 제외한 가장 최신의 Plack 모듈 설치
 
```
$ cpanm Plack~">= 1.0000, != 1.0016"
```

사실 `Makefile.PL` 에 의존모듈들을 넣었을 때 위와 같은 표기가 필요할 경우가 발생할 수 있겠습니다만, 대개의 경우는 특정 버젼만 지정해서 사용하는 경우가 많았기 때문에 어떨런지 잘 모르겠습니다. 

Git Repository 에서 받아서 설치
---------------------------
  
 몇몇 CPAN Author 들을 보면, CPAN 에 올릴 만큼 정비되지 않았다는 이유로 Github 에만 공개해놓는 경우를 많이 볼 수 있습니다. 몇번 그런 경우가 있었고, 일부러 다른 툴을 사용해서 Github 에서 CPAN Module 을 설치했던 기억도 있습니다. 이제부터 cpanm 이 이런 케이스를 지원해주니 다행이군요.

 기본 브랜치를 설치
  
```
$ cpanm git://github.com/JEEN/p5-WebService-Aladdin.git
```

 devel 브랜치를 설치

```
$ cpanm git://github.com/JEEN/p5-WebService-Aladdin.git@devel
```

 특정 커밋내용이 적용된 부분을 설치

```
$ cpanm git://github.com/JEEN/p5-WebService-Aladdin.git@730fbd0a80
```


결론
---

 perlbrew 에서 `perlbrew install-cpanm` 으로 설치된 cpanm 을 사용하고 있었는데,
 
```
$ cpanm --self-upgrade
```

 로는 perlbrew 를 통해서 설치된 cpanm 의 버젼이 바뀌지 않는군요. 그럴 경우는, 
 
```
$ perlbrew install-cpanm
```

 을 통해서 기존의 cpanm 을 덮어써서 설치할 수 있습니다.
