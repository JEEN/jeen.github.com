---
layout: post
title: "Catalyst - ActionRole"
date: 2011-12-27 21:14
comments: true
categories:  [perl, catalyst,waf,actionrole,hook]
---

### Action Role

  오래된 이야기이지만 Catalyst 가 Moose 기반으로 만들어지면서 ActionRole 이라는 개념이 만들어졌었지요.
  실제 업무에서 적용해볼 껀덕지가 없어서 만져보지는 못했습니다만, 최근에 업무에서 ActionRole 을 적용해서 간단한 수정작업을 진행했습니다.

``` perl
package MyApp::Web::ActionRole::Logger;
use Moose::Role;
use namespace::autoclean;

after execute => sub {
    my ($self, $controller, $c) = @_;
    
    # ...
}

1;
```

  일단 업무에서 사용한 ActionRole 의 기본형은 위와 같습니다.

  Catalyst App 을 만들 때 사용한 네임스페이스를 기준으로 `ActionRole::[RoleName]` 을 사용합니다. 그러니 위에서 봤을 때 Catalyst App 의 이름은 `MyApp::Web` 이 되겠죠. 당연히 ActionRole 의 이름은 Logger 입니다.

  또한, Logger 라는 ActionRole 을 참조할 때, Catalyst 는 `MyApp::Web` 뿐만 아니라 `Catalyst::ActionRole::Logger` 가 있으면 이를 참조할 수 있습니다.
   `MyApp::Web::ActionRole::Logger` 와 `Catalyst::ActionRole::Logger` 두개가 존재할 시에는 앞의 `MyApp::Web::ActionRole::Logger` 를 우선적으로 사용하게 됩니다.
   
### 주의점

  위의 코드의 `after execute => sub { ... }` 에서 주의할 점이 한가지 있습니다. Catalyst 의 `auto`, `begin`, `end` 등의 Private Action 들 또한 실행이 된다는 점 입니다.
  예를들어 `/user/login` 이라는 수행하기 위해서 각 컨트롤러 마다 `auto`, `begin` 등의 Private Action 이 정의되어 있다고 하면, 아마 아래와 같이 나올 것입니다.
   
```
  .------------------------------------------------------------+-----------.
| Action                                                     | Time      |
+------------------------------------------------------------+-----------+
| /auto                                                      | 0.000600s |
| /user/auto                                                 | 0.000208s |
| /user/login                                                | 0.000219s |
| /user/end                                                  | 0.009542s |
|  -> MyApp::Web::View::Default->process                    | 0.008665s |
'------------------------------------------------------------+-----------'
```

  애시당초 `/user/login` 가 끝나면 무엇무엇을 한다라고 `ActionRole::Logger` 에 정의한 마당에서 해당 액션이 2-3회 중복해서 실행되는 경우가 발생하는 것입니다. 바로 `auto`, `end` 가 끝난 다음에도 해당 ActionRole 을 참고한다는 것이죠.

``` perl
package MyApp::Web::ActionRole::Logger;
use Moose::Role;
use namespace::autoclean;

after execute => sub {
    my ($self, $controller, $c) = @_;
    
    return if $self =~ /(?:auto|begin|end)/;
    
    # ...
}

1;
```

  그래서 적절하게 `auto`, `begin`, `end` 는 필터링해줍니다. `$self` 변수에 어떤 액션에서 온 것인지 알 수 있습니다. 전 처음에 계속 `$c->action` 으로 알고 착각을 했더랬습니다.

  아, 물론 ActionRole 을 추가해줬다고 바로 쓸 수 있는 것은 아닙니다.
  해당 ActionRole 을 사용할 컨트롤러의 기본 상속 모듈을 `Catalyst::Controller` 에서 `Catalyst::Controller::ActionRole` 로 바꾸도록 합니다.
  
``` perl
package MyApp::Web::Controller::Root;
use Moose;
use namespace::autoclean;
BEGIN { extends 'Catalyst::Controller::ActionRole` }
...
```

### Conclusion

  위의 예제에서는 그냥 단순히 `Root` 에서만 사용하고 있는 것 처럼 보이지만, 업무상에서는  특정 컨트롤러 몇개를 제외한 나머지 모든 컨트롤러에서 공통으로 사용하고 있습니다. 
  
  거의 모든 컨트롤러 상의 액션이 끝날 때, 시작될 때, 혹은 양쪽 모두에서 공통적으로 특정 행동을 정의할 때 ActionRole 과 함께하면 좀 더 유연하고 아름다운 코드작성에 도움이 되지 않을까 생각하고 있습니다.
  