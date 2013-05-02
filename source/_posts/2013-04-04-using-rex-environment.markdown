---
layout: post
title: "Using Rex - Environment"
date: 2013-04-04 12:28
comments: true
categories: [ perl, rex ]
---

 주위 사람들은 Chef나 Fabric 을 많이 쓴다고 하지만 ( 그보다 손으로 여전히 하는 사람들이 더 많은 것 같기도 ), Rex 에 그냥 적응하면서 여전히 [Rex](http://rexify.org) 를 사용하고 있습니다. 회사를 옮기고 새로운 작업환경을 둘러보다가 이전의 패턴이 그대로 먹히지 않기에 기존에 사용하던 Rexfile 의 구조도 살며시 바꿔나가야 했습니다.
 
Rex 초기 설정
-----------

 Rex 가 설치되어 있다면, `rexify` 커맨드를 사용할 수 있습니다. 이 커맨드를 이용해서 초기 뼈대를 만들어 봅니다.

```
$ rexify M
```

그러면 아래와 같은 구성으로 Rex 를 사용하기 위한 기본 구성이 마련됩니다.

```
$ tree
.
├── Rexfile
└── lib
    └── M.pm
```

`Rexfile` 에는 온갖 설정 정보를 넣고, M.pm 에는 각 Task 를 정의해둡니다.

서버 그룹 위의 또 다른 그룹을 정의한다?
------------------------------

　지금까지는 서버의 목적, 그러니까 `DB` 서버 면 `DB` 그룹에 , `Web` 서버면 `Web` 그룹에 묶으면 되었는데요. 하지만 현재의 회사에서는 서비스그룹이 나뉘어져 있기도 하고, 계정정보도 좀 다르기도 하고 그런 게 있습니다. -_-;

 그래서 `environment` 로 각 그룹을 묶어서 별도로 관리하기로 했습니다.

 그런 것을 고려해서 써놓은 Rexfile 은 아래와 같습니다.

```
use Rex -feature => 0.40;

environment sdt => sub {
    set user => "xxxx";
    set password => "xxxxxx";
    set -passauth;

    set group => "all" => qw/1.2.3.51 1.2.3.52 1.2.3.53 1.2.3.54 1.2.3.55 1.2.3.56/;
    set group => "db"  => qw/1.2.3.53 1.2.3.54/;
    set group => "web" => qw/1.2.3.51 1.2.3.52 1.2.3.55 1.2.3.56/;
};

environment pshd => sub {
    set user => "xxxx";
    set password => "xxxxxxxx";
    set -passauth;
    set group => "all" => qw/1.2.4.31 1.2.4.32 1.2.4.33 1.2.4.34 1.2.4.35 1.2.4.36/;
    set group => "db"  => qw/1.2.4.35 1.2.4.36/;
    set group => "web" => qw/1.2.4.31 1.2.4.32 1.2.4.33 1.2.4.34/;
};

require M;
```

이렇게 `sdt` 와 `pshd` 라는 environment 를 따로 빼놓고 그 안에 각각 전체와 Web, DB 각각을 정의해둡니다.

 `M.pm` 에는 `restart-opsview-agent`, `restart-munin-node`, `restart-apache`, `deploy-apps` 같은 뭐 이런저런 Task 들이 정의되어 있다고 치고…

 그런 것들을 각 environment 의 그룹단위로 실행시키도록 합니다.

```
$ rex -E sdt -G all M:restart-opsview-agent
```

 위처럼 `-E` 의 인자값으로 environment 를 지정하고 `-G` 의 인자값으로 all 그룹을 지정해줌으로써, sdt 의 전체 서버에 대해서 `opsview-agent` 를 재시작하게끔 합니다.

결론
---

 사실은 뭐 각 서버에 SSH Key 를 박아넣고 사용해야 좀 더 안심이 되기도 하지만, 여기에는 뜻모를 어른들의 사정이 숨어져 있는지라 쉽게 그럴수는 없기도 하답니다. -_-; 좀 더 시간을 두고 뭐 여러가지 불안요소와 편의성을 도모하기로 하고…

 터미널 사용에 익숙하지 않은 팀원들을 위해서 `Rex::WebUI` 도 살며시 검토해봤는데, 아직까지는 Task 에 기재된 정보를 기준으로 읽어들이는 것 밖에 되지 않는지라… 위처럼 그룹을 지정하거나 environment 를 지정하거나 하는 것은 불가능하군요. 뭐 어차피 서버 데몬을 아무나 만지게 하고 그러는 것도 조심스러운 지라… -_-;; 꼭 모두가 써야하는 것인가 하는 의문이 있어서, 이건 뭐 전제자체가 붕괴되는 군요.
 
 아무튼 Rex 괜찮습니다.