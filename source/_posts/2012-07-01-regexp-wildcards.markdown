---
layout: post
title: "Regexp::Wildcards"
date: 2012-07-01 00:46
comments: true
categories: [ perl ]
---

 저같은 평범한 개발자야 어떤 패턴을 확인하고 정규표현식을 쓰면 되는데, 사실 일반 사용자들에게
정규표현식을 어떤 설정의 입력값으로 밀어넣는 것은 일반 사용자에게 배려가 부족하지 않나 하고 생각했었습니다.

- `/path/a/b`
- `/path/a/c`
- `/path/a/d`

`/path/a/` 디렉토리 아래의 b,c,d 파일들을 삭제한다고 합니다. 물론 b,c,d 밖에 없다고 합니다. 이럴때
보통 모두가 대개 와일드카드를 사용하죠.

``` bash
rm -rf /path/a/*
```

라고 말이죠.

 예를들어서 어떤 관리자페이지 아래에서 특정 유저그룹의 접속권한에 대해서 다룬다고 해봅니다. 특정그룹은 고정IP를 통해서 접근해올 수 있고, 또 다른 그룹은 특정 IP대역에서 찾아온다고 합니다. 이런 것들을 복잡한 설정파일을 통해서가 아니라 유저가 관리하기 쉬운 인터페이스를 제공해야 할 필요도 있죠.

 일반적인 입력예는 아래와 같습니다.

- `211.123.123.1-211.123.123.254`
- `211.123.122.5`
- `211.123.121.4, 211.123.121.8`

 적당한 IP대역이거나, 혹은 단일 IP라거나, 혹은 몇몇 패턴을 알 수 없는 복수개의 IP라거나...

- `211.123.126.0/24`

 혹은 네트워크 관리자라면 CIDR 스타일을 사용할지도 모르겠습니다.

- `211.123.128.*`
- `211.123.121.10*`

 아니면 위에서 말한 대로 와일드카드를 사용할 수 있겠죠.

 우선 위의 네개째까지는 `Net::IPAddrRanges` 라는 모듈을 사용하면 편하게 이용할 수 있습니다.

``` perl
use strict;
use warnings;
use Net::IPAddrRanges;

my $ranges = Net::IPAddrRanges->new;
$ranges->add(qw{
    211.123.123.1-211.123.254
    211.123.122.5
    211.123.121.4 211.123.121.8
    211.123.126.0/24
});

$ranges->find('211.123.123.5'); # OK;
```

 이런저런 다양한 입력값들을 받아서 `Net::IPAddrRanges` 모듈에 넘겨주고 `->find` 메소드를 통해서 접속해온 IP(211.123.123.5)가 접속가능한 IP인지를 위처럼 간단하게 확인할 수 있죠.

 와일드카드 사용시에는 이와는 좀 다릅니다.

``` perl
use strict;
use warnings;
use Regexp::Wildcards;

my $rw = Regexp::Wildcards->new( type => 'unix' );

my $user_ip = '211.123.121.108';

if (scalar grep { $user_ip =~ $_ }
           map { $rw->convert($_) } qw{ 211.123.128.* 211.123.121.10* }) {
    print "OK";
}
```

 뭐 사실 이런 코드를 적당한 메소드로 만들어서 사용하고 있습니다. IP제한같은 시나리오에는 사실 Net::IPAddrRanges 를 사용하는 게 좋아보이기는 합니다만... 최근에 `Regexp::Wildcards` 를 사용하는 경우가 있었는데...

 유저의 권한별로 페이지의 접근을 제어하고 싶다라는 것이었죠.

``` yaml
  ROLE1: [ /admin/user/list, /admin/user/*/download ]
  ROLE2: [ /admin/user/* ]
```

 ROLE1 의 경우에는 admin/user 라는 컨트롤러중에 유저 리스트(/admin/user/list) 와 각 유저별로 관련정보를 다운로드를 할 수 있습니다. 그 이외에는 전부 접근을 차단한다는 것이구요.

 ROLE2 의 경우에는 admin/user 의 모든 액션에 접근을 가능하게 합니다.

 이때는 카탈리스트를 사용하고 있다는 전제아래, Admin.pm 이라는 컨드롤러파일의 `auto` 아래 다음과 같은 코드를 추가했습니다.

``` perl
sub auto :Private {
....

    my $rw = Regexp::Wildcards->new( type => 'unix' );

    my $ACL = ...; # 어떻게 샤바샤바해서 유저권한에 따른 접근가능 목록을 뽑아옵니다

    unless (scalar grep { $c->action =~ $_ } map { $rw->convert($_); } @{ $ACL }) {
        $c->flash( warn_messages => [ '접속할 수 없어요.' ] );
        $c->res->redirect($c->uri_for("/admin"));
        return 1;
    }
....
}
```

 네 아무튼 뭐 사실 결론은 굳이 할 수 있다면 간단하게 설정할 수 있는데, 왜 설정파일에까지 정규표현식이 끼어들어야 하느냐 라는 생각아래에서 시작했었지요. 정규표현식으로 난무할 이런저런 상황들을 적당하게 와일드카드로 커버할 수 있다면 더 직관적으로 이해하고 사용할 수 있지 않을까 하는 생각이 듭니다.


