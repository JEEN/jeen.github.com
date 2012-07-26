---
layout: post
title: "Export Facebook Event Attending List - perl-kr TeaTime"
date: 2012-07-26 14:22
comments: true
categories: [ perl, Facebook ]
---

 지난번 1회 티타임때의 문제가 제대로 참가비 징수를 못해서 비용정산에 어려움이 있었는데,
이 문제를 해결하기 위해서 간단하게 참가자 리스트를 A4 용지로 뽑아내서 체크하도록 하는 건 어떨까 하는 생각이 있었습니다.

 [#perl-kr 티타임 Vol.2](http://jeen.tistory.com/entry/perlkr-%ED%8B%B0%ED%83%80%EC%9E%84-2-%EC%84%B1%ED%99%A9%EB%A6%AC%EC%97%90-%EB%81%9D%EB%82%AC%EC%8A%B5%EB%8B%88%EB%8B%A4)

 아무튼 그런 생각을 잠깐 했다가 해볼까 해서 아래처럼 코드를 써넣었습니다.


``` perl
use strict;
use warnings;
use Text::Xslate;
use Data::Section::Simple;
use Facebook::Graph;

my $fb = Facebook::Graph->new;
$fb->access_token("YOUR ACCESS TOKEN");
my $r = $fb->fetch('466164950078338/attending');
my @attendees;
for my $person (@{ $r->{data} }) {
    my $f = $fb->fetch($person->{id});
    $f->{profile_image} = $fb->picture($person->{id})->get_large->uri_as_string;  
    push @attendees, $f;
}
push @attendees, {} for (1..3);

my $tx  = Text::Xslate->new(
    syntax => 'TTerse',
    module => [ 'Text::Xslate::Bridge::TT2Like' ],
    path => [
        Data::Section::Simple->new()->get_data_section()
    ],
);

print $tx->render('template.tx', { attendees => \@attendees });

__DATA__

@@ template.tx
<html>
 <head>
   <title>#perl-kr 티타임 #2</title>
   <style>
     [% INCLUDE "css.tx" %]
   </style>
 </head>
 <body>
   <h1>※ #perl-kr 티타임 #2</h1>
   <table class="table table-striped table-bordered" style="width:50%">
     <thead>
       <tr>
         <th class="span1">&nbsp;</th>
         <th class="span1">&nbsp;</th>
         <th class="span2">Name</th> 
         <th class="span1">Attend?</th>
       </tr>
     </thead>
     <tbody>
       [% FOREACH p IN attendees %]
       <tr>
         <td>[% loop.count %]</td>
         <td><img src="[% p.profile_image %]" width="30" height="30" /></td>
         <td>[% p.name %][% IF p.username %]([% p.username %])[% END %]</td> 
         <td></td>
       </tr>
       [% END %] 
     </tbody>
   </table>
 </body>
</html>

@@ css.tx
table {
  max-width: 100%;
  border-collapse: collapse;
  border-spacing: 0;
}

.table {
  width: 100%;
  margin-bottom: 18px;
}
.table th, .table td {
  padding: 8px;
  line-height: 18px;
  text-align: left;
  vertical-align: top;
  border-top: 1px solid #ddd;
}
.table th {
  font-weight: bold;
}
.table thead th {
  vertical-align: bottom;
}
.table thead:first-child tr th, .table thead:first-child tr td {
  border-top: 0;
}
.table tbody + tbody {
  border-top: 2px solid #ddd;
}
.table-condensed th, .table-condensed td {
  padding: 4px 5px;
}

.table-bordered {
  border: 1px solid #ddd;
  border-collapse: separate;
  *border-collapse: collapsed;
  -webkit-border-radius: 4px;
  -moz-border-radius: 4px;
  border-radius: 4px;
}
.table-bordered th + th, .table-bordered td + td, .table-bordered th + td, .table-bordered td + th {
  border-left: 1px solid #ddd;
}
.table-bordered thead:first-child tr:first-child th, .table-bordered tbody:first-child tr:first-child th, .table-bordered tbody:first-child tr:first-child td {
  border-top: 0;
}
/* line 75, ../../../../../../.rvm/gems/ruby-1.9.2-p290/gems/bootstrap-sass-2.0.1/vendor/assets/stylesheets/bootstrap/_tables.scss */
.table-bordered thead:first-child tr:first-child th:first-child, .table-bordered tbody:first-child tr:first-child td:first-child {
  -webkit-border-radius: 4px 0 0 0;
  -moz-border-radius: 4px 0 0 0;
  border-radius: 4px 0 0 0;
}
.table-bordered thead:first-child tr:first-child th:last-child, .table-bordered tbody:first-child tr:first-child td:last-child {
  -webkit-border-radius: 0 4px 0 0;
  -moz-border-radius: 0 4px 0 0;
  border-radius: 0 4px 0 0;
}
.table-bordered thead:last-child tr:last-child th:first-child, .table-bordered tbody:last-child tr:last-child td:first-child {
  -webkit-border-radius: 0 0 0 4px;
  -moz-border-radius: 0 0 0 4px;
  border-radius: 0 0 0 4px;
}
.table-bordered thead:last-child tr:last-child th:last-child, .table-bordered tbody:last-child tr:last-child td:last-child {
  -webkit-border-radius: 0 0 4px 0;
  -moz-border-radius: 0 0 4px 0;
  border-radius: 0 0 4px 0;
}
.table-striped tbody tr:nth-child(odd) td, .table-striped tbody tr:nth-child(odd) th {
  background-color: #f9f9f9;
}

.table tbody tr:hover td, .table tbody tr:hover th {
  background-color: #f5f5f5;
}

table .span1 {
  float: none;
  width: 44px;
  margin-left: 0;
}
table .span2 {
  float: none;
  width: 124px;
  margin-left: 0;
}
table .span3 {
  float: none;
  width: 204px;
  margin-left: 0;
}
table .span4 {
  float: none;
  width: 284px;
  margin-left: 0;
}
table .span5 {
  float: none;
  width: 364px;
  margin-left: 0;
}
table .span6 {
  float: none;
  width: 444px;
  margin-left: 0;
}
table .span7 {
  float: none;
  width: 524px;
  margin-left: 0;
}
table .span8 {
  float: none;
  width: 604px;
  margin-left: 0;
}
table .span9 {
  float: none;
  width: 684px;
  margin-left: 0;
}
table .span10 {
  float: none;
  width: 764px;
  margin-left: 0;
}
table .span11 {
  float: none;
  width: 844px;
  margin-left: 0;
}

table .span12 {
  float: none;
  width: 924px;
  margin-left: 0;
}

```

 급작스레 한 작업이라 그냥 간단히 HTML 로 뽑아버렸지만... 사실 꿈은 좀 더 아름다운 레이아웃으로 뙇하고 나와줬으면                                                                 했었지요.

 일단 예정 참가자 이외에 예상치못한 참가자가 있을 것을 염두해서 3개정도의 행을 더 추가해주었구요;;

 `Text::Xslate` + `Data::Section::Simple` 의 조합은 파일하나에 우걱우걱 템플릿까지 끼워넣고 이럴 경우에는 아주 좋았습니다.

 CSS 도 그냥 별도의 섹션으로 나눠서 `INCLUDE` 해버릴 수도 있으니 :-)

 `Facebook::Graph` 모듈은 생각보다 좀 변태같습니다. 그 이유는 Facebook Graph API 도 변태같다는 근본적인 .... 그리고 Access Token 은 뭐 적당하게 Developer 페이지 가서 줏어서 넣었습니다. :-)

![](https://lh6.googleusercontent.com/-GrgEvhNP0d0/UBDW9W-YzLI/AAAAAAAACAQ/5G48cMzvryY/w389-h521-k/list.jpg)