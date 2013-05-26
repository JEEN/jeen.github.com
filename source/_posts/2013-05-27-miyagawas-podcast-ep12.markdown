---
layout: post
title: "miyagawa's podcast ep12 - Korean Translation"
date: 2013-05-27T00:15:00+09:00
comments: true
external-url: 
categories: [ perl ]
---

 자주 챙겨듣는 podcast 중에 개인적으로 존경하는 개발자인 @miyagawa 씨가 운영하는 podcast 가 있습니다.
 
- [Tatsuhiko Miyagawa's Podcast](http://podcast.bulknews.net)

 주제는 개발이나 각종 기기, 이벤트, 언어, 커뮤니티 등등의 IT와 관련된 다양한 이야기들을 다루고 있습니다.
 
 항상 사전녹화하는 것을 수정해서 올리곤 했는데 이번에는 Mixlr 라는 서비스를 이용해서 생방송으로 진행되었습니다.
 
- [Mixlr makes broadcasting live audio simple. Share your sounds live...](http://mixlr.com)

 시간은 미리 공지된 대로 5.26 저녁 10시. 게스트들과의 대화는 스카이프로 이뤄진 것 같습니다. 대화는 1시간 분량입니다.

 아래의 내용은 IRC 채널 #perl-kr 에서 내용을 들으면서 옮겨적은 것이며, 부분적으로 빼먹은 부분이나 급작스런 컨텍스트 전환이 있습니다.
 
### Intro

- jeen__ : 미역 팟캐스트 12회는 라이브라고 해서 이거 듣고 퇴근해야지... 퇴근이랄까..
- skyloader : 미역이 팟캐스트도 하나요?
- skyloader : 주제가 perl?
- jeen__ : ㅇㅇ
- skyloader : 오올
- skyloader : 하지만 일본어겠지..
- jeen__ : ㅇㅇ
- jeen__ : lestratt, tokuhirom 이 게스트
- jeen__ : 처음으로 펄만을 위한 주제인듯 -_-;
- jeen__ : tokuhirom TOEIC 쳤냐고 ㅋㅋ
- jeen__ : 쳤는데 점수 안나왔다고
- skyloader : 올 진사마의 실시간 통역
- jeen__ : tokuhirom 이 아마 YAPC::NA 2013 스피커로 나가니까

	- [YAPC::NA 2013 - June 3-5, Austin, Texas](http://www.yapcna.org/yn2013/talk/4635)

- jeen__ : 의식을 높이기 위해서 TOEIC 공부하면서 시험쳐봤다고
- jeen__ : ㅋㅋㅋ
- jeen__ : 이번에 NA 스피커로 lestratt 이랑 tokuhirom 이 가니까 그래서 그런가
- jeen__ : 호...
- jeen__ : Skype 에 봇이랄까 그런 거 들어가서
- jeen__ : Skype 대화내용을 리다이렉트 시키고 있다고
- jeen__ : 물론 대화내용은 음성이지만
- skyloader : 딱히 perl내용 같지 않..
- jeen__ : 뭐 아직은...
- jeen__ : 사담만 있을 뿐
- jeen__ : Perl, YAPC 얘기
- jeen__ : ...

### Perl 5.18 에 대해서

- jeen__ : Perl 5.18
	- [perl 5.18.0 is now available!](http://www.nntp.perl.org/group/perl.perl5.porters/2013/05/msg201940.html)
- skyloader : 흠
- jeen__ : Jesse 에서 RJBS 로 매니저가 바뀌고 나서
- jeen__ : 매년 5월에 나오게 되었다고
- jeen__ : 하지만 뭔가 매년 서두르고 있는 건 아닌가라는 느낌으로 서둘러 내고 있는 느낌.
- skyloader : 아니 그럼 일년에 한번만 release한다는 것?
- jeen__ : 매년 이틀씩 늦춰지고 있다고…
- jeen__ : 메이저 업데이트는 1년에 한번
- skyloader : 음
- jeen__ : 5.12->5.14->5.16->5.18
- skyloader : 오키
- jeen__ : Hash Randomization
- jeen__ : 덕분에 테스트 개박살 났다
- jeen__ : 미역도 꽤 테스트 실패하는 모듈이 여럿 있었는데
- jeen__ : 5.17.x 시절에 고쳐놔서
- jeen__ : 그래도 몇개는 아직 그대로 라고
- jeen__ : TT2 도 아직 못 고쳤다고
- jeen__ : 5.18 용 보고는 누군가 일괄적으로 RT 에 리포트 해주고 있다고
- jeen__ : (그래서 나한테도 왔구나...)
- jeen__ : JSON::XS 가 가장 큰 문제
- skyloader : Hash randomization이 뭐길래
- jeen__ : 메일링 리스트에 지적되었는데
- jeen__ : 이런 수정은 의미가 없다고 레만이 지적
- jeen__ : 오히려 Perl 문서에 적혀 있는대로 했으니까
- jeen__ : 니들이 고쳐라!
- jeen__ : 라고 했지만...
- jeen__ : 최근에 관련 수정을 업데이트 ㅋㅋㅋ
- jeen__ : 츤데레
- jeen__ : 이걸로 얻을 수 있는 시큐리티 메리트는 거의 없다는 게 레만의 의견
- jeen__ : 실제 Perl 에 대한 해쉬 공격방법은 제대로 보고되지 않아서
- jeen__ : 5.16.2 취약점에서 한번 그런 게 보고 되었지만...
- jeen__ : 보고자의 보고내용으로는 특정값을 넣어서 메모리가 붕붕 먹었던 그런 경우가 있었다고
- jeen__ : HTTP Query Parameters 의 경우는 거의 대개 Hash 에 넣어서 하기 때문에
- jeen__ : 그런 경우에 해쉬관련 공격의 대상이 될 수 있는데
- jeen__ : 하지만 Perl 5 Porters 에 관련 내용이 올라오지 않아서
- jeen__ : 그다지 의논되지 않은 내용이라고
- jeen__ : 5.16 에서 변경된 패치와 5.18 에서 변경된 내용은...
- jeen__ : 5.16에서는 같은 해쉬를 복사하면 ... 블라블라
- skyloader : 제 수준에서는 심오한 내용이라 이해가 안됨 ㅠ
- jeen__ : 레만은 5.5 Perldoc 까지 되집어 올라갔는데
- jeen__ : 스펙대로 했다고
- jeen__ : 그런 내용을...
- jeen__ : perldoc 관련 표현이 애매했다라는 의견도 있고
- jeen__ : 5.8 에서 해쉬를 복사해서
- jeen__ : 몇번이나 돌려봤을 때
- jeen__ : 그 중에 한번은 값의 순서가 바뀌는 경우가 있었다고
- jeen__ : given-when, smart match…
- jeen__ : experimental 로 격이 내려갔다 라는 것은
- jeen__ : 거의 deprecated 라는 상태와 같다고
- jeen__ : given-when, smart match 의 경우는 그다지 와닫는 표현과 퍼포먼스도 그다지 좋지 않았다고
- jeen__ : 너무 어정쩡한 상태로 5.10 에 넣어버려서
- jeen__ : 그게 계속 문제가 되고 해서 5.18 에서 experimental 로
- jeen__ : subroutine signature ?
- jeen__ : 5.20 대상으로 subroutine signature 를...
- jeen__ : sub asda($...) { }
- jeen__ : 아직 스펙이 정의되지는 않았다고
- skyloader : prototype?
- jeen__ : MOP 도 5.20 릴리즈 대상으로 ...
- jeen__ : 하지만 아직... -_-;
- jeen__ : XS 방방하지 않아도 적절한 퍼포먼스가 나왔으면..
- jeen__ : 5만줄 정도의 코드가 삭제 되었는데
- jeen__ : CPANPLUS + 거기에 의존되었던 코드들
- jeen__ : (5.19에서)
- jeen__ : Module::Build, CGI 를 5.20 에서 deprecated 로 만들려고
- jeen__ : 하고 있다고
- jeen__ : 옛 버젼에서 빌드시에 문제가 ..
- jeen__ : 어차피 Module::Build 가 없어져도 CPAN 클라이언트가 알아서 감지해서 잡아주기 때문에 별 문제가 없을 거라고
- jeen__ : CGI.pm 의 경우는 ....
- jeen__ : 표준모듈로 해 줄 필요는 없지 않냐 라고...
- jeen__ : CGI.pm 용 보안패치도 여러번 나오기도 해서
- jeen__ : CGI 를 CPAN 에서 넣을려고 하면
- jeen__ : FCGI 를 인스톨하려고 하고
- jeen__ : 그게 XS 의존이라서 C컴파일 환경이 없는 곳에서는 대개 실패
- jeen__ : 그래서 CGI 에서 FCGI 의존을 없애려고
- jeen__ : 지금 CGI 가 FCGI 의 경우까지 고려한 코드가 들어가 있어서
jaker has joined (~jaker@36.39.50.37)
- jeen__ : tokuhirom 은 5.18.1 까지 기다릴 거라고
- jeen__ : 아직 상용서버에 넣어 돌리기는 괜히 걱정된다나...
- jeen__ : 특별히 써보고 싶다라는 기능도 없고 해서
- jeen__ : 5.16.3 이 가장 좋은 선택지가 아닌가 하고
- jeen__ : 10-20% 정도 퍼포먼스 향상은 있다고 하는데
- jeen__ : 어떤 경유로 퍼포먼스 향상이 있었는지 그걸 몰라서
- jeen__ : taint support 를 없애는 걸로 퍼포먼스가 올라가는 것은 아닌가 하고 생각

### plenv

- jeen__ : 미역이 perlbrew -> plenv 로 바꾸
- jeen__ : plenv 의 차이점은?

	- [plenv - perl binary manager - metacpan.org](https://metacpan.org/module/plenv)

- jeen__ : perlbrew 는 환경변수 PATH 를 열심히 변경하고 있는데
- jeen__ : plenv 는 중간에 PATH 를 직접건드리지 않고 하고 있다고
- jeen__ : perlbrew 에서 너무 많은 펄 버젼을 지원하기 위햇 어런저런 마법같은 구문들을 집어넣어서
- jeen__ : 그걸 심플하게 하기 위해서 plenv 를 만들었다고
- jeen__ : 테스트를 위해서 perlbrew switch 하지 않고
- jeen__ : 좀 더 간단하게 가능하다고
- jeen__ : plenv 를 production 에서 쓰는 건 필요이상의 오버헤드가 들어가서 스스로도 권장하지 않는다고
- jeen__ : travis ci, dotcloud 도 perlbrew 를 사용하고 있

	- [dotCloud - One home for all your apps](https://www.dotcloud.com)
	- [Travis CI - Free Hosted Continuous Integration Platform for the Open Source Community](https://travis-ci.org)

- jeen__ : .rvmrc 같은 표기처럼 해당 프로젝트 루트에서 버젼표기를 하는 방식으로 하는 게 편리하지 않은가 라든가
- jeen__ : plenv 는 yapc::na 에서 발표할 예정이라고
- jeen__ : perlbrew 쪽이 압도적으로 지명도가 높고, 문서도 별로고
- jeen__ : 그래서 사용할 메리트가 별로 없으니
- jeen__ : 문서에 집중하는 게 좋지 않겠느냐
- jeen__ : plenv 는 이런 부분에서 압도적으로 좋다라는 부분이 없어서
- jeen__ : 뭘 강조해야할지 망설이고 있다

### YAPC::Asia Tokyo 2013

- jeen__ : YAPC::Asia 이야기
	- [YAPC::Asia 2013 会場を紹介します | YAPC::Asia Tokyo 2013](http://yapcasia.org/2013/05/yapcasia-2013-venue.html)
- jaker : 끝났나요?
- jeen__ : 스피커 모집은 아무 문제가 없으면 내일 시작
- jeen__ : 미역 뱅기 티켓 끊어야 되니까 빨리 뭐 해달라고
- jeen__ : 4트랙으로 진행한다고 ㄷㄷㄷ
- jeen__ : 여태껏 3트랙으로 했는데
- jeen__ : 일수는 2일로
- jeen__ : 전야제는 여느때처럼 하고
- jeen__ : JPA 주최로 이번이 5회째
- jeen__ : 흙
- jeen__ : 누군가 해준다면 넘겨주고 싶다고
- jeen__ : 컨퍼런스 운영자 모임에서 먹거리 얘기가 나와서..
- jeen__ : 맥주랑 닭튀김은 있어야 된다고...
- jeen__ : 매년 와인/일본주는 남는데
- jeen__ : 맥주는 추가주문해야된다고
- jeen__ : 모두들 감자튀김이랑 닭튀김이랑 맥주 정도만 있으면 되는 건 아닌가..
- jeen__ : 동공대 스시 가 매우 좋았다고
- jeen__ : 결혼식 피로연 하는 그런 공간이 있어서
- jeen__ : 300명 정도 들어가고
- jeen__ : 컨퍼런스 파티 참가비는 무료
- jeen__ : 컨퍼런스 참가자는 1000명 목표
- luzluna : 실시간번역 감사..
- jeen__ : 작년이 860명이었으니
- luzluna : 치맥은 어느나라를 가든 진리...
- jeen__ : 941 가 말하기를 컨퍼런스를 확대하느냐 포커스를 맞춰서 집중하느냐
- jeen__ : 하지만 아직 마키는 좀 더 확대노선으로 갔으면 한다고
- jeen__ : 지금까지 오지 못한 사람들을 조금 더 오게 하도록
- jeen__ : 미역이 미국의 Railsconf 에서는 1500명 정도 왔다고
- jeen__ : 장소는 OSCON 과 같은 장소
- jeen__ : 실제 사용규모는 절반
- jeen__ : 트랙은 3-4 밖에 없어서
- jeen__ : 제일 작은 방에 들어가도 한 방에
- jeen__ : 200-300 들어가야 되니까
- jeen__ : 니치한 이야기를 하기에는 좀 어려웠다고
- jeen__ : 모두가 재미있어 하는 토크는 별로 재미있지 않다
- jeen__ : 그래서 대상이 20-50명 정도로 하면 굉장히 재미있고 남은 참가자들의 모티베이션도 생긴다고
- jeen__ : YAPC::ASIA 는 제일 큰곳은 450명 들어가는 곳이고, 작은 곳이 60명
- jeen__ : YAPC::ASIA Closed Talk 같은 공간을 준비한다고
- jeen__ : ㅋㅋㅋ
- jeen__ : 뒷얘기 위주
- jeen__ : 예전에 perl 관련 설문을 했는데
- jeen__ : 의외인 부분은 의외로 많은 사람들이 perl6 얘기를 듣고 싶어한다
- jeen__ : 그리고 레거시 시스템을 어떻게 끌고 가느냐
- jeen__ : 그리고 관련 사례를 소개시켜줬으면 한다 라든가
- jeen__ :미국쪽에는 그런 사례소개가 많았는데
- jeen__ : 근데 레거시 관련 얘기는 미국에서는 별로 없어서
- jeen__ : Big Data
- jeen__ : 수치 계산
- jeen__ : 이런 거 좀 해줬으면 한다고
- jeen__ : 4-5년 전에는 Bio 에서 많이 썼는데
- jeen__ : 이런 게 전부 Python 으로 가버린 거 같다고
- jeen__ : USA Today 가 Perl 로 되어 있다고

	- [USA TODAY: Latest World and US News  - USATODAY.com](http://www.usatoday.com)

- jeen__ : 근데 YAPC::NA 라든가 이런 곳에서 사례 소개로 안나온다고
- jeen__ : RubyKaigi 는 이번주 하는데
- jeen__ : 더이상 규모를 확대하지 않고
- jeen__ : 좀 더 참가비를 올리고
- jeen__ : 진짜 오고 싶어하는 사람들을 오게 하겠다는데...
- jeen__ : 전체 크기를 크게 해버리면 아무래도 예산관리가 힘들다고
- jeen__ : 매번 장소 찾는 것도 피똥흘리면서 하고
- jeen__ : 일본 국내 첫 영국식 허브 컨퍼런스 회장이라고...
- jeen__ : YAPC::ASIA 는
- jeen__ : 작년은 앉을 공간이 별로 없었는데
- jeen__ : 올해는 좀 나아질 거 같다고
- jeen__ : LTthon 트랙이라고 작년은 하루종일 LT 만 하는 트랙이 있었는데
- jeen__ : 올해는 좀 힘들겠다고
- skyloader : 앉아 있던 카페가 문닫아서 전 이만 이동..
- jeen__ : 모두들 Perl 에 대한 사랑이 없고, Perl 커뮤니티에 대한 사랑만 있는 것인가...
- jeen__ : 그래서 Perl 커뮤니티에서 딴 얘기만 하는 건 아닌가
- jeen__ : …
- jeen__ : RubyKaigi 에 가는 사람은 정말로 초 Ruby Lover인데
- jeen__ : Perl 컨퍼런스는 Perl 아니고도 다른 거 많이 쓰고 그런 경우가 있어서
- jeen__ : 거리감이 먼 것은 아닌가
skyloader has left IRC (Remote host closed the connection)
- jeen__ : YAPC 말고 좀 더 Core 한 얘기를 하는 컨퍼런스가 좋지 않겠는가 하고
- jeen__ : Hackathon 인가..
- jeen__ : Official 로는 아직 YAPC Hackathon 은 못하고
- jeen__ : 전날에 장소가 정해지거나
- jeen__ : 그런 경우가 많아서...
- jeen__ : QA Hackathon 에서는 50명 정도라면 대개 장소 정도는 빌릴 수 있지 않을까
- jeen__ : NHN 이라든가, Mixi 라든가, DeNA 라든가 … 라고
- jeen__ : Hallaway hackathon
- jeen__ : 이라고 트랙에서 얘기듣지 않고
- jeen__ : 밖에서 서로 떠들고 하는 게 있어서
- jeen__ : 오히려 그런 공간을 마련해주는 것도 좋지 않겠는가 라고
- jeen__ : 운영쪽에서 ~~한다가 아니라
- jeen__ : 다른쪽에서 오히려 ~~한다!
- jeen__ : 라고 정하고 덤벼들었으면 좋겠다고
- jeen__ : 작년에는 NA 에서 토크 스트리밍을 했는데 , 작년 유러피언컵 보면서 NA 토크를 봤다고 (미역)
- jeen__ : 대만의 OSDCTW 장소는  국제회의장이라서
- jeen__ : 자리마다 마이크가 붙어있다고
- jeen__ : 질문 있으면 버튼 누르고 마이크로 말하면 된다고...
- jeen__ : 이번 YAPC::Asia 초청스피커인 pjf 는 생각 외로 커뮤니티에서 별로 유명하지 않다고
- jeen__ : 그래도 발표는 재미있다나
- luzluna : pjf가 누구에요?
- jeen__ : [http://yapcasia.org/2013/](http://yapcasia.org/2013/)
- hongbot : [YAPC::Asia Tokyo 2013] - http://yapcasia.org/2013/
- jeen__ : autodie 만든사람이요
- luzluna : 아..
- jeen__ : 작년 White Camel Award
- jeen__ : https://twitter.com/JEEN_LEE/status/338657107895259136
- jeen__ : ㅋㅋㅋ
- hongbot : JEEN_LEE: このpodcast、同時翻訳してるんですけど… #bulknews http://t.co/BGrK6qtBaX
- jeen_ : 방송 도중에 언급 ㅋㅋㅋㅋ

<blockquote class="twitter-tweet"><p>このpodcast、同時翻訳してるんですけど… <a href="https://twitter.com/search/%23bulknews">#bulknews</a> <a href="http://t.co/BGrK6qtBaX" title="http://twitter.com/JEEN_LEE/status/338657107895259136/photo/1">twitter.com/JEEN_LEE/statu…</a></p>&mdash; JEEN (@JEEN_LEE) <a href="https://twitter.com/JEEN_LEE/status/338657107895259136">May 26, 2013</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
