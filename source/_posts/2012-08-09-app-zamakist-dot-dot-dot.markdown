---
layout: post
title: "App::Zamakist..."
date: 2012-08-09 13:33
comments: true
categories: [perl]
---

취미라고 할 것이 있다면 잠, 책, 드라마감상 정도라고 할까요.

걔중에 몇몇 미드들을 아직까지 꾸준하게 시간을 내면서 보고 있습니다.

걔중에는 가끔보면 자막이 없어서 일일이 자막찾으러 들어가고 받고 해야하는 지루한 클릭이 필요한 경우도 있습니다.

윈도라면 `곰플레이어` 로 이런 부분의 수고가 조금 줄어들기는 하지만 Mac 에서 `Movist` 를 통해서 주로 영상을 감상하려면 심히 신경쓰이지 않을리 없습니다.

<blockquote class="twitter-tweet" lang="ko"><p>멘탈리스트 4기를 볼려고 했는데, 자막이 없네... 그래서 자막을 세팅하는 스크립트를 깨작거려서 만들어봤다. 좀 더 브러쉬 업해야 되지만서도... <a href="https://t.co/jeXVNTnI" title="https://gist.github.com/3150026">gist.github.com/3150026</a></p>&mdash; JEEN (@JEEN_LEE) <a href="https://twitter.com/JEEN_LEE/status/226260944597180417" data-datetime="2012-07-20T10:23:07+00:00">7월 20, 2012</a></blockquote>

그러니까 7월 20일경에 일단 이런 문제를 겪어서 잠깐 깨작거려서 만들어보기는 했지만 대놓고 앞에 내놓기도 그렇고해서 그냥 보류했었는데요. 뭐 물론 여전히 앞에 내놓는 게 좀 그렇기는 합니다.

