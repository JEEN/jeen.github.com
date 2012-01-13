---
layout: post
title: "Picasa Upload Script II"
date: 2012-01-13 18:36
comments: true
categories: [perl,picasa]
---

``` perl picasa-uploader.pl
#!/usr/bin/env perl
package PicasaUploader;
use Any::Moose;
use namespace::autoclean;
use Config::Pit;
use LWP::UserAgent;
use Net::Google::AuthSub;
use MIME::Types ();

has user_id => (
    is      => 'ro',
    default => 'default',
);

has album_id => (
    is      => 'ro',
    default => 'default',
);

has files => (
    is      => 'ro',
    isa     => 'ArrayRef',
    default => sub { [] }
);

has google_auth => (
    is      => 'rw',
    isa     => 'Net::Google::AuthSub',
    default => sub {
        Net::Google::AuthSub->new(
            service => 'lh2',
            source  => 'my-picasauploader-0.1'
        );
    }
);

has '_ua' => (
    is      => 'rw',
    isa     => 'LWP::UserAgent',
    default => sub {
        LWP::UserAgent->new( cookie_jar => {} ); 
    }
);

has 'config' => (
    is          => 'ro',
    isa         => 'HashRef',
    lazy_build  => 1,
);

sub BUILD {
    my $self = shift;
    $self->auth;
    return 1;
}

sub _build_config {
    Config::Pit::pit_get('google.com', require => {
       username => 'your username on google.com',
       password => 'your password on google.com' 
    });    
}

sub auth {
    my $self = shift;

    my $res = $self->google_auth->login($self->config->{username}, $self->config->{password});
    die "Login Failed: ". $res->error unless $res->is_success;
}

sub upload_files {
    my $self = shift;

    for my $file_path (@{ $self->files }) {
        my $link = $self->upload_file($file_path);
        print $link."\n";
    }
}

sub upload_file {
    my ($self, $file_path) = @_;

    my $file = Path::Class::File->new($file_path);
    die "File Not Found : $file" unless -f $file;

    my $post_url = sprintf 'https://picasaweb.google.com/data/feed/api/user/%s/albumid/%s', $self->user_id, $self->album_id;

    my ($mime_type, $encoding) = MIME::Types::by_suffix($file->basename);
    my $content = $file->slurp;
    my $res = $self->_ua->post($post_url,
        %{ $self->google_auth->auth_params },
        Content_Type   => $mime_type,
        Slug           => $file->basename,
        Content        => $content,  
    );
    
    die 'UPLOAD Failed : '.$res->status_line unless $res->is_success;
    my ($link) = ($res->decoded_content=~/content type.*?src='(.*?)'/gsm);
    $link;
}

__PACKAGE__->meta->make_immutable;

package main;
  
my $uploader = PicasaUploader->new( files => \@ARGV );
$uploader->upload_files;
```

  [@aanoaa 님의 글][blog-aanoaa-picasa-upload]에서 Picasa Uploader 스크립트를 소개했었는데요. `PicasaUploader` 라고 package 로 정의해서 쓰도록 했습니다. 
  거기에 [Config::Pit][cpan-config-pit] 을 써서 계정정보를 다루도록 했구요.
  심심해서 그냥 약간 개량해봤습니다. 

[cpan-config-pit]:https://metacpan.org/module/Config::Pit
[blog-aanoaa-picasa-upload]:http://aanoaa.github.com/blog/2012/01/03/picasa-upload-script/