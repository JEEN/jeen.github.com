---
layout: post
title: "Using Plack::Middleware::OAuth"
date: 2011-12-27 13:24
comments: true
categories: [perl,psgi,oauth,middleware,c9s]
---
  
### Before Using Plack::Middleware::OAuth

  사실은 [Plack::Middleware::OAuth][cpan-pmoauth] 를 사용하기 이전에는 OAuth 관련 callback 처리등을 직접 파라메터를 확인해가면서 해당 액션을 처리했었습니다. 그게 P::M::OAuth 를 사용해서 각 OAuth Provider 들을 좀 더 유연하게 다룰 수 있게 되지 않았나 합니다.
  
``` perl
    sub redirect_to_foursquare {
        my $self = shift;

        # redirect to foursquare authentication url
        $self->response->redirect("https://foursquare.com/oauth2/authenticate?client_id=$client_id&response_type=code&redirect_uri=$callback_url");
    }

  sub receive_token_foursquare {
      my $self = shift;
      
      my $res = Furl->new->get("https://foursquare.com/oauth2/access_token?client_id=$client_id&client_secret=$client_secret&grant_type=authorization_code&redirect_uri=$redirect_url&code=".$self->request->param('code'));
      ...
  }
```

  예, 뭐 위처럼 OAuth 인증시에 `client_id` 랑 `client_secret` 그리고 `grant_type` 등 여러가지 파라메터의 지정이나 인증 URL 등등이 Provider 마다 다르고 이러니 뭐 일일이 Provider 마다 매번 어플리케이션 단에서 코드를 써나가는 것이 참 불쾌하기 마련이었습니다.
  나름 플러거블하게 만든다고 해봤지만 역시 이런 부분은 Middleware 단에서 처리하는 게 좋지 않을까 생각하고 있었지요.
  그런의미에서 P::M::OAuth 를 사용하게 되었습니다.
    
### Custom Provider

  P::M::OAuth 는 OAuth v1/v2 를 지원합니다. OAuth 버젼마다 파라메터가 바뀌기 때문에 사용하고자 하는 OAuth Provider 가 어떤 버젼을 지원하는 지 확인해봐야 합니다.
   P::M::OAuth 는 기본적으로 *Github*, *Twitter*, *Facebook*, *Live*, *Google* 의 다섯가지 OAuth Provider 를 지원합니다.

  저는 *FourSquare* 의 OAuth 를 사용하려고 하는 데, 기본적으로 제공해주지 않으니, 약간 손질을 해줘야 되겠죠.
  
``` perl
package Plack::Middleware::OAuth::Foursquare;
use strict;
use warnings;

sub config {
    +{
        version          => 2,
        authorize_url    => 'https://foursquare.com/oauth2/authenticate',
        access_token_url => 'https://foursquare.com/oauth2/access_token',
        response_type    => 'code',
        grant_type       => 'authorization_code',
    };
}

1;
```

  위처럼 `Plack::Middleware::OAuth::Foursquare` 와,

``` perl
package Plack::Middleware::OAuth::UserInfo::Foursquare;
use strict;
use warnings;
use parent qw(Plack::Middleware::OAuth::UserInfo);
use LWP::UserAgent;
use JSON;

sub create_handle {}

sub query {
    my $self = shift;

    my $uri = URI->new('https://api.foursquare.com/v2/users/self');
    $uri->query_form( oauth_token => $self->token->access_token );
    my $res = LWP::UserAgent->new->get($uri);
    my $body = $res->decoded_content;
    return unless $body;

    my $obj = decode_json($body) || {};
    return $obj->{response}->{user};
}

1;
```

  `Plack::Middleware::OAuth::UserInfo::Foursquare` 를 추가합니다.

  이렇게 쉽게 OAuth Provider 마다 해당 네임스페이스 맞는 모듈을 추가해줍니다.
 
### MyApp

``` perl
# ...
my $app = MyApp->psgi_app;
builder {
    mount '/oauth' => builder {
        enable 'OAuth',
            on_success => sub {
                my ($mw, $token) = @_;
                
                # get Foursquare's UserInfo
                my $info = Plack::Middleware::OAuth::UserInfo->new( config => $mw->config, token => $token );
                
                if ($token->is_provider('Foursquare')) {
                return $mw->redirect('/');  
              }
              on_error => sub { #... },
              providers => {
                  'Foursquare' => {
                      client_id => 'YOUR CLIENT_ID',
                      client_secret => 'YOUR CLIENT_SECRET',
                  },
              };
            }
    };
    mount '/' => $app;
};
```

  대충 위처럼 사용을 합니다.
  좀 더 자세한 예제는 [P::M::OAuth 모듈의 예제 psgi 파일][github-pmoauth-app-psgi] 또는 , 이 모듈을 사용한 제 프로젝트 [Bobby-Akawa 의 특정 모듈][github-bobby-lib-bobby-pm]에서 찾아볼 수 있습니다.

### Conclusion

  이로써 간략하게나마 Plack::Middleware::OAuth 를 이용해서 OAuth 인증을 Plack Middleware 단에서 구현할 수 있게 되었습니다. 조금 설명이 빈약해서 차후에 좀 더 구현단으로 들어가서 좀 더 깊게 파고들어가 볼까 합니다.
  
[cpan-pmoauth]:http://search.cpan.org/perldoc?Plack::Middleware::OAuth
[github-pmoauth-app-psgi]:https://github.com/c9s/Plack-Middleware-OAuth/blob/develop/eg/app.psgi
[github-bobby-lib-bobby-pm]:https://github.com/JEEN/Bobby-Akawa/blob/develop/lib/Bobby.pm#L46