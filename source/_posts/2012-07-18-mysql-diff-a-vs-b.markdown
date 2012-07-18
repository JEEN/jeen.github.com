---
layout: post
title: "MySQL::Diff - A vs B"
date: 2012-07-18 18:30
comments: true
categories: [ mysql, perl ]
---

 최근에 유지보수 관련 일로 골머리를 싸고 있다가 겨우겨우 마무리하고 있는 일이 하나 있었습니다.

 PHP 프로젝트였는데, 유지보수 관련해서 여러가지 이슈들이 나오기 마련이고, 그 때문에 잘 움직이던 기존의 DB구조를
변경해줄 필요가 있었습니다.

 뭐 테이블을 추가하거나, 어떤 컬럼의 형식을 변경하거나, 혹은 인덱스를 추가하거나 하는 뭐 이런 경우 말이죠.

 처음에는 개발환경아래에서 기존의 product 환경에서 운용중인 DB를 dump 떠와서 진행하고, 프로젝트를 진행하면서
변경되는 SQL 내용들을 일일이 적어주었습니다. 그러다가 뭐 이사람 저사람 손을 옮겨타다가 결국 최종 마무리단계에서 
어떤 컬럼이 변경되었는지, 어떤 테이블이 추가되었는지에 대한 확신을 할 수 없었습니다.

 그래서 기록된 SQL 을 기반으로 눈과 손으로 검수를 해야하는 지경에서 결국 실수가 생기기 마련이죠. 그리고 클레임이 발생했습니다.

 자, 그럼 어떻게 하면 이런 상황에서 사태를 수습하느냐... 고심했습니다.

 그 결과 혹시 이거라면 해서 찔러본 것이 빙고 였던 것이 [MySQL::Diff](http://metacpan.org/module/MySQL::Diff) 였습니다.

 맨처음 덤프떠온 DB 를 `DB_A` 로 지정하고, 최종적으로 개발이 종료된 DB 를 `DB_B` 라고 놓습니다.

 이 `DB_A` 와 `DB_B` 를 서로 비교하는 것이죠. 그걸을 위해서 `MySQL::Diff` 모듈이 사용됩니다.


``` bash
$ mysqldiff --host1 localhost --user1 root --password1 whatthehell --host2 localhost --user2 root --password2 whatthehell bto bto_test
## mysqldiff 0.43
## 
## Run on Wed Jul 18 18:26:39 2012
## Options: password2=whatthehell, password1=whatthehell, user1=root, host2=localhost, debug=0, host1=localhost, user2=root
##
## ---   db: bto (host=localhost user=root)
## +++   db: bto_test (host=localhost user=root)

ALTER TABLE auth DROP COLUMN updated_on; # was datetime NOT NULL DEFAULT '0000-00-00 00:00:00'
ALTER TABLE auth DROP COLUMN created_on; # was datetime NOT NULL DEFAULT '0000-00-00 00:00:00'
ALTER TABLE auth ADD UNIQUE passwd (passwd);
CREATE TABLE test1 (
  test1 int(11) NOT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
```

위처럼 컬럼의 삭제, 인덱스의 추가, 테이블의 추가 등 모든 변경 내역들이 나오게 됩니다.

다음부터는 이처럼 좀 더 간단하고 확실하게 할 수 있는 방법을 취해야 되겠습니다.