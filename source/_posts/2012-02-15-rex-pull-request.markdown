---
layout: post
title: "Rex Pull Request"
date: 2012-02-15 23:21
comments: true
categories: [github,rex]
---

* [(R)?ex - A simple framework to simplify systemadministration][rex-homepage]

  최근에 여러 서버 일괄 작업을 위해서 [Rex][cpan-rex] 로 이것저것 해보고 있는 중입니다. Rex 는 Ruby 쪽에서 많이 쓰인다는 Chef 같은 부류의 툴이랄까요, 아무튼 그런 류의 일을 Perl 로 할 수 있게 하는 툴입니다.

  Rex 에 속한 네임스페이스를 살펴보면 다양한 OS 의 경우에 상응하는 다양한  케이스를 고려하고 있는 것 같습니다.  

  오늘 제가 쓴 Rex 의 가벼운 예제로는 다음과 같습니다.
  
``` perl Rexfile
user 'user';
password 'password';

pass_auth;

group => "sweet-vms" => "vm[01..10]";

require Rex::Init;

```


``` perl Rex::Init
package Rex::Init;
use Rex::Commands;
use Rex::Commands::Run;
use Rex::Commands::Pkg;
use Rex::Commands::Iptables;

desc 'Add yum repo';
task "add_yum_repo", group => "sweet-vms", sub {
    run("yum -y install http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.2-2.el5.rf.x86_64.rpm");
    print "Added RPMForge Repo on /etc/yum.repos.d/\n";

    repository add => "Opsview",
        url => 'http://downloads.opsera.com/opsview-community/latest/yum/centos/$releasever/$basearch';
    print "Added OpsView Repo on /etc/yum.repos.d/\n";

    update_package_db;
    print "Updated yum package DB\n";
};

desc 'Install OpsView-Agent';
task "install_opsview_agent", group => "sweet-vms", sub {
    install package => 'opsview-agent';
    print "Installed [opsview-agent]\n";
};

desc 'Open NRPE(5666) Port';
task 'open_nrpe_port', group => "sweet-vms", sub {
    open_port 5666;
};
```

이렇게 `Rexfile` 에 각 서버 그룹과 SSH 접속 계정, 그리고 `Rex::Init` 에 그 그룹에 대한 작업(Task) 를 정의합니다.

  위처럼 정의한 작업에 대해서,
  
``` bash
$ rex -T
Tasks
  Init:add_yum_repo              Add yum repo
  Init:install_opsview_agent     Install OpsView-Agent
  Init:open_nrpe_port            Open NRPE(5666) Port
Batches
```

  `rex -T` 를 통해서 작동할 수 있는 Task 의 리스트를 받을 수 있습니다.

  그리고, 
  
 ``` bash
 $ rex 'Init:add_yum_repo' 'Init:install_opsview_agent'
 ```
 
   이런식으로 동작할 작업들을 지정해줌으로, 위의 `sweet-vms` 그룹의 vm01 부터 vm10 의 의 열대의 서버에 대해서 `RPMForge` 와 `OpsView` `yum repo` 를 추가하고, `opsview-agent` 를 설치합니다. `opsview-agent` 자체는 기본적인 yum package 에 없는 라이브러리를 참조하기에 `RPMForge` 를 우선 등록한 것입니다.

  예, 뭐 아무튼 이런 식입니다. 아직 뭐 본격적으로 이런저런 상황에 부딪히면서 검증하고 있고, Rex 자체에서 어떻게 할 수 없으면 직접 패치를 써나가 볼 까합니다.

  그런 의미에서, 오늘 Rex 에 Pull Request 를 하나 보내서 0.25 에 Merge 되었습니다. :-)

* [https://github.com/JEEN/Rex/commit/1d69a96ba4371dc9e030fab494c887b9126a2c9d][rex-gpgcheck]
* [http://github.com/krimdomu/Rex/pull/12][rex-pull-12]

  사실 위에서 `opsview-agent` 는 제대로 설치되지 않습니다. 이유는 Pull Request 를 보내면서 봉착한 상황과 대처로 적어놨습니다.

<blockquote class="twitter-tweet"><p>Rex 무려 GPL v3 였구나...</p>&mdash; JEEN (@JEEN_LEE) <a href="https://twitter.com/JEEN_LEE/status/169724680612216832" data-datetime="2012-02-15T10:08:11+00:00">February 15, 2012</a></blockquote>

[rex-gpgcheck]:https://github.com/JEEN/Rex/commit/1d69a96ba4371dc9e030fab494c887b9126a2c9d
[rex-homepage]:http://rexify.org/
[cpan-rex]:http://metacpan.org/module/Rex
[rex-pull-12]:http://github.com/krimdomu/Rex/pull/12