---
layout: post
title: ".proverc for less typing"
date: 2012-11-26 21:44
comments: true
categories: [ perl, test ]
---

- [http://jeen.github.com/blog/2012/11/21/using-test-pretty/]()

그러고보니 앞전에 쓴 글에서
 
``` bash
$ prove -Ilib -Pretty -v -lr 
```

요렇게 길게 쓰고 있었는데, 문서도 제대로 안보고 이래저래 옵션들을 갖다붙이다보니 돌이켜보면 참 부끄럽기 그지 없습니다.

- [https://metacpan.org/module/prove#OPTIONS]()

위의 prove 문서를 참고로해서 제가 무엇을 잘못했는지 다시 한번 돌이켜봤습니다.

### -Ilib 과 -l 은 같다.

 그러니까 위에서는 `-Ilib` 과 `-lr` 로 같이 쓰고 있었는데 -_-;;
 
```
-l,  --lib             Add 'lib' to the path for your tests (-Ilib).
```

`-l` 로 단순히 줄여서 표현할 수 있습니다. 해당 옵션이 무엇인지도 모르고 그냥 붙여쓰고 있었다니...

### 타이핑횟수를 줄인다.

그러니까 매번 테스트할 때마다

```
$ prove -Pretty -lvr
```

이렇게 쓰기 참 번거롭지요.

그러고보니 해당 프로젝트 루트아래에 `.proverc` 를 만들어서 사용하는 방법이 있었습니다.

```
-l
-v
-r
-Pretty
```

이렇게 해두면 이제 앞으로는

```
$ prove
```

만으로 위의 옵션을 활성화한 채로 테스트를 동작시킬 수 있습니다.

### 결론

똑같은 일을 3번, 4번하다 보면 가끔 내가 뭐하는 짓인가 하는 생각이 들 때가 있는데… 좀 더 빨리 깨달았으면 좋았을 것을 이라는 생각을 그때마다 하게 됩니다.
