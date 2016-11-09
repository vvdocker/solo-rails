# vvdocker/solo-rails
+ centos7
+ use bundler
+ ruby version 2.2.5 2.3.0 ... 

## quick start
### build
```
docker build -t vvdocker-rails .
```
### run
```
docker run -d -p 3000:3000 vvdocker-rails
```

### attach
```
docker exec -it vvdocker-rails bash
```

## Dockerfile

```
FROM centos:7

# use utf-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# install common tools
RUN yum install -y git vim sudo tar wget
RUN yum install -y epel-release
RUN yum install -y gcc-c++ git glibc-headers libffi-devel libxml3 libxml2-devel libxslt libxslt-devel libyaml-devel make nodejs npm openssl-devel readline readline-devel sqlite-devel zlib zlib-devel

## Ruby
RUN cd /usr/local/src && \
  wget http://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.5.tar.gz && \
  tar zxvf ruby-2.2.5.tar.gz && \
  cd ruby-2.2.5 && \
  ./configure --disable-install-doc && \
  make && \
  make install

RUN yum install -y patch
RUN gem update --system

# crontab
#RUN yum install -y crontabs
#RUN cp -p  /usr/share/zoneinfo/Japan /etc/localtime

RUN yum -y install mysql-server mysql-devel

# [edit] rails repository
RUN git clone https://~~~~.git

# [edit] work dir or no such Gemfile error!!!!
WORKDIR /server

RUN gem install bundler
RUN gem install nokogiri -- --use-system-libraries=true --with-xml2-include=/usr/include/libxml2/
RUN bundle config build.nokogiri --use-system-libraries

RUN bundle install --path vendor/bundle
RUN bundle config --global path 'vendor/bundle'

# environment
#ADD env /server/.env
#ADD database.yml config/database.yml

# server start
EXPOSE  3000
CMD ["rails", "server", "-b", "0.0.0.0"]
```

