---
layout: post
title: "Date::Holidays::KR Released"
date: 2012-02-07 20:28
comments: true
categories: [perl,cpan]
---

  회사의 경영자 @y0ngbin 께서 `is_bizday` 라는 스크립트를 만들어서 쓰는 것을 보고 호오 했었는데...
  
  <script src="https://gist.github.com/1646007.js?file=is_bizday.pl"></script>
  
  알고보니 내용인즉슨 HTTP Request 를 날리고 그 결과를 파싱하는 일을 하고 있다는 것을 알고, 급히 [Date::Holidays::KR][cpan-date-holidays-kr] 이라는 모듈을 만들기로 했습니다... 랄까 사실은 작년부터 떠밀려온 `Things` 의 이슈 중 하나였습니다.

  사실 일본같은 경우에는 음력이 옛날에 없어져버렸고, 지금은 양력만으로 돌고 있고, 중국은 한국과 마찬가지로 음력을 사용하고 있습니다. 재미삼아서 여러나라의 공휴일을 살펴봤는데, 대개 정해진 날, 또는 몇 월의 몇번째 주의 월요일이라든가 하는 식으로 공휴일이 지정되어 있는 것을 봤습니다.
  캐나다의 경우는 주마다 휴일이 다르더군요. :-)

  아무튼 같은 음력을 사용하는 중국, 그 중국용으로 만든 [Date::Holidays::CN][cpan-date-holidays-cn] 을 참고해서 `Date::Holidays::KR` 을 만들었습니다. 음력도 미묘하게 중국과는 다르다고 하는데, 이 부분은 @aer0 님께서 만드신 [Date::Korean][cpan-date-korean] 모듈을 사용해서 음력/양력 변환을 이용했구요. (설날,추석,석가탄신일의 경우는 음력)

<blockquote class="twitter-tweet"><p>Date-Holidays-KR 0.01 by JEEN - <a href="http://t.co/pHQVHLaJ" title="http://metacpan.org/release/JEEN/Date-Holidays-KR-0.01/">metacpan.org/release/JEEN/D…</a></p>&mdash; CPAN New Modules (@cpan_new) <a href="https://twitter.com/cpan_new/status/166840689223675904" data-datetime="2012-02-07T11:08:14+00:00">February 7, 2012</a></blockquote>

<blockquote class="twitter-tweet"><p>Date-Holidays-KR 0.02 by JEEN - <a href="http://t.co/mgouifly" title="http://metacpan.org/release/JEEN/Date-Holidays-KR-0.02/">metacpan.org/release/JEEN/D…</a></p>&mdash; CPAN New Modules (@cpan_new) <a href="https://twitter.com/cpan_new/status/167066106622771200" data-datetime="2012-02-08T02:03:58+00:00">February 8, 2012</a></blockquote>

  어제 0.01 올리다가 `Makefile.PL` 에 의존모듈을 안정해놨구나 해서 추가해서 다시 올려놨습니다. 

``` diff
diff --git a/Makefile.PL b/Makefile.PL
index 7b27280..fef5219 100644
--- a/Makefile.PL
+++ b/Makefile.PL
@@ -2,7 +2,7 @@ use inc::Module::Install;
 name 'Date-Holidays-KR';
 all_from 'lib/Date/Holidays/KR.pm';

-# requires '';
+requires 'Date::Korean';

 tests 't/*.t';
 author_tests 'xt';
 ```

[cpan-date-holidays-kr]:http://metacpan.org/module/Date::Holidays::KR
[cpan-date-holidays-cn]:http://metacpan.org/module/Date::Holidays::CN
[cpan-date-korean]:http://metacpan.org/module/Date::Korean
