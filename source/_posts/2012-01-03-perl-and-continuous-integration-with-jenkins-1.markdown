---
layout: post
title: "Perl and Continuous Integration with Jenkins - I"
date: 2012-01-03 11:45
comments: true
categories:  [jenkins,ci,perl,java,nhn,test]
---

### Jenkins 를 사용하기 까지

  사실 지난주에 NHN 에서 주최하는 공개개발자 교육에  갔다왔었습니다. 평소 Jenkins 에 대해서 약간 환상 같은 게 있다고 할까, 필요성이 있다고 할까요. 좀 더 프로젝트의 품질이 나아지고 있다는 환상, 그런 걸 보면서 나름 보람을 느끼고 싶다라는 생각도 있었습니다.
  
  아무튼 Jenkins 교육은 Java 위주의 프로젝트에 적용하는 얘기였지만, 다른 언어에서도 충분히 지원된다라는 얘기를 익히들어왔기에 대강 실습을 따라하면서 감을 익히고 회사에 와서 본격적으로 Jenkins 를 만지기 시작했습니다.

### Perl & Jenkins

  Jenkins 교육에서는 Maven 을 위주로 여러가지 옵션(?) 을 추가하는 것으로 간단하게 각종 Report 를 뽑아내기 위한 xml 들을 생성할 수 있었습니다. 아쉽게도 Perl 로는 Maven 만큼 쉽게 그렇게 만들기는 어려웠지요.
  
  일단 Perl 에는 기본적으로 TAP 형식을 따르고 있기 때문에 Java 류의 JUnit 으로 결과를 뽑아낼 필요가 있었습니다. 그래서 TAP 를 JUnit 으로 쉽게 바꿔줄 수 있는 방법이 필요했죠.
  
``` bash
$ prove --formatter --timer TAP::Formatter::JUnit t
```

  위와 같은 형식으로 `t` 디렉토리 아래의 테스트파일들을 테스트하며 결과는 `TAP::Formatter::JUnit` 에 의해서 xml 파일로 나오게 됩니다.
  
``` xml
<testsuites>
  <testsuite failures="0"
             errors="0"
             time="25.6525909900665"
             tests="1"
             name="Catalyst_controller_Admin-Activity_t">
    <testcase time="0"
              name="1 - Request should succeed"></testcase>
    <system-out><![CDATA[ok 1 - Request should succeed
1..1
]]></system-out>
    <system-err></system-err>
    </testsuite>
</testsuites> 
```

### Task::Jenkins

  사실은 Jenkins 에서 Perl 프로젝트를 올릴 때의 주요한 모듈의 묶음은 [Task::Jenkins][cpan-task-jenkins] 모듈로 제공되고 있습니다. Task::Jenkins 에 있는 모듈들은,
  
* [TAP::Formatter::JUnit][cpan-tap-formatter-junit]
* [App::Prove][cpan-app-prove]
* [Devel::Cover][cpan-devel-cover]

  입니다.
  
  `TAP::Formatter::JUnit` 의 경우는 위에서 설명했고, `App::Prove` 는 위의 테스트코드를 실행하는 커맨드인 `prove` 를 사용하기 위한 모듈입니다. `Devel::Cover` 는 Code Coverage 를 확인할 수 있는 모듈이구요.

  일단 Jenkins 에서 Perl 프로젝트를 적용하는 준비는 기본적으로 위의 `Task::Jenkins` 를 설치함으로 어느 정도 끝났다고 볼 수 있습니다.

### Jenkins 설치

  회사내의 서버에 Jenkins 를 설치했습니다. Ubuntu 10.10 Server 버젼이며, Jenkins 의 설치는 그렇게 어렵지 않습니다.

  쉽게 Google 에서 *Jenkins Ubuntu* 로 검색해서 나온 것들 중 첫번째를 골라봤습니다.

  * [Installing Jenkins on Ubuntu][install-jenkins-on-ubuntu]

  `apt` source 추가하고 `aptitude` 로 설치하고, Apache 에서 Proxy 설정해주고, 이에 대한 자세한 이야기는 위의 링크에 자세히 나와 있으니 참고하시길 바랍니다.

  NHN 공개개발자 교육에서는 보안상의 문제로 Tomcat 위에다가 얹어놓기를 권하셨지만, Jenkins 자체의 Standalone Server 로 충분하지 않나 생각됩니다. 뭐 어차피 사내 네트워크 안에서 사용할 것인데... 라며... =3

  아무튼 이렇게 띄워서 실제로 사내에서 사용하고 있는 Jenkins Top Page 는 아래와 같습니다.
  
![Jenkins Top][image-jenkins-top]

  한번에 적으려니 좀 길고 해서, 일단 밑밥깔기부터 하고 다음 글부터는 좀 더 자세한 Jenkins 에서 Perl 프로젝트 적용에 대해서 상세한 캡쳐와 함께 설명하고자 합니다.
  
[image-jenkins-top]:http://lh6.googleusercontent.com/--lGDWehSz5k/TwKGZAtvsUI/AAAAAAAAA9w/tRFttK10-wg/jenkins-top-page.png
[install-jenkins-on-ubuntu]:https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu
[cpan-app-prove]:http://search.cpan.org/perldoc?App::Prove
[cpan-devel-cover]:http://search.cpan.org/perldoc?Devel::Cover  
[cpan-task-jenkins]:http://search.cpan.org/perldoc?Task::Jenkins
[cpan-tap-formatter-junit]:http://search.cpan.org/perldoc?TAP::Formatter::JUnit