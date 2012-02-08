---
layout: post
title: "CPAN Module Update"
date: 2012-02-09 01:06
comments: true
categories: [perl,cpan]
---

<blockquote class="twitter-tweet" lang="ko"><p>Acme-CPANAuthors-Korean 0.08 by JEEN - <a href="http://t.co/CjlRnKEO" title="http://metacpan.org/release/JEEN/Acme-CPANAuthors-Korean-0.08/">metacpan.org/release/JEEN/A…</a></p>&mdash; CPAN New Modules (@cpan_new) <a href="https://twitter.com/cpan_new/status/167271503061200896" data-datetime="2012-02-08T15:40:08+00:00">2월 8, 2012</a></blockquote>

<blockquote class="twitter-tweet" lang="ko"><p>Lingua-KO-TypoCorrector 0.03 by JEEN - <a href="http://t.co/b1Lp98PW" title="http://metacpan.org/release/JEEN/Lingua-KO-TypoCorrector-0.03/">metacpan.org/release/JEEN/L…</a></p>&mdash; CPAN New Modules (@cpan_new) <a href="https://twitter.com/cpan_new/status/167273895550922753" data-datetime="2012-02-08T15:49:39+00:00">2월 8, 2012</a></blockquote>

<blockquote class="twitter-tweet" lang="ko"><p>Date-Holidays-KR 0.03 by JEEN - <a href="http://t.co/qAXrPO6f" title="http://metacpan.org/release/JEEN/Date-Holidays-KR-0.03/">metacpan.org/release/JEEN/D…</a></p>&mdash; CPAN New Modules (@cpan_new) <a href="https://twitter.com/cpan_new/status/167275532466143232" data-datetime="2012-02-08T15:56:09+00:00">2월 8, 2012</a></blockquote>

<blockquote class="twitter-tweet" lang="ko"><p>WebService-Aladdin 0.0706 by JEEN - <a href="http://t.co/lyGpgk6W" title="http://metacpan.org/release/JEEN/WebService-Aladdin-0.0706/">metacpan.org/release/JEEN/W…</a></p>&mdash; CPAN New Modules (@cpan_new) <a href="https://twitter.com/cpan_new/status/167276286962704385" data-datetime="2012-02-08T15:59:09+00:00">2월 8, 2012</a></blockquote>

<blockquote class="twitter-tweet" lang="ko"><p>WWW-Shorten-Durl 0.05 by JEEN - <a href="http://t.co/mtnX4oEn" title="http://metacpan.org/release/JEEN/WWW-Shorten-Durl-0.05/">metacpan.org/release/JEEN/W…</a></p>&mdash; CPAN New Modules (@cpan_new) <a href="https://twitter.com/cpan_new/status/167277296259055616" data-datetime="2012-02-08T16:03:09+00:00">2월 8, 2012</a></blockquote>

  이전 글에서 언급했듯, 제가 만든 CPAN Module 들의 Meta 정보중에 repository 가 누락된 것도 있었고, 이 중 몇몇은 Github Project 이름까지 p5-* 형식으로 바꿔놓아서 전부 일일이 바꿔둘 필요가 있었습니다.

``` perl
use inc::Module::Install;
name 'Date-Holidays-KR'; 
all_from 'lib/Date/Holidays/KR.pm';
requires 'Date::Korean' => 0;

tests 't/*.t';
test_requires 'Test::More';
resources repository => 'git://github.com/JEEN/p5-Date-Holidays-KR.git';
auto_include;
WriteAll;
```

  위는 [Date::Holidays::KR][cpan-date-holidays-kr] 의 `Makefile.PL` 입니다. 몇몇 모듈은 [ExtUtils::MakeMaker][cpan-extutils-makemaker] 를 사용하고 있었는데, 모두 [Module::Install][cpan-module-install] 을 사용하도록 교체하고, repository 를 각각의 github repository 를 지정한 것뿐입니다.

  마음먹은 김에 이슈처리하고 저녁시간을 빌어서 CPAN 에 일괄 업로드했습니다. 완전 스팸이네요.

[cpan-date-holidays-kr]:http://metacpan.org/module/Date::Holidays::KR
[cpan-extutils-makemaker]:http://metacpan.org/module/ExtUtils::MakeMaker
[cpan-module-install]:http://metacpan.org/module/Module::Install

