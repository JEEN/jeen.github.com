---
layout: post
title: "Carton - CPAN dependencies manager"
date: 2012-01-16 22:49
comments: true
categories:  [perl, cpan]
---

  Jenkins 이야기를 마저 쓸려고 했는 데, 그 사이사이에 사용된 것들부터 정리하고자 우선은 Carton 이야기 부터 먼저하고자 합니다.

  잘은 모르지만 Carton 은 Ruby 의 Bundler 에 영감을 받아서 만들어 진 것입니다. 어떤 Perl 로 만든 소프트웨어의 의존모듈의 관리가 편해집니다. :-) 일례로, 노트북에서 개발을 하고 이것을 서버에 올릴려고 할 때, 일일이 의존모듈 체크를 해야할 필요성이 있습니다. 그때마다 `cpan MODULE` 등으로 설치를 해나가야 한다면 여간 힘든 일이 아니죠.

  그런 상황에서 쉽게 문제를 해결할 수 있는 것이 `Carton` 이 아닐까 생각합니다.

### Carton 설치

  `Carton` 은 간단하게 `cpan` 으로 설치할 수 있습니다.
  
``` bash
$ cpanm carton
```

  위의 커맨드로 `Carton` 을 설치하면 `carton` 이라는 커맨드가 생깁니다.

### Carton 이용

  `Carton` 을 제대로 이용하려면, 해당 Perl App 의 의존모듈이 `Makefile.PL` 안에서 잘 관리되고 있어야 합니다.

``` perl Makefile.PL
use ExtUtils::MakeMaker;
WriteMakefile(
    'NAME'      => 'WebService::Aladdin',
    'AUTHOR'    => 'JEEN <jeen@perl.kr>',
    'VERSION_FROM' => 'lib/WebService/Aladdin.pm', # finds $VERSION
    'PREREQ_PM' => {
        Test::Base => 0,
        version    => 0,
        Class::Accessor::Fast => 0,
        LWP::UserAgent => 0,
        URI            => 0,
        Carp           => 0,
        XML::FeedPP    => 0
    },
);
```

  우선 예제로 제가 만든 `WebService::Aladdin` 모듈의 `Makefile.PL` 이 위와 같습니다. 이는 `WebService::Aladdin` 모듈에서 필요로 하는 의존모듈 등의 정보들을 나타내고 있습니다.  

``` bash
$ carton install
```

  `carton install` 을 통해서 `Makefile.PL` 에 정의된 의존모듈을 설치합니다. 이때 `Carton` 은 의존모듈의 의존트리까지도 설치해버립니다. 그러니 실제 의존모듈이 10개라도, 그 10개의 각각의 모듈의 의존모듈까지도 설치하기 때문에 실제로 설치되는 것은 10개 이상의 모듈들이 될 것입니다.

  아무튼 `carton install` 이 끝났다면 `carton.lock` 파일이 생성됩니다. 그리고 `local` 이라는 디렉토리의 아래에 의존모듈들이 설치됩니다.

``` text carton.lock
{
   "modules" : {
      "Algorithm::Diff" : {
         "dist" : "Algorithm-Diff-1.1902",
         "module" : "Algorithm::Diff",
         "mymeta" : {
            "abstract" : "unknown",
            "author" : [
               "unknown"
            ],
            "dynamic_config" : 0,
            "generated_by" : "ExtUtils::MakeMaker version 6.62, CPAN::Meta::Converter version 2.112621, CPAN::Meta::Converter version 2.112150",
            "license" : [
               "unknown"
            ],
            "meta-spec" : {
               "url" : "http://search.cpan.org/perldoc?CPAN::Meta::Spec",
               "version" : "2"
            },
            "name" : "Algorithm-Diff",
            "no_index" : {
               "directory" : [
                  "t",
                  "inc"
               ]
            },
            "prereqs" : {
               "build" : {
                  "requires" : {
                     "ExtUtils::MakeMaker" : 0
                  }
               },
               "configure" : {
                  "requires" : {
                     "ExtUtils::MakeMaker" : 0
                  }
               },
               "runtime" : {
                  "requires" : {}
               }
            },
            "release_status" : "stable",
            "version" : "1.1902"
         },
         "name" : "Algorithm::Diff",
         "pathname" : "T/TY/TYEMQ/Algorithm-Diff-1.1902.tar.gz",
         "provides" : {
            "Algorithm::Diff" : {
               "file" : "Algorithm/Diff.pm",
               "version" : "1.1902"
            },
            "Algorithm::DiffOld" : {
               "file" : "Algorithm/DiffOld.pm",
               "version" : "1.1"
            }
         },
         "version" : "1.1902"
      },
      ...
   }
}
```

  이후 `Makefile.PL` 에서 또다른 의존모듈이 추가되었을 시에, 또다시
  
