---
layout: post
title: "Better Than Ack"
date: 2013-05-02T23:14:14+09:00
comments: true
external-url: 
categories: 
---

계속 쓰다보니 자연스레 손에 익은 이유도 있고, 여러 서버들에 `ack` 이 기본적으로 깔려 있지 않은 이유도 있습니다. 그것이 제게 있어서 `ack` 을 사용하지 않는 핑계가 되겠네요.

그런 현실중에 `ag` 를 소개하는 것은 조심스러울 따름입니다.

- [https://github.com/ggreer/the_silver_searcher](https://github.com/ggreer/the_silver_searcher)

위 github 페이지에서 확인할 수 있는 내용대로,

- `ack` 보다 3-5배 빠르고
- 검색시에 `.gitignore`, `.hgignore` 를 참고하여 검색대상에서 제외해줍니다.
- 그리고 `ack` 보다 한글자 더 적은 노력으로 검색할 수 있습니다. :-)

제 Github Pages Repo 안에 속한 1566 개의 파일을 대상으로 각각의 성능을 비교해봤습니다.

```
$ time ag Perl
……
……
source.old/_posts/2012-08-28-building-a-perl-project-on-travis-ci.markdown
11:`Travis CI`은 이전에 얘기를 들었을 때는 별  관심이 없었는데(처음에는 확실히 Perl 을 지원하지 않아서), 언제부턴가 Perl 을 지원한다는 얘기를 듣고 Perl 커뮤니티에서 움직임이 조금씩 있더라구요. `Dist::Zilla::TravisCI` 같은 모듈들도 나오는 걸로 봐서…
15: - [Travis CI - Building a Perl Project](http://about.travis-ci.org/docs/user/languages/perl/)
43:Perl may be copied only under the terms of either the Artistic License or the
44:GNU General Public License, which may be found in the Perl 5 source kit.
46:Complete documentation for Perl, including FAQ lists, should be found on
48:Internet, point your browser at http://www.perl.org/, the Perl Home Page.
……
ag Perl  0.07s user 0.14s system 128% cpu 0.164 total
```

```
$ time grep -R Perl .
……
./source.old/_posts/2012-12-26-a-usecase-of-app-fatpacker.markdown:  # ABSTRACT: Perl binding for Redis database
……
grep -R Perl .  1.13s user 0.09s system 77% cpu 1.573 total
```

```
$ time ack Perl
……
public/page/5/index.html
245:<a href="/2012/07/26/export-facebook-event-attending-list-perl-kr-teatime/">Export Facebook Event Attending List - Perl-kr TeaTime</a>
……
ack Perl  0.38s user 0.06s system 54% cpu 0.812 total
```

보시다시피 `ag` 의 위력은 대단합니다. 기본적으로 사용법도 `ack` 과 크게 다를 것도 없습니다.

문제는 기본적으로 사용할 수 있냐는 것이겠는데… 요즘 세상에는 `Rex` 같은 걸로 그냥 뭐 한번만 수고해주면 뭐 사용할 서버들에 일괄적으로 설치하는 것은 일도 아니지 않냐라고 그냥 스스로 수긍해버리면 될 것 같습니다.

