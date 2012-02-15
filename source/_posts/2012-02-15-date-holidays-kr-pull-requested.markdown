---
layout: post
title: "Date::Holidays::KR Pull Requested"
date: 2012-02-15 23:48
comments: true
categories: [cpan,keedi]
---

* [Date::Holidays::KR Released][post-date-holidays-kr-released]

* [CPAN Module Update][post-cpan-module-update]


    지난번 야매로 간만에 CPAN 에 올린 [Date::Holidays::KR][cpan-date-holidays-kr] 에 버그가 발견되었습니다. 음력을 만만하게 본 탓이 컸습니다.
  
  거기에 더해서 `holidays()` 함수도 동작을 안하는 것을 모르고 올려버렸었네요.
  
  이런 상황에 있어서 @keedi 님께서 Pull Request 를 보내주셨습니다.

  
* [https://github.com/JEEN/p5-Date-Holidays-KR/pull/1][date-holidays-kr-pull-1]


  아주 풍성한 Pull Request 입니다. 거기에 번듯한 테스트코드까지...

  모듈의 성격 탓인지, Pull Request 를 보내시면서

> keedi : 휴일이 너무 적어요 ㅡㅜ

> keedi : 모듈 보면서 더 느끼는거지만 휴일이 겨우 저거뿐 ;ㅁ;

  라는 현 세태에 대한 푸념을 ... ㅡㅜ

<blockquote class="twitter-tweet"><p>Date-Holidays-KR 0.04 by JEEN - <a href="http://t.co/9Jcv6nws" title="http://metacpan.org/release/JEEN/Date-Holidays-KR-0.04/">metacpan.org/release/JEEN/D…</a></p>&mdash; CPAN New Modules (@cpan_new) <a href="https://twitter.com/cpan_new/status/169756771706089472" data-datetime="2012-02-15T12:15:42+00:00">February 15, 2012</a></blockquote>

 재빨리 Merge 해서 다시 CPAN 에 올렸습니다.  앞으로는 좀 더 신중하게 CPAN 에 올려야 되겠구나 하고 결심을 해봅니다.
  
  @keedi++
 
[post-cpan-module-update]:http://jeen.github.com/blog/2012/02/09/cpan-module-update/
[post-date-holidays-kr-released]:http://jeen.github.com/blog/2012/02/07/date-holidays-kr-0-dot-01-released/
[cpan-date-holidays-kr]:http://metacpan.org/module/Date::Holidays::KR
[date-holidays-kr-pull-1]:https://github.com/JEEN/p5-Date-Holidays-KR/pull/1
