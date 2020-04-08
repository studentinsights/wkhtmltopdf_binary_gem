# fork note:
hello!
This fork removes binaries other than Mac OS and Ubuntu 18.04, so that it can be used for projects that run on OSX for local development and Ubuntu 18 in production, and not carry all the other binaries around.  This is a problem for Heroku, where there's a size limit for deployed slugs.

-------------------------------

# Installation and usage

Install in your Gemfile as usual

```ruby
gem 'wkhtmltopdf-binary'
```

In many environments, this is all you need to do. This gem installs a binary stub that tries to determine which wkhtmltopdf binary will work on your system, and point to the packaged binary that most closely matches.

In some environments, invoking this binary will result in an error, saying the needed permissions are not available.
This is because `wkhtmltopdf-binary` ships with gzipped binaries for many platforms, and then picks the appropriate one
upon first use and unzips it into the same directory. So if your ruby gem binaries are installed here:

    /usr/lib/ruby/versions/2.6/bin/

The various wkhtmltopdf-binaries will be installed here:

    /usr/lib/ruby/versions/2.6/lib/ruby/gems/2.6.0/gems/wkhtmltopdf-binary-0.12.5.1/bin/

Giving write access whatever user is running your program (e.g. web server, background job processor),
e.g. your own personal user in a dev environment, will fix the problem. After the binary is uncompressed, write access can be revoked again if desired.

    chmod -R 777 /usr/lib/ruby/versions/2.6/lib/ruby/gems/2.6.0/gems/wkhtmltopdf-binary-0.12.5.1/bin/

# Gem Development

## Extracting binaries

Hints for extracting binaries from https://wkhtmltopdf.org/downloads.html (dpkg and rpm2cpio is available on Homebrew).

Debian/Ubuntu

    dpkg -x wkhtmltox_0.12.5-1.trusty_amd64.deb .

CentOS

    rpm2cpio wkhtmltox-0.12.5-1.centos7.x86_64.rpm | cpio -idmv

macOS

    xar -xf wkhtmltox-0.12.5-1.macos-cocoa.pkg
    cat Payload | gunzip -dc | cpio -i

## Compression

Binaries should be compressed with `gzip --best` after extracting. The matching binary will be extracted on first
execution of `bin/wkhtmltopdf`.

## Testing with Docker

Make sure you have Docker and Docker Compose installed (see https://docs.docker.com/compose/install/ for more
information).

There are Dockerfiles for the supported Linux based distributions under `.docker`. You can build them all with
`docker-compose build` and run each individually with e.g. `docker-compose run ubuntu_18.04`.

There also is a rudimentary minitest test that simply invokes `docker-compose run` for each distribution and
expects to see the output of `wkhtmltopdf --version`. Just run `rake` to run it. 

You can clean up after testing with `docker-compose down --rmi all`.
