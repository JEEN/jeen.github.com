---
layout: post
title: "HTTPS-SSL-LWP-SOAP and Perl"
date: 2012-07-02 15:23
comments: true
categories: [ perl ]
---

> tl;dr: I hate Java and SOAP, use Perl


 지난 주에 고객으로부터 요구를 받았는데, DB특정 항목에 대치되는 어떤 값을 가지고 모 API를 통해서 결과를 받아서 뿌려주는 간단한 것이었습니다.

 그 API 제공회사에서 날라온 API 명세서는 뭘 해도 자바였었죠. 샘플이라고 널려놓은 두세네개 코드도 전부 자바였습니다. 해야하는 건 결국 뭐 SOAP 정도였죠.

 명세서에는 Axis 라는 것을 깔아서 뭐 붙여서 어떻게 뭐 wsdl 붙이고 어쩌고 복잡하게 써놨는데...

 결론부터 말하면 endpoint 와 보낼 값들만 필요하면 자바가 무슨 소용이냐 싶어서 집어냈습니다.


``` perl
use strict;
use warnings;
use LWP::UserAgent;
use Data::Dumper;

my $q = {
<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <soapenv:Body>
 .........
 </soapenv:Body>
};

my $ua = LWP::UserAgent->new;
my $res = $ua->post('https://........',
  'Content-Type' => 'text/xml; charset=utf-8',
  Content => $q,
);

print Dumper $res->content;
```

 우선 SOAP XML 내용 및 endpoint 는 해당업체 비밀자료이기에 표시하지 않습니다.

 위의 코드의 결과는

``` bash
Can\'t connect to demo-asp.ysdasp-service.ne.jp:443 (certificate verify failed)

LWP::Protocol::https::Socket: SSL connect attempt failed with unknown error error:14090086:SSL routines:SSL3_GET_SERVER_CERTIFICATE:certificate verify failed at .../perl-5.14.2-llvm/lib/site_perl/5.14.2/LWP/Protocol/http.pm line 51.
```

이라고 나온 것이었습니다. 유효하지 않은 인증서라는 데, 브라우저에서는 그냥 무시하고 지나가버리면 되는 데, Perl 의 경우에서는 어떻게 할까... 결론은 구글해봤습니다.

``` perl
$ENV{'PERL_LWP_SSL_VERIFY_HOSTNAME'} = 0;
```

 `PERL_LWP_SSL_VERIFY_HOSTNAME` 이라는 환경변수의 값을 0으로 지정함으로써 LWP 가 인증서의 유효성 체크를 생략시켜버립니다.

 이제는 되겠지 하고 실행을 시켜보니...

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <soapenv:Body>
  <soapenv:Fault>
   <faultcode xmlns:ns1="http://xml.apache.org/axis/">ns1:Client.NoSOAPAction</faultcode>
   <faultstring>no SOAPAction header!</faultstring>
   <detail/>
  </soapenv:Fault>
 </soapenv:Body>
</soapenv:Envelope>
```

 이라는 결과가 나옵니다. `SOAPAction` 이라는 헤더가 없다고 하는데... SOAP 통신에는 SOAPAction 이라는 헤더를 넣는다고... 그냥 해보니 `SOAPAction` 에 그냥 빈값이라도 헤더이름만 넣고 돌려보면 되겠구나 라는 생각이 들었습니다.

``` perl
my $res = $ua->post('https://........',
  'Content-Type' => 'text/xml; charset=utf-8',
  SOAPAction => '""',
  Content => $q,
);
```

 POST 시에 `SOAPAction` 헤더를 넣어주고 다시 돌려보았습니다.

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <soapenv:Body>
  <soapenv:Fault>
   <faultcode>soapenv:Server.userException</faultcode>
   <faultstring>org.xml.sax.SAXParseException: The processing instruction target matching &quot;[xX][mM][lL]&quot; is not allowed.</faultstring>
   <detail/>
  </soapenv:Fault>
 </soapenv:Body>
</soapenv:Envelope>
```

 다시 해당 `faultstring` 으로 검색을 해본 결과, 이유는 <?xml ...?> 앞에 빈 공백값이 있으면 안된다는 것이었습니다.

``` perl
my $q = q{<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 <soapenv:Body>
 .........
 </soapenv:Body>
};
```

 맨 위의 코드에서 `q{` 다음에 개행을 넣은 게 문제가 되어서 다시 지우고 돌려본 결과 좌르르르륵 펴쳐져 나오는 기나긴 XML의 향연...

 아무튼 이걸로 API 제공업체에서 JAVA 를 기준으로 작성된 길고 긴 명세서들과 설치요령 등등의 문서들을 제쳐두고 해야할 일에만 집중할 수 있게 되었습니다.
