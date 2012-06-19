---
layout: post
title: "DBIx::Class + Replication"
date: 2012-06-17 19:05
comments: true
categories: [ perl,dbix-class ]
---

 일반적으로 회사에서 개발하는 규모에서는 Replication 을 크게 고려하지 않았었는데, 이번에 수주받은 프로젝트에서 DBIx::Class 사용시에 Replication 에 대해서 고려해야 할 부분이 있었습니다.

 이전 회사에서 MySQL Replication 을 설정할 때 slave 서버들의 앞단에 keepalived 를 놓아서 로드 밸런서 역할로 사용했던 적이 있습니다. 말그대로 부하분산의 목적도 있고, 많은 slave 서버들의 커넥션정보를 일일이 웹 어플리케이션에서 설정할 필요도 없었죠. 하지만 IDC 에 로드밸런서 서버 한대를 더 우겨넣어야 했었죠.

 뭐 그리고 어플리케이션 로직에서도 master, slave 를 명시적으로 지정해야할 필요도 있었습니다.

 최근에는 HAProxy 를 사용해서 이런 저런 구성을 하고 있다는 얘기를 들었지만 마땅히 실제업무에서는 사용해보지 못해서 뭐라 말도 못하겠군요.

 그나저나 DBIx::Class 에서는 0.081x 버젼부터 자체적으로 Replication 설정을 할 수 있게 되었습니다. 위에서 로드밸런서를 사용하는 것과는 달리 slave 서버들의 접속정보를 모두 적어야할 필요가 있습니다. 하지만 설정만으로 갱신/참조용 쿼리를 어플리케이션 로직에 상관없이 분리시킬 수 있습니다.

- [DBIx:Class::Storage::DBI::Replicated - metacpan.org](https://metacpan.org/module/DBIx::Class::Storage::DBI::Replicated)

 DBIx::Class::Storage::DBI::Replicated 의 SYNOPSIS 에 나와있는 대로 하면 됩니다.

    ....
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

 DBIx::Class 에서 위처럼, ->storage_type 에 DBI::Replicated 를 지정하고 그에 적합한 인수를 넘겨주면 됩니다. `balancer_type` 은 기본적으로 `First`, `Random` 이 있습니다. 일단 저는 `Random`을 사용하구요. 

 balancer_args에서 `auto_validate_every` 를 통해서 매초당 Replication 의 유효성을 확인합니다. 0으로 지정되어 있을 경우에는 뭐 안하겠다고 생각할 수 있지만... 그게 아니라 매번 쿼리를 날릴때마다 체크를 하게 됩니다.

 pool_args 의 `maximum_lags` 는 Replication 서버에 대한 유효성 체크에서 해당 수치를 초과하면 자동으로 Replication Pool 에서 해당 서버에 대한 접속을 빼버리게 됩니다.

 ->connection 에서 지정한 것은 master 서버, ->storage->connect_replicants 에서 복수의 slave 서버들을 지정합니다.

 그럼 Replication 체크는 어떻게 하고 있을까요?

    sub is_replicating {
        my $status = shift->_get_dbh->selectrow_hashref('show slave status');
        return ($status->{Slave_IO_Running} eq 'Yes') && ($status->{Slave_SQL_Running} eq 'Yes');
    }

 `DBIx::Class::Storage::DBI::mysql` 에서 확인할 수 있습니다만, `show slave status` 를 날려서 Slave_IO_Running, Slave_SQL_Running 값을 보고 Replication 체크를 수행하고 있는 것이죠.  뭐 물론 mysql 의 경우입니다만, 만약 DBD::* 에서 is_replicating 이 정의가 되어 있지 않다면 master 로의 fail-over 같은 건 동작하지 않습니다.

 화면상에서 이뤄지는 각각의 쿼리들이 정말로 제대로 가고 있을까 확인해보고 싶을 경우도 있을 겁니다. 그때는...

    $schema->storage->debug(1);

  을 지정함으로, 어떤 쿼리가 어느 DB 서버로 향하는 지에 대한 정보가 화면에 뿌려지기에 눈으로 확인하기 좋겠죠.

 만약 Replication 이 제대로 설정되지 않으면 자동으로 모든 쿼리는 master 서버로 향하게 됩니다.

    Exception trying to ->ensure_connected for replicant dbi:mysql:bto:localhost, error is Class::MOP::Class:::around(): DBI Connection failed: DBI connect('my app:localhost','user',...) failed: Access denied for user 'user'@'localhost' (using password: YES) at /Users/jeen/perl5/perlbrew/perls/perl-5.14.2-llvm/lib/site_perl/5.14.2/DBIx/Class/Storage/DBI.pm line 1249. at /Users/jeen/perl5/perlbrew/perls/perl-5.14.2-llvm/lib/site_perl/5.14.2/darwin-2level/Class/MOP/Method/Wrapped.pm line 162
    No Replicants validate, falling back to master reads.

 이렇게 몇줄만의 설정으로 간단하게 어플리케이션단에서 Replication 설정을 수행할 수 있습니다...만, 여러가지 운용이슈가 발생할 테고 그에 맞춰서 적당하게 답을 내야할 경우가 생기겠죠. 네, 뭐 그건 그때 가서... 
