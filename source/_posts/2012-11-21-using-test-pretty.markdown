---
layout: post
title: "Using Test::Pretty"
date: 2012-11-21 22:40
comments: true
categories: [ perl, test ]
---

 사실 `TAP` 의 결과자체는 그렇게 이쁘장하지 않습니다.
  이전에 `NHN Deview 2012` 에서 `Mocha` 로 돌렸을 때 나오는 이쁨직한 테스트 결과를 보고, 
  
> 아, 저러면 왠지 테스트코드 쓰고, 돌릴 만하겠다

 라는 뻘생각이 들었습니다. 하지만 전 테스트코드를 그렇게 용의주도하게 쓰지 않는 편이어서 그냥 다른 세상 이야기라고 넘겼습니다.
 
 그러는 와중에 일본의 Perl IRC 채널에서 `Test::*` 와 `TAP` 등에 대한 여러가지 회의론(?) 같은 이야기가 들려오다가 `BDD` 나 테스트 결과 자체가 하나의 `Spec` 으로 재이용되는 등의 이야기를 듣고는 많이 감화되었죠.
 
 그러는 중에 @tokuhirom 씨가 최근에 내놓은 `Test::Pretty` 와 `Test::Ika` 라는 모듈이 등장했습니다.
 
- [Test::Pretty](http://metacpan.org/module/Test::Pretty)
- [Test::Ika](http://metacpan.org/module/Test::Ika)

`Test::Ika` 의 경우는 Perl5 용 `BDD` Framework 의 자리를 노리는 그런 모듈입니다만.. 현재의 버젼(`0.02`)에서는 생각대로 결과가 나와주지 않기에 조금 아쉬웠습니다.

그게 `describe`, `it` 등의 `BDD` 에서 사용하는 구문을 사용하고 있지만, `Test::Mojo` 처럼 `Test::More` 를 래핑한 모듈에서 사용할 때라든가… 좀 많이 아쉬운 장면들이 아직 있습니다. 뭐 물론 만든 지 얼마 안되기도 하고 그런 의미에서는 전도유망하다고할까요 :-)

`Test::Pretty` 의 경우는 크게 이를 고려하지 않고도 흔히  사용하는 `Test::More` 로  테스트코드를 쓰고, `prove` Plugin 으로 동작합니다. 이름 그대로 결과를 이쁘게 보여준다는 것이죠.

다음처럼 말이죠.

### 일반적인 TAP 형식의 출력

![일반적인 TAP 형식](https://lh5.googleusercontent.com/-trTeNFoXQtU/UKzc4yghokI/AAAAAAAACFY/zaEbENGNHOQ/test-pretty-01.png)

### Test::Pretty 를 이용한 출력

![Test::Pretty 를 이용한 출력](https://lh3.googleusercontent.com/-okmAPP0EmC8/UKzc74KHsYI/AAAAAAAACFg/rthGC2gQn_E/test-pretty-02.png)

그러다가 JS 테스트 프레임워크인 [Mocha](http://visionmedia.github.com/mocha/) 를 들여다 봤는데 여러모로 베껴왔으면 하는 그런 세련됨이 있네요. :-)