``` bash
$ carton install
```

을 통해서 추가의존모듈을 설치할 수 있습니다. 이렇게 `carton install` 로 설치된 로컬 모듈들은

``` bash
$ carton list
IO-stringy-2.110
File-Listing-6.03
XML-FeedPP-0.43
CPAN-Meta-2.112621
...
```

  `carton list` 로 확인할 수 있습니다.
  
  그리고 `carton exec` 를 통해서 로컬모듈에 설치된 의존모듈을 참조해서 실행할 수 있습니다. 일례로 `Catalyst` 나 `Dancer` 로 만든 WebApp 들을 위에서 설명한 대로 `carton install` 한다음,
  
``` bash
$ carton exec -- plackup -a app.psgi -p 3000 --mode production
```

  이처럼 `carton exec` 를 통해서 실행할 수 있지요.
  
### Carton 을 이용하고서...

  우선은 Carton 을 이용함으로, 한 어플리케이션 당 하나의 모듈참조장소를 가지게해서 여러 어플리케이션이 혼잡한 경우에 발생할 수 있는 부작용을 예방할 수 있습니다. `Makefile.PL` 에서 해당 모듈의 버젼을 지정함으로 특정 버젼에 대해서만 의존을 가질 수 있기 때문에 더더욱 이런 면에서는 효과적입니다.

  그래서 저같은 경우는 오히려 서비스마다 그 서비스용 계정을 만들고 `perlbrew` 환경을 갖추는 것보다 가볍게 서비스용 계정은 하나만 가지며 각 서비스디렉토리마다 `Carton` 을 올리는 쪽으로 움직이고 있습니다. 그러니 굳이 시스템 펄을 사용하는 것이 큰 거부감을 느끼지 않게 되었네요.

### 그 외

  일단 `carton.lock` 에 기재된 의존모듈 정보가 많으면 많을수록 `carton install` 에 걸리는 시간은 더 오래걸리게 됩니다. 물론 Carton 이 기본적으로 외부미러(http://cpan.metacpan.org) 를 사용하고 있기에, 그럴 수도 있겠다 싶어서 코드를 한번 훑어봤는데, 마침 `PERL_CARTON_MIRROR` 라는 환경변수를 통해서 가까운 혹은 로컬 네트워크 안의 CPAN 미러를 지정해줌으로 많은 시간을 단축할 수 있지 않을까 생각해봅니다.

### Conclusion

  물론 Carton 은 현재 ALPHA 퀄리티라고 합니다. 버젼도 현재(2012-01-16) 로는 0.9.3 이고, 1.0 정식 릴리즈까지는 아직 시간이 남아있습니다. 몇몇 기능이 바뀔거라는 주의사항이 [Carton 모듈페이지][metacpan-carton] 에서 확인할 수 있습니다.

  좀 더 다양한 `Carton` 에 대한 이야기는 작년 [YAPC::Asia2011 의 Carton Talk Page][yapcasia-2011-carton] 에서도 확인하실 수 있습니다.

[metacpan-carton]:http://metacan.org/module/Carton
[yapcasia-2011-carton]:http://yapcasia.org/2011/talk/91