- [https://github.com/JEEN/p5-App-Zamakist](https://github.com/JEEN/p5-App-Zamakist)

일련의 과정을 말하자면…

``` bash
$ ls -la  
total 0
drwxr-xr-x  12 jeen  staff   408  8  9 13:45 .
drwxr-xr-x+ 52 jeen  staff  1768  8  9 13:45 ..
-rw-r--r--   1 jeen  staff     0  8  9 13:45 NCIS.Los.Angeles.S03E01.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff     0  8  9 13:45 NCIS.Los.Angeles.S03E02.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff     0  8  9 13:45 NCIS.Los.Angeles.S03E03.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff     0  8  9 13:45 NCIS.Los.Angeles.S03E04.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff     0  8  9 13:45 NCIS.Los.Angeles.S03E05.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff     0  8  9 13:45 NCIS.Los.Angeles.S03E06.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff     0  8  9 13:45 NCIS.Los.Angeles.S03E07.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff     0  8  9 13:45 NCIS.Los.Angeles.S03E08.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff     0  8  9 11:44 NCIS.Los.Angeles.S03E09.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff     0  8  9 09:29 NCIS.Los.Angeles.S03E10.720p.HDTV.X264-DIMENSION.avi
```
이렇게 뭐 디렉토리에 영상자료들이 널부러져 있습니다. 하지만 자막은 없죠.

```
$ zamakist --dir .
Found 10 subscriptions
┌────┬──────────────────────────────────────────────────────┐
│ id │ filename                                             │
├────┼──────────────────────────────────────────────────────┤
│  1 │ NCIS.Los.Angeles.S03E01.720p.HDTV.X264-DIMENSION.avi │
│  2 │ NCIS.Los.Angeles.S03E02.720p.HDTV.X264-DIMENSION.avi │
│  3 │ NCIS.Los.Angeles.S03E03.720p.HDTV.X264-DIMENSION.avi │
│  4 │ NCIS.Los.Angeles.S03E04.720p.HDTV.X264-DIMENSION.avi │
│  5 │ NCIS.Los.Angeles.S03E05.720p.HDTV.X264-DIMENSION.avi │
│  6 │ NCIS.Los.Angeles.S03E06.720p.HDTV.X264-DIMENSION.avi │
│  7 │ NCIS.Los.Angeles.S03E07.720p.HDTV.X264-DIMENSION.avi │
│  8 │ NCIS.Los.Angeles.S03E08.720p.HDTV.X264-DIMENSION.avi │
│  9 │ NCIS.Los.Angeles.S03E09.720p.HDTV.X264-DIMENSION.avi │
│ 10 │ NCIS.Los.Angeles.S03E10.720p.HDTV.X264-DIMENSION.avi │
└────┴──────────────────────────────────────────────────────┘
Continue to download? [y/n] y
Downloaded 10 subscriptions
```

그래서 뭐 `App::Zamakist` 라는 모듈을 사용하면 뭐 이렇게 자막을 한꺼번에 다운받을 수 있다 뭐 이런 거죠.

``` bash
$ ls -la
total 1592
drwxr-xr-x  22 jeen  staff    748  8  9 13:46 .
drwxr-xr-x+ 52 jeen  staff   1768  8  9 13:46 ..
-rw-r--r--   1 jeen  staff      0  8  9 13:45 NCIS.Los.Angeles.S03E01.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  63779  8  9 13:46 NCIS.Los.Angeles.S03E01.720p.HDTV.X264-DIMENSION.smi
-rw-r--r--   1 jeen  staff      0  8  9 13:45 NCIS.Los.Angeles.S03E02.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  75041  8  9 13:46 NCIS.Los.Angeles.S03E02.720p.HDTV.X264-DIMENSION.smi
-rw-r--r--   1 jeen  staff      0  8  9 13:45 NCIS.Los.Angeles.S03E03.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  84915  8  9 13:46 NCIS.Los.Angeles.S03E03.720p.HDTV.X264-DIMENSION.smi
-rw-r--r--   1 jeen  staff      0  8  9 13:45 NCIS.Los.Angeles.S03E04.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  78550  8  9 13:46 NCIS.Los.Angeles.S03E04.720p.HDTV.X264-DIMENSION.smi
-rw-r--r--   1 jeen  staff      0  8  9 13:45 NCIS.Los.Angeles.S03E05.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  94809  8  9 13:46 NCIS.Los.Angeles.S03E05.720p.HDTV.X264-DIMENSION.smi
-rw-r--r--   1 jeen  staff      0  8  9 13:45 NCIS.Los.Angeles.S03E06.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  98759  8  9 13:46 NCIS.Los.Angeles.S03E06.720p.HDTV.X264-DIMENSION.smi
-rw-r--r--   1 jeen  staff      0  8  9 13:45 NCIS.Los.Angeles.S03E07.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  76016  8  9 13:46 NCIS.Los.Angeles.S03E07.720p.HDTV.X264-DIMENSION.smi
-rw-r--r--   1 jeen  staff      0  8  9 13:45 NCIS.Los.Angeles.S03E08.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  94320  8  9 13:46 NCIS.Los.Angeles.S03E08.720p.HDTV.X264-DIMENSION.smi
-rw-r--r--   1 jeen  staff      0  8  9 11:44 NCIS.Los.Angeles.S03E09.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  69537  8  9 13:46 NCIS.Los.Angeles.S03E09.720p.HDTV.X264-DIMENSION.smi
-rw-r--r--   1 jeen  staff      0  8  9 09:29 NCIS.Los.Angeles.S03E10.720p.HDTV.X264-DIMENSION.avi
-rw-r--r--   1 jeen  staff  55905  8  9 13:46 NCIS.Los.Angeles.S03E10.720p.HDTV.X264-DIMENSION.smi
```

뭐 일단 CPAN 에 올릴 생각도 없는 토이모듈 겸 생활용품 모듈이라서…

아 물론 자막검색은 GOM 자막을 애용하고 있습니다.

검색방식은 뭐 파일이름에서 확장자만 떼서 검색날려서 뭐 여차저차해서 파일다운로드 하는 링크 빼서 일괄 다운로드 하는 것이죠.

이제 시작하려는 미드가 있는데 자막이 없다면 이거 한번만 돌려주면 앞으로 자막찾는 수고없어지겠습니다. :-)

