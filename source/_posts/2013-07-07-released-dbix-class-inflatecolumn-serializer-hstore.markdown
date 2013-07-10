---
layout: post
title: "Released DBIx::Class::InflateColumn::Serializer::Hstore"
date: 2013-07-07T19:05:31+09:00
comments: true
external-url: 
categories: [ perl, pg ]
---

 예전에는 그냥 Pg 를 사용한 만들어진 웹서비스를 유지보수하는 수준에서 밖에 Pg 를 접하지 않았는데...
 최근의 한 프로젝트에서 Postgres 를 도입하기로 결정이 났습니다. 

 이전부터 Pg 의 hstore나 Array 타입이라든가 이런저런 유의미한 기능에 대해서는 기대하고 있었고, 약간 설레임이 있었습니다.
 
 작업을 하면서 살펴보니 hstore 의 경우는 Postgre Extension 으로 존재하며, DBIx::Class 는 아마도 DB 의 특정 Extension 까지 보살펴주지는 않겠지 하고 생각했습니다.
 
 결국 기본적으로 DBIx::Class에서 컬럼의 참조/등록시에 Serialization/Deserialization 하면서 쓰고, hstore 에 대해서 참조쿼리를 발생시킬 때는 적절한 스칼라레퍼런스로 사용할 수 밖에 없지 않겠는가 싶었습니다.
 
 그래서 뭐 결국은 DBIx::Class::InflateColumn::Serializer 모듈에 그냥 끼워넣기로 DBIx::Class::InflateColumn::Serializer::Hstore 라는 모듈을 추가해서 CPAN 에 올렸습니다.
 
 사용법은 CPAN 문서의 SYNOPSIS 를 참조하면 됩니다. :-)
 
 DBD::Pg 에서 UTF8 사용을 위해서 `pg_enable_utf8` 과 `Pg::hstore` 를 병용할 때 UTF8 표시문제가 있어서 기존 `DBIx::Class::InflateColumn::Serializer` 에는 없던 옵션인 `recursive_encode` 를 추가했습니다.
 
 UTF8 을 포함한 hstore 컬럼과 그렇지 않은 것들을 구분해서 사용하면 되지 않을까 싶습니다만... 
 
 뭐 아무튼 결론은 요 1주일 정도 Pg 로 옮기면서 되던 게 안된다든가, 이런게 된다든가 하는 다양한 문제를 접하며 즐거운 비명을 지르고 있습니다.