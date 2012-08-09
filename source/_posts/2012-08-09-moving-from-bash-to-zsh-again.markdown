---
layout: post
title: "Moving from bash to zsh again"
date: 2012-08-09 16:30
comments: true
categories: [zsh]
---

 밥벌이도구가 바뀌고 해서 다시 ZSH 로 돌아갔습니다.
 
<blockquote class="twitter-tweet" lang="ko"><p>레티나 맥북프로를 오늘 받은 게 자랑.txt <a href="http://t.co/38oGVx7W" title="http://jeen.tistory.com/560">jeen.tistory.com/560</a></p>&mdash; JEEN (@JEEN_LEE) <a href="https://twitter.com/JEEN_LEE/status/232821768325300225" data-datetime="2012-08-07T12:53:29+00:00">8월 7, 2012</a></blockquote>


-  [https://github.com/skwp/dotfiles](https://github.com/skwp/dotfiles)
  
지난번에 MacBook Air 에 올리다가 뭐가 꼬인 `YADR` 을 썼습니다. `grep` 경고메시지가 없이 깔끔하게 돌아가는 군요.
  
 이래저래 개발환경도 맞추고 그러다가 지난달에 zsh 5.0 이 릴리즈 된 것을 보고 Mt.LION 에 깔린 zsh 버젼을 확인하니..
 
``` bash
$ zsh --version
zsh 4.3.11 (i386-apple-darwin12.0)
```

`4.3.11` 이었습니다. 그래서 zsh 5.0 으로 올려보려고 brew 로 일단 깔았죠.

그리고 `chsh` 로 셸을 바꾸려고 하는 데…

``` bash
$ chsh -s /usr/local/bin/zsh
Changing shell for jeen.
Password for jeen: 
chsh: /usr/local/Cellar/zsh/5.0.0/bin/zsh: non-standard shell
```

이라고 뜨는 것이었지요.

구글검색결과 `/etc/shells` 에 해당 셸의 패스를 추가하는 것으로 이 난관을 헤쳐나갈 수 있다고 합니다.

그래서 네. zsh 5.0 쓰고 있는데 뭐가 좋은지는 써보면서 느끼게 되려나요.

- [https://github.com/zsh-users/zsh/blob/master/NEWS](https://github.com/zsh-users/zsh/blob/master/NEWS)

이런 CHANGELOG 도 일단 보고 있지만요...