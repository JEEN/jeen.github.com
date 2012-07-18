---
layout: post
title: "Export Github Issues"
date: 2012-07-18 15:44
comments: true
categories: [ perl, github ]
---

 회사에서는 한달에 $20 인가 지불하면서 Github 을 쓰고 있습니다.
 $20 플랜에서는 한정적인 Private Repo 밖에 만들지 못합니다. 그래서 완료가 된 프로젝트에 대해서는
Github Repo 를 닫고 사내 Git Repo 로 옮기는 방식으로 사용하고 있습니다.

 그럴거면 그냥 사내 Git Repo 를 사용할 것이지 왜 돈내가면서 Github 를 쓰느냐고 물으신다면,
Github 에서 제공해주는 Issue 관리라든가, Wiki 라든가 여러모로 통합이 잘되어 있고 깔끔해서 
사용하기 편하다고 그냥 그렇게 말하렵니다.

 아무튼 그러면서, 프로젝트 뿐 아니라 회사내에 발생하는 다양한 이슈들은 Bugzilla 에 기록해놓습니다.

 다 끝나서 닫아야할 프로젝트들을 사내 Repo 로 옮기는 것은 좋지만, Issues 안에 적어둔 깨알같은 내용들과 코멘트들 등등을 고스란히 다 허공으로 날려버려야 된다는 것은 뼈아픈 일입니다.

 그래서 잠깐 시간을 내서 Issues 에 있는 내용(이슈타이틀,내용,코멘트, Git commit, 이슈 참조 등등)을 뽑아서 Bugzilla 에 담아놓기 좋게 코드를 써봤습니다.


``` perl export-github-issues.pl
use strict;
use warnings;
use Text::Xslate;
use Data::Section::Simple;
use Net::GitHub::V3;
use Config::Pit;

my $pit = pit_get('github.com', require => {
    login => "Your login",
    pass  => "Your pass",
});

my $github = Net::GitHub::V3->new(
    login => $pit->{login},
    pass  => $pit->{pass},
);

my ($user, $repo) = @ARGV;
unless ($user && $repo) {
    print "Usage: perl p.pl silexkr Donnenwa\n";
    exit;
}

my $tx  = Text::Xslate->new(
    syntax => 'TTerse',
    module => [ 'Text::Xslate::Bridge::TT2Like' ],
    path => [
        Data::Section::Simple->new()->get_data_section()
    ],
);

my $issue = $github->issue;
my @issues = $issue->repos_issues($user, $repo, { state => 'closed' });
while($issue->has_next_page) {
    push @issues, $issue->next_page;
}

for my $iss (@issues) { 
    my @comments = ();
    my @events   = ();
    eval { @comments = $issue->comments('silexkr', $repo, $iss->{number}) };
    eval { @events   = $issue->events('silexkr', $repo, $iss->{number}) };
    print $tx->render('text.tx', { %{ $iss }, comments => \@comments, events => \@events });
}

__DATA__

@@ text.tx

[% number %].[% title %] [% FOREACH label IN labels %][% label.name %] [% END %]
  - [% user.login %]
  - [% created_at %] ~ [% closed_at %]
[% IF body %]
// [% body %]
[% END %]
[% FOREACH comment IN comments %]
= [% comment.user.login %] @ [% comment.updated_at %]
-- [% comment.body %]
[% END %]
[% FOREACH event IN events %][% NEXT UNLESS event.commit_id %]
= [% event.commit_id %][% END %]
```

  [Net::GitHub](http://metacpan.org/module/Net::GitHub) 모듈에서 뭔가 코멘트를 뽑아내려는 데 없으면 계속 죽어버리는 문제 등등이 있어서 뭐 그냥 `eval {}` 로 묶어버렸구요.

  [Text::Xslate](http://metacpan.org/module/Text::Xslate) 에서 `path` 를

``` perl
Data::Section::Simple->new()->get_data_section();
``` 

 으로 해서 쉽게 `->render()` 의 첫번째 인수를 템플릿이름으로 지정할 수 있더군요.

 `Net::GitHub` 의 `->next_page` 가 최근 최근에 사용된 API 기준으로 동작을 하다보니, `while()` 안에서 `events`, `comments` 를 혼용해서 사용해버리면 의도한 대로 동작을 하지 않아서, `@issues` 안에 일단 전체 이슈를 뽑고서 시작을 했습니다.

 아무튼 뭐 얼기설기 그냥 놔두기도 그렇고 혹여나 해서 일단 올려봅니다.