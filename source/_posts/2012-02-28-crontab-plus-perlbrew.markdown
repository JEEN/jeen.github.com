---
layout: post
title: "crontab + perlbrew"
date: 2012-02-28 21:03
comments: true
categories: [ perl, crontab ] 
---

  이번 글은 성의가 매우 부족하게 트윗의 열거로 끝낼까 합니다.

<blockquote class="twitter-tweet"><p>질문. perlbrew 설정이 crontab에서도 적용되게 하려면 어떻게 적어줘야 할까요..? .bashrc 에서 perlbrew의 설정파일을 부르게 해 둔 상태인데 crontab으로 실행하는 스크립트에는 적용이 안 되어 시스템 펄을 부르네요.</p>&mdash; Raymundo (@gypark) <a href="https://twitter.com/gypark/status/172218904352010240" data-datetime="2012-02-22T07:19:21+00:00">February 22, 2012</a></blockquote>


<blockquote class="twitter-tweet" data-in-reply-to="172218904352010240"><p>@<a href="https://twitter.com/gypark">gypark</a> <a href="https://t.co/Q0yfoquc" title="https://gist.github.com/1882996">gist.github.com/1882996</a> 이런 식으로 하면 됩니다.</p>&mdash; envi (@nving) <a href="https://twitter.com/nving/status/172219600216399872" data-datetime="2012-02-22T07:22:07+00:00">February 22, 2012</a></blockquote>

{% gist 1882996 example %}

<blockquote class="twitter-tweet" data-in-reply-to="172219600216399872"><p>@<a href="https://twitter.com/nving">nving</a> 어... 그러면 다른 버전으로 switch 할 때마다 저 변수값을 바꿔줘야 해서...</p>&mdash; Raymundo (@gypark) <a href="https://twitter.com/gypark/status/172220146138611713" data-datetime="2012-02-22T07:24:17+00:00">February 22, 2012</a></blockquote>

<blockquote class="twitter-tweet" data-in-reply-to="172221017534631936"><p>@<a href="https://twitter.com/nving">nving</a>perlbrew깔린디렉토리/perlbrew/bin/perlbrew exec 스크립트.pl이런 식으로 하니까 되네요~</p>&mdash; Raymundo (@gypark) <a href="https://twitter.com/gypark/status/172224703652564992" data-datetime="2012-02-22T07:42:23+00:00">February 22, 2012</a></blockquote>

<blockquote class="twitter-tweet" data-in-reply-to="172220146138611713"><p>@<a href="https://twitter.com/gypark">gypark</a> @<a href="https://twitter.com/nving">nving</a>님께서 알려주신 방법이 정석(perlbrew와 동일한 방식)이라고 보여집니다. 다만 저라면 환경 변수 설정 없이 명시적으로 실행할 펄 바이너리를 명시하고 실행할 스크립트를 적어줄 것 같습니다.</p>&mdash; keedi (@keedi) <a href="https://twitter.com/keedi/status/172228561166536704" data-datetime="2012-02-22T07:57:43+00:00">February 22, 2012</a></blockquote>

<blockquote class="twitter-tweet"><p>@<a href="https://twitter.com/gypark">gypark</a> @<a href="https://twitter.com/nving">nving</a> 크론에서 돌린다면 사실 어떤 버전이든지 알아서(환경 변수에 따라) 실행되게 하는 것은 위험한 것 같습니다. 더불어 크론으로 돌릴 스크립트라면 환경 변수를 믿지 않도록 작성할 필요도 있을테구요.</p>&mdash; keedi (@keedi) <a href="https://twitter.com/keedi/status/172228854906228737" data-datetime="2012-02-22T07:58:53+00:00">February 22, 2012</a></blockquote>

<blockquote class="twitter-tweet" data-in-reply-to="172227703838220289"><p>@<a href="https://twitter.com/nving">nving</a> @<a href="https://twitter.com/gypark">gypark</a> 그렇게 실행할 경우 지금 현재 자신이 switch 하는 perl 버전에 따라 cronᅟ으로 돌릴 스크립트가 영향을 받지 않나요? 그렇다면 어떤 버전에는 설치된 모듈이 있고 어떤 버전에는 없다면 문제가 생길 것 같아요.</p>&mdash; keedi (@keedi) <a href="https://twitter.com/keedi/status/172229942577336320" data-datetime="2012-02-22T08:03:12+00:00">February 22, 2012</a></blockquote>

<blockquote class="twitter-tweet" data-in-reply-to="172234678059794432"><p>@<a href="https://twitter.com/keedi">keedi</a> @<a href="https://twitter.com/nving">nving</a> RSS만드는 스크립트들을, 5.14에서 테스트했더니 와장창 망가지더라고요. Mechanize쪽이 어느 순간부터 반환값을 디코드해서 반환하는 터라. 그래서 거기에 맞춰 고쳤더니 정작 크론에서는 5.8 상에서 도는 바람에 OTL</p>&mdash; Raymundo (@gypark) <a href="https://twitter.com/gypark/status/172237570988703745" data-datetime="2012-02-22T08:33:31+00:00">February 22, 2012</a></blockquote>


 이상 날로먹는 crontab + perlbrew 끝.
