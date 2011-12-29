---
layout: post
title: "Perl - Email Send"
date: 2011-12-29 17:45
comments: true
categories: [perl,email,rjbs,attachments]
---

  회사 선배이신 [@mintegrals][twitter-mintegrals] 께서 최근에 사내 버그질라에 올려놓은 내용을 보고 약간 코드를 수정해봤습니다.
  내용인즉슨, 파일 첨부한 메일을 어떻게 보낼 수 있냐라는 것이었지요.
  기존에 써놨던 메일송신을 하는 모듈이 파일첨부를 고려하지 않았던 것이었는데, 그런 상황등을 고려해서 좀 더 수정해봤습니다.

### MyApp::Mail
  
``` perl
package MyApp::Mail;
use Moose;
use namespace::autoclean;
use Email::MIME;
use Email::MIME::Creator;
use Email::Sender::Simple 'sendmail';
use Email::Sender::Transport::SMTP;
use Encode;
use MIME::Types ();

has to => (
    is      => 'ro',
    isa     => 'Str',
    default => sub {
        Encode::encode( "MIME-Header", $_[0] );
    },
    required => 1,
);

has from => (
    is      => 'ro',
    isa     => 'Str',
    default => sub {
        Encode::encode( "MIME-Header", $_[0] );
    },
    required => 1,
);

has subject => (
    is      => 'ro',
    isa     => 'Str',
    default => sub {
        Encode::encode( "MIME-Header", $_[0] );
    },
    required => 1,
);

has body => (
    is  => 'ro',
    isa => 'Str',
);

has transport => (
    is  => 'ro',
    isa => 'HashRef'
);

has attachments => (
    is => 'ro',
    isa => 'ArrayRef[Path::Class::File]',
    default => sub { [] },
);

sub _get_parts {
    my $self = shift;

    my @parts;
    push @parts, 
        Email::MIME->create(
            attributes => {
                content_type => 'text/plain',
                charset      => 'utf8',
            },
            body => Encode::encode_utf8($self->body),
        );

    for my $attachment (@{ $self->attachments }) {
        confess "File Not Found : $attachment" unless -f $attachment;
        my ($mime_type, $encoding) = MIME::Types::by_suffix($attachment->basename);
        $mime_type ||= 'multipart/mixed';
        my $file_body = $attachment->slurp;
        push @parts, Email::MIME->create(
            attributes => {
                content_type => $mime_type,
                name         => $attachment->basename,
                filename     => $attachment->basename,
                encoding     => 'base64',
                disposition  => 'attachment',
            },
            body => $file_body,
        );
    }
    \@parts;
}

sub send {
    my $self = shift;

    my $opt = {};
    if ($self->transport) {
        $opt->{transport} = Email::Sender::Transport::SMTP->new( $self->transport );
    }

    my $email = Email::MIME->create(
        header => [
            From    => $self->from,
            To      => $self->to,
            Subject => $self->subject
        ],
        parts => $self->_get_parts, 
    );
    sendmail($email, $opt);
}

__PACKAGE__->meta->make_immutable;

1;
```

  파일첨부를 위한 `attachments` 접근자를 추가로 지정합니다. 첨부파일은 하나가 아닌 여러개가 가능하기에 `ArrayRef` 를 기본형식으로 합니다. 각 요소는 반드시 `Path::Class::File` 로 받도록 했습니다.
  `MIME::Types` 로 첨부파일의 확장자를 통해서 MIME-TYPE 을 뽑아내도록 하고, 알 수 없다면 `multipart/mixed` 로 지정했습니다.
  `transport` 레이어를 별도로 지정할 수 있으며, `transport` 의 지정이 없을 경우는 당연히 로컬호스트의 `smtp` 를 이용하도록 합니다.

### Using MyApp::Mail

  위의 `MyApp::Mail` 을 이용하기 위해서 아래와 같이 `sendmail.pl` 이라는 스크립트를 준비했습니다.
  
``` perl
# sendmail.pl
use strict;
use warnings;
use MyApp::Mail;
use Path::Class::File;

my $to    = 'your@email.com';
my $from  = 'my@email.com';
my $subject = 'MailSender Test Subject';
my $body    = 'MailSender Test Body';

my $mailer = MyApp::Mail->new(
    to => $to,
    from => $from,
    subject => $subject,
    body    => $body,
    attachments => [
      Path::Class::File->new('test.doc'),
      Path::Class::File->new('text.pdf')
    ],
    transport => {
        host => 'smtp.gmail.com',
        port => 465,
        sasl_username => 'YOUR_GOOGLE EMAIL',
        sasl_password => 'YOUR_GOOGLE_PASS',
        ssl  => 1,
    },
);

$mailer->send;
```

  위에서 언급한 `transport` 레이어를 정의해서 지메일의 `smtp` 를 사용하도록 합니다. 이를 위해서 물론 Google 계정정보를 입력할 필요가 있습니다.

### How to Test?

  [Email::Sender::Manual::QuickStart][cpan-email-sender-quickstart] 문서를 확인 해보면 Testing 관련 내용이 나옵니다.

``` perl
  use Test::More;
  BEGIN { $ENV{EMAIL_SENDER_TRANSPORT} = 'Test' }
  use YourCode;

  YourCode->run;

  my @deliveries = Email::Sender::Simple->default_transport->deliveries;
```

  위와같은 샘플코드가 제시되어 있는데,

``` perl
BEGIN { $ENV{EMAIL_SENDER_TRANSPORT} = 'Test' }
```

 가 지정되면, 실제로 메일 송신이 이뤄지지 않습니다.
 
``` perl
  my @deliveries = Email::Sender::Simple->default_transport->deliveries;
```

  에서 그동안 송신한 메일건수당 성공실패를 판별할 수 있고, 작성된 메일 내용또한 확인이 가능합니다. 테스트코드를 작성함에 있어서 위의 두 부분이 절대적으로 필요합니다.
  위처럼 테스트 코드를 작성할 시에는 실제로 메일 송신을 하지 않았기에 `transport` 레이어의 발생하는 어떤 불상사는 감지할 수 없습니다.
  
### Conclusion

  * [Perl 로 메일보내기 ... SSMTP 와 MIME::Lite 를 사용해서!][jeen-mime-lite-post]

  이전에 메일보내기 관련해서 `SSMTP` + `MIME::Lite` 를 사용하는 그런 코드를 2008년쯤에 썼었네요. 부끄럽습니다. 2009년부터는 아마 `MIME::Lite` 를 안썼던 것 같네요.
  
  뭐 당장 필요로 하는 부분만 구현을 한 것일 뿐인지라, 차후에 다른 어떤 요구사항이 있다면 그에 맞춰서 다시 한번 소개를 해볼까 합니다.

[twitter-mintegrals]:http://twitter.com/mintegrals
[jeen-mime-lite-post]:http://jeen.tistory.com/entry/Perl-Perl-%EB%A1%9C-%EB%A9%94%EC%9D%BC%EB%B3%B4%EB%82%B4%EA%B8%B0-SSMTP-%EC%99%80-MIMELite-%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%84%9C
[cpan-email-sender-quickstart]: http://search.cpan.org/~rjbs/Email-Sender-0.110001/lib/Email/Sender/Manual/QuickStart.pm#Testing