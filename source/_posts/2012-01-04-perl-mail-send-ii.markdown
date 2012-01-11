---
layout: post
title: "Perl - Mail Send II"
date: 2012-01-04 18:26
comments: true
categories: [perl,mail] 
---

* [Perl - Mail Send][myentry-mail]

  이전에 Perl 에서 Mail Send 관련해서 한번 깨작거린 적이 있습니다.

  오늘은 이에 대해서 추가적인 수정이 필요한 부분이 있어서, `MyApp::Mail` 을 조금 더 건드려 봤습니다.

``` perl
package MyApp::Mail;
use Moose;
use Moose::Util::TypeConstraints;
use namespace::autoclean;
use Email::MIME;
use Email::Sender::Simple 'sendmail';
use Email::Sender::Transport::SMTP;
use Encode;
use MIME::Types ();

subtype 'MailAddresses' => as 'Str';

coerce 'MailAddresses',
    from 'ArrayRef',
        via { join(", ", (map { Encode::encode( "MIME-Header", $_ ) } @{ $_ })) };
     from 'Str',
         via { Encode::encode( "MIME-Header", $_ ) };

has to => (
    is      => 'ro',
    isa     => 'MailAddresses',
    default => sub {
        Encode::encode( "MIME-Header", $_[0] );
    },
    required => 1,
    coerce    => 1,
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

  일단 수정을 필요로 한 부분은, 똑같은 메일 내용을 둘 이상에게  보내야 할 경우입니다. 일반적으로 업체에서 보내는 메일의 헤더를 보면 `To` 에 본인만 들어가 있을 겁니다. 이게 일반적이죠.
  
  하지만 이번에는 사내 시스템 관련 리포트를 보내는 것이고, 특정 다수에게 보내며, 메일링이 없다는 전제하에서 움직였습니다.

변경된 부분은,

``` diff
package MyApp::Mail;
use Moose;
+use Moose::Util::TypeConstraints;
use namespace::autoclean;
@@ -9, 13 +10, 22 @@ use Email::Sender::Transport::SMTP;
use Encode;
use MIME::Types ();

+subtype 'MailAddresses' => as 'Str';

+coerce 'MailAddresses',
+   from 'ArrayRef',
+        via { join(", ", (map { Encode::encode( "MIME-Header", $_ ) } @{ $_ })) };
+     from 'Str',
+         via { Encode::encode( "MIME-Header", $_ ) };
+
has to => (
    is      => 'ro',
-    isa     => 'Str',
+    isa     => 'MailAddresses',
    default => sub {
        Encode::encode( "MIME-Header", $_[0] );
    },
    required => 1,
+    coerce    => 1,
);
```

  입니다. subtype 을 지정하기 위해서 `Moose::Util::TypeConstraints` 를 use 할 필요가 있구요. `To` 헤더의 형식을 `MailAddresses` 라는 subtype 으로 받도록 합니다. 그냥 문자열 값이 들어오든 Array Reference 가 들어오든 그에 맞춰서 변환해주도록 했구요.

  이전 글에서 간략하게 Mail 테스트 방법에 대해서 소개했는데, 현재 위처럼 수정한 다음에 아래와 같이 테스트코드를 작성해서 문제없이 작동하는 것을 확인할 수 있었습니다.

``` perl send-mail.t
use strict;
use warnings;
use Test::Most;
use_ok 'MyApp::Mail';
use Path::Class::File;

BEGIN {
    $ENV{EMAIL_SENDER_TRANSPORT} = 'Test';
};

my %data_set = (
    to      => 'your@email.com',
    from    => 'my@email.com',
    subject => 'MailSender Test Subject',
    body    => 'MailSender Test Body',
);

# Send a mail w/ attachments
my $mailer = MyApp::Mail->new(
    %data_set,
    attachments => [ 
        Path::Class::File->new('somefile.zip'),
        Path::Class::File->new('somefile.ee')
    ],
);

$mailer->send;

# Send a mail without attachments
my $mailer2 = MyApp::Mail->new(%data_set);

$mailer2->send;

# Send a mail with Multiple Recipients
my $mailer3 = MyApp::Mail->new(
    %data_set,
    to => [ 'your2@email.com', 'anotheremail@gmail.com' ],
);

$mailer3->send;

# Just string
my $mailer4 = MyApp::Mail->new(
  %data_set,
  to => 'mail-1@mail.com, mail-2@mail.com, mail-3@mail.com',
);

$mailer4->send;

my $deliveries = Email::Sender::Simple->default_transport->deliveries;
for my $deliv (@{ $deliveries }) {
  my $sent_addresses = join ", ", @{ $deliv->{successes} };
  ok $deliv->{successes}, 'Mail sent to '. $sent_addresses;
}
done_testing();
```

  물론 `$deliveries` 변수를 덤프해보면 각각의 메일헤더들이 존재하고 좀 더 항목당 세밀하게 테스트를  수행할 수 있겠지만, 여기서는 단순하게 이정도로 그치도록 합니다.
 
  사실은 뭐 `To` 안에 여러개의 메일이 들어간다는 전제로 ArrayRef 를 넣었지만, 사실 Str 을 받기에 `To` 에 대해서는 그냥 문자열로 `mail-1@mail.com, mail-2@mail.com, mail-3@mail.com` 으로 해도 가능합니다. 하지만 그냥 제 고집상 복수의 데이터셋이 들어갈 때는 왠지 ArrayRef 로 넣고 싶어졌을 뿐입니다. 존중받고 싶은 취향같은 것이죠.

[myentry-mail]:http://jeen.github.com/blog/2011/12/29/perl-email-send/