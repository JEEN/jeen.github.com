---
layout: post
title: "DBIx::Class and JSON"
date: 2012-08-25 12:44
comments: true
categories: [ perl,catalyst,dbic ]
---

 흔한 웹서비스의 흔한 AJAX 나 흔한 RESTful API 를 제공하기 위해서, 대개 JSON 포맷의 데이터를 서버에서 뿜어내고는 합니다.
 
 하지만 누구나가 Catalyst + DBIx::Class 조합에서 이렇게 하면 되는 게 아닐까 하고 접근했다가…
 
> … encountered object '..', but neither allow_blessed nor convert_blessed settings are enabled …

 라는 에러를 접하게 됩니다. bless 된 오브젝트를 어떻게든 JSON 인코딩을 시도하려고 했을 때는 위와 같은 에러를 쉬이 접할 수 있습니다.

 이것을 푸는 방법은 사실 여러가지가 있습니다.
 
 이것저것 신경쓰지 말고 한번에 가자고 할 때는 Catalyst::View::JSON 에서 인코딩하게 되는 stash 된 값들을 전부 ArrayRef 나 HashRef 로 변환해서 넘기는 방법입니다.

``` perl
   my @data;
   my $rs = $c->model('DB')->resultset('User')->search();
   while(my $row = $rs->next) {
       push @data, {
           id => $row->id,
           name => $row->name,
           created_at => $row->created_at,
       };
   }
   $c->stash->{people} = \@data;
```

 위처럼 특정컬럼을 선별해서 넣어주는 방법이 있겠지요. 좀 더 단순화한다면, 각  스키마테이블 별로 HashRef 를 반환하는 메소드를 넣어두는 것도 좋습니다.

``` perl  lib/MyApp/Schema/Result/User.pm
sub to_hashref {
    my $self = shift;
    return {
        id => $self->id,
        name => $self->name,
        created_at => $self->created_at,
    };
}

   # Controller
   my @data;
   my $rs = $c->model('DB')->resultset('User')->search();
   while(my $row = $rs->next) {
       push @data, $row->to_hashref;
   }
   $c->stash->{people} = \@data;
```

``` perl
  my $person = $c->model('DB')->resultset('User')->search($cond, {
      result_class => 'DBIx::Class::ResultClass::HashRefInflator',
  })->first;
  $c->stash->{person} = $person;
```

 그럴 필요가 없다면 위처럼 `result_class` 를 지정해줌으로써 결과를 무조건 HashRef 로 반환하게끔 합니다.
 
 사실 `DBIx::Class::ResultClass::HashRefInflator` 를 적용하면 일일이 오브젝트를 만들어내지 않기 때문에 퍼포먼스 측면에서 매우 좋습니다만, 그냥 HashRef 일 따름인지라Resultset 에서 정의한 각종 메소드를 참조할 수 없습니다.
 그러니 이런저런 경우에는 가려서 사용하는 것이 좋습니다.
  
 이제 본질적으로 좀 더 접근해보면…
 JSON::XS 는 인코딩할 시에, 오브젝트가 대상인 경우에는 해당 오브젝트의 TO_JSON 메소드를 참조하여 결과를 뽑아낼 수 있습니다. 물론 `allow_blessed`, `convert_blessed` 플래그를 지정할 필요가 있지요.

``` perl
# lib/MyApp/View/JSON.pm
package MyApp::View::JSON;
use JSON::XS ();

use parent qw(Catalyst::View::JSON);

my $encoder = JSON::XS->new
              ->utf8
                          ->pretty(0)
                          ->indent(0)
                          ->allow_blessed(1)
                          ->convert_blessed(1);

sub encode_json {
    my ($self, $c, $data) = @_;
    $encoder->encode($data);
} 

1;
```

 그러면 위처럼 `encode_json` 을 오버라이드 합니다. `allow_blessed` 와 `convert_blessed`  플래그를 켜주는 것이죠.

``` perl
# lib/MyApp/Schema/Result/User.pm
..
sub TO_JSON {
    return { $_[0]->get_inflated_columns };
}
..
```

그리고 위처럼 User Result 에 대해서 TO_JSON 를 지정합니다.

``` perl
   $c->stash->{people} = [  $c->model('DB')->resultset('User')->search()->all ];
```

그러면 이제는 아무런 걱정없이 그냥 생짜 DBIC Resultset 을 날려버려도 이제는 알아서 문제해결이 됩니다. :-)

 각 Resultset 마다 `TO_JSON` 을 지정하기가 벅차다면…
 
 - [Seoul.pm Advent Calendar 2011 - 열일곱번째 날 : DBIx::Class로 스키마 관리하기](http://advent.perl.kr/2011/2011-12-17.html)
 
위의 글에서 `ResultBase` 를 참고해주세요.

 `MyApp::Schema::ResultBase` 를 상속받은 각 Result 테이블들은 `TO_JSON` 을 오버라이드 해서 빼놓고 싶은 컬럼(예를 들어 `password` 같은 컬럼) 을 배제해서 출력할 수 있지 않을까요?
 
 

