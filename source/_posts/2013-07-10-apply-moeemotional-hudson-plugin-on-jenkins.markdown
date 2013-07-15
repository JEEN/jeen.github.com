---
layout: post
title: "Apply MoeEmotional Hudson Plugin on Jenkins"
date: 2013-07-10T22:28:59+09:00
comments: true
external-url: 
categories: [ jenkins ]
---

<blockquote class="twitter-tweet"><p>젠킨스에서 대머리 아저씨 얼굴 보기가 싫어서, 급하게 테마 하나 제작했습니다. <a href="https://t.co/FcNPdqMjlO">https://t.co/FcNPdqMjlO</a> 앞으로 플랫플랫하게 해볼 예정.</p>&mdash; Park Hyun-woo (@lqez) <a href="https://twitter.com/lqez/statuses/354925228079579138">July 10, 2013</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

 이 모든 원인은 @lqez 님의 트윗에서 시작했습니다.
 
 이 트윗을 보고 예전에... 그러니까 일본에서 일하고 있을 당시 CI 에는 별로 신경도 쓰지도 않던 시절에 Hatena bookmark 에서 모에짙은 플러그인에 대한 기사를 떠올렸습니다.

 - [Hudsonの萌え化、もしくは痛Hudson化](http://d.hatena.ne.jp/kanu-orz/20090803/1249225200)

　그러니까 당시 Jenkins 가 Hudson 이던 시절에 수염난 아저씨를 눈뜨고 쳐다볼 수 없었던 Hudson 사용자들은 `moeemotional-hudson` 이라는 플러그인을 만들어 냅니다.
　그와 관련된 링크는 아래와 같습니다.

- [hud子「継続的インテグレーションを、あなたに」](http://d.hatena.ne.jp/torazuka/20090731/1248970549)
- [RED_LAMP用のhud美を追加](http://d.hatena.ne.jp/torazuka/20090802/hudmis)
- [萌えよHudson(hud子バージョン公開)](http://d.hatena.ne.jp/kanu-orz/20090802/1249182957)

 그리고 모에말고 일반적인 Emotional Jenkins 플러그인도 존재합니다. -_-;
 
 - [https://wiki.jenkins-ci.org/display/JENKINS/Emotional+Jenkins+Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Emotional+Jenkins+Plugin)

 뭐 결론은 Stable, Unstable 에 따라서 각각의 상황에 맞는 그림을 대신 표시해주는 것이지요.

<blockquote class="twitter-tweet"><p>그러니까 그런 노력의 결과로... 이런 아저씨가... <a href="http://t.co/1d1TuMWbJs">pic.twitter.com/1d1TuMWbJs</a></p>&mdash; JEEN (@JEEN_LEE) <a href="https://twitter.com/JEEN_LEE/statuses/354939083795083265">July 10, 2013</a></blockquote>

<blockquote class="twitter-tweet"><p>이런 OL 이 된다는 것입니다. <a href="http://t.co/c3Wu2plYAT">pic.twitter.com/c3Wu2plYAT</a></p>&mdash; JEEN (@JEEN_LEE) <a href="https://twitter.com/JEEN_LEE/statuses/354939140883742720">July 10, 2013</a></blockquote>

 이 플러그인은 각 Job 단위로 설정이 가능합니다. 플러그인을 설치하고 post-build action 에서 Moeemotional Hudson 을 추가해줍니다. <strike>그러니까 Job 단위로 설정이 가능한게 Job A 페이지에서만 표시되고 해당 Job 의 빌드 결과에 따른 그림변화는 없었습니다.</strike> 아... 캐쉬에 의한 착각이었습니다. 각 빌드 페이지에서도 정상적으로 출력됩니다.
 
 아무튼 플랫플랫하게 나올 결과물을 기대해봅니다.
 
 - [Github - lqez/flat-jenkins](https://github.com/lqez/flat-jenkins)