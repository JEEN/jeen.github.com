---
layout: post
title: "Test::More - use 'subtest' instead of {} block"
date: 2012-01-05 20:33
comments: true
categories: [perl,test,jenkins]
---

  여태껏 테스트코드를 써나가면서 그냥 {} 블록안에 일정한 흐름의 테스트 코드를 적곤 했었습니다.

``` perl
use Test::More;
use HTTP::Request::Common;
use Catalyst::Test 'MyApp::Web';

# Access /
{
    my $res = request('/');
    ok($res->is_success, "Request should be succeed");
    ok($res->header('.......'), 'Header xxxxx exist');
    like($res->content, qr|some sentences|, "Found some sentences")
}

# Login
{
    my $req = POST('/login', [ 'username' => 'blahblah', 'password' => 'blahblah' ]);
    my $res = request($req);
    ok($res->is_success, "Request should be suceed");
    ....
}

# some action
{
    ...;
}
```

  일단 뭐 {} 블록으로 각 action 에 대한 접근결과를 테스트하고, `#` 로 주석표시를 하면서 하나둘 하나둘 각 블록에 `$req`, `$res` 를 추가했었습니다. 일단 뭐 {} 블록으로 묶으면서 `$req`, `$res` 따위의 변수의 재정의 등에 관한 경고같은 것이 안나와서 좋고, 제대로 분류가 되는 느낌이기도 했습니다.

### 그냥 `subtest` 를 사용하면 어떨까?

  그러는 도중에 [Test::More 매뉴얼][cpan-test-more]을 읽어보다가 `subtest` 를 사용할 수 있다는 것을 알게 되었습니다.

``` perl
use HTTP::Request::Common;
use Catalyst::Test 'MyApp::Web';
use Test::More;

subtest 'access to /' => sub {
    my $res = request('/');
    ok($res->is_success, "Request should be succeed");
    ok($res->header('.......'), 'Header xxxxx exist');
    like($res->content, qr|some sentences|, "Found some sentences")
};

subtest 'Login' => sub {
    my $req = POST('/login', [ 'username' => 'blahblah', 'password' => 'blahblah' ]);
    my $res = request($req);
    ok($res->is_success, "Request should be suceed");
    ....
};
```

  `subtest` 를 사용하면서 코드는 위처럼 바뀌었습니다. `#` 로 주석처리를 할 필요도 없고, 해당 `subtest` 마다 명확하게 무엇을 하는 지 확실하게 의미있는 `subtest` 의 이름으로 정해줄 수 있었습니다.

  `subtest` 로 테스트를 썼을 경우는 TAP 결과는 다음과 같습니다.

``` bash
$ prove t/myapp.t
ok 1 - access to /
    ok 1 - Request should be succeed
    ok 2 - Header xxxxx exist
    ok 3 - Found some sentences
    1..3
ok 2 - Login
    ok 1 - Request should be suceed
    ...
    1..3
....
1..3
ok
All tests successful.
Files=1, Tests=3, 10 wallclock secs ( 0.07 usr  0.01 sys +  6.16 cusr  0.61 csys =  6.85 CPU)
Result: PASS
```

  전체 테스트 횟수는 `subtest` 안의 테스트 항목이 아니라, `subtest` 의 갯수가 되는 것입니다.
  
  그리고 `subtest` 를 사용하지 않고 {} 블록을 이용해서 flat 하게 정의했을 때는 아래와 같습니다.

``` bash
...
ok 1 - Request should be succeed
ok 2 - Header xxxxx exist
ok 3 - Found some sentences
ok 4 - Request should be suceed
 ...
```

  테스트 항목 1,4 번의 메시지가 같기 때문에 매번 달리 지정해줘야 되는 수고가 발생하죠. 그리고 각각의 테스트 항목 하나하나가 테스트 갯수가 되는 것입니다.

  사실은 위의 Jenkins 상의 `Test Result Trend` 의 그래프가 어느 순간 갑자기 뚝 떨어지길래, 어라 이상하다 싶어서 살펴본 결과, `subtest` 기준으로 카운트되어있는 것을 확인했습니다.
  
![Jenkins Test Result Trend 의 낙폭][img-test-result-trend]

  대충 위의 `#58` 번 빌드가 그 쯤이 되겠네요.

  아무튼 Jenkins 도입이후에 좀 더 테스트코드에 더 신경을 쓰고 있는 요즘입니다.
  
[cpan-test-more]:http://search.cpan.org/perldoc?Test::More
[img-test-result-trend]:https://lh5.googleusercontent.com/-TSX-Kd5-fX4/TwWRjvHx2LI/AAAAAAAAA94/pYrEEgHBZic/test-result-trend.png