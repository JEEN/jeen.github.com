---
layout: post
title: "DBIx::Class and Replication - II"
date: 2012-06-19 15:35
comments: true
categories: [ perl, dbix-class]
---

- [DBIx::Class + Replication](http://jeen.github.com/blog/2012/06/17/dbix-class-and-replication/)

그러니까 17일이죠.

> 이렇게 몇줄만의 설정으로 간단하게 어플리케이션단에서 Replication 설정을 수행할 수 있습니다…만, 여러가지 운용이슈가 발생할 테고 그에 맞춰서 적당하게 답을 내야할 경우가 생기겠죠. 네, 뭐 그건 그때 가서…

라고 지난 글의 말미에서 얘기했습니다만, 네 금방 운용이슈가 발생했습니다.

 넘겨준대로 설정해서 돌리려고 하는 데 이런 에러가 떴다는 것입니다.

``` bash
    Class::MOP::Class:::around(): DBI Exception: DBD::mysql::db selectrow_hashref failed: Access denied; you need the SUPER,REPLICATION CLIENT privilege for this operation [for Statement "show slave status"] at /home/*/perl5/perlbrew/perls/perl-5.14.2/lib/site_perl/5.14.2/i686-linux/Class/MOP/Method/Wrapped.pm line 162
```

 딱 봐도 DB 접속계정의 권한문제인데, 가장 간단한 답은 접속계정에 해당 권한을 부여하면 되는 것이었는데요.
 하지만 접속 계정을 추가해서 어떻게 하기에는 다른 운용이슈가 발생할 여지가 있다고 생각하기에(그럴리가...) 수정을 요구받았습니다. 

 결국 DBIx::Class::Storage::DBI::Replicated::* 아래의 소스를 파보도록 했습니다. 관련 동작은 `connect_replicants` 시에 `validate_replicants` 에서 각 Replicant 마다 `is_replicating` 체크를 하게 되는 것입니다.

 일단 뭐 예, DBI::Replicated 시에는 기본적으로 그냥 뭐 자동 fail-over 까지 슥샥슥샥 해줄 수 있는데... 그냥 이런 거 다 필요없다라는 합의점에 이르러 결국은 해당부분을 회피하도록 했습니다.

``` perl
    BEGIN {
        use DBIx::Class::Storage::DBI::Replicated::Pool;

        no warnings 'redefine';
        *DBIx::Class::Storage::DBI::Replicated::Pool::validate_replicants = sub {
            return 1;
        };
    };
```

 추가한 부분은 위와 같습니다. 컴파일시에 미리 `validate_replicants` 를 재정의해버리는 것이었습니다.

 전체 소스는 아래와 같습니다.

``` perl
    BEGIN {
        use DBIx::Class::Storage::DBI::Replicated::Pool;

        no warnings 'redefine';
        *DBIx::Class::Storage::DBI::Replicated::Pool::validate_replicants = sub {
            return 1;
        };
    };

    my $schema = MyApp::Schema->clone;
    $schema->storage_type([
        '::DBI::Replicated' => {
            balancer_type => '::Random',
            balancer_args => {
                auto_validate_every => 10,
                master_read_weight  => 0,
            },
            pool_args => {
                maximum_lag => 2,
            }
        }
    ]);
    $schema->connection({
        dsn      => "dbi:mysql:myapp:127.0.0.50",
        user     => "user",
        password => "whatthehell",
        RaiseError        => 1,
        AutoCommit        => 1,
        mysql_enable_utf8 => 1,
        on_connect_do     => ["SET NAMES utf8"],
        quote_char        => q{`},
    });
    $schema->storage->connect_replicants([
        {
            dsn      => "dbi:mysql:myapp:192.168.0.51",
            user     => "user",
            password => "whatthehell",
        },
        ....
    ]);
```

 그리고 그들은 행복하게 오래오래 살았답니다~. 끝.