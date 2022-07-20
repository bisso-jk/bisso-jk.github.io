---
title: github blog 사용법 + Jekyll 적용
author: Bisso
date: 2021-09-12 10:46:00 +0900
categories: [Blogging, GitBlog]
tags: [writing]
---

## Github 블로그 만드는 방법

1. Repository 생성
 - Repository 이름 -> [username].github.io 설정 ( username : github username )

![git_create 이미지](/assets/img/blog_image/git_repository_create.png)

2. git clone<br>

```console
 git clone (repository_url)
 
 cd [username].github.io
 touch index.html ( OS가 windows인 경우는 파일 생성해주시면 됩니다 )
```

3. index.html 파일 수정
 - 아래 이미지 처럼 수정

![git_create 이미지](/assets/img/blog_image/index.png)

4. git push
 - 변경된 내용 들을 repository에 push

```console
git add .
git commit -m "Initial Commit"
git push origin [브랜치이름]
```

5. git blog 확인
 - [username].github.io 접속
 - 화면 확인
 
---

![git_create 이미지](/assets/img/blog_image/github_blog.png)

---

아무것도 없는 화면이지만 <br>
Jekyll 테마를 적용해서 더 이쁘게 만들 수 있어요 <br>

Jekyll 테마를 적용해볼게요 <br><br>


## Jekyll 테마 적용

1. Jekyll install

```console
gem install jekyll bundler
```

실행하여 Jekyll 설치해주세요

Mac에서 실행 시 아래 에러 발생 시 system Ruby를 쓰고 있어 권한이 없어 설치 오류 발생. <br>
Ruby를 설치해주어야 합니다.

```console
$ gem install bundler
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
```

### 오류 조치 방법


- rbenv 설치

```console
brew install rbenv ruby-build
rbenv versions -> 잘 설치 되어있는지 확인
* system (set by /Users/jk-pc/.rbenv/version) 
  2.7.4
```

system ruby로 세팅 되어 있는 것을 확인하실 수 있습니다.

- 설치 가능 버전 확인

```console
rbenv install -l

2.6.8
2.7.4
3.0.2
jruby-9.2.19.0
mruby-3.0.0
rbx-5.0
truffleruby-21.2.0.1
truffleruby+graalvm-21.2.0
```

저는 3.0.2 버전을 설치했다가 제가 사용하고 싶은 테마가 3.0.0 이상을 지원하지 않아 <br>
2.7.4 버전을 설치했습니다. ^^

- Ruby 설치 및 버전 확인

```console
rbenv install [설치할 버전]

rbenv versions
* system (set by /Users/jk-pc/.rbenv/version) 
  2.7.4
```  

- global 버전 변경

```console
rbenv global 2.7.4

  system
* 2.7.4 (set by /Users/jk-pc/.rbenv/version)

```

system ruby 말고 저의 경우엔 2.7.4 를 set 하고 있습니다.

마지막으로 rbenv path 추가하기 위해 <br>
shell 설정 파일 변경을 해줘야 합니다.<br>

```console
vi ~/.zshrc 또는 (자신의 쉘 파일을 열어주세요)

아래 설정 추가

[[ -d ~/.rbenv  ]] && \
  export PATH=${HOME}/.rbenv/bin:${PATH} && \
  eval "$(rbenv init -)"
  
적용

source ~/.zshrc 또는 (자신이 적용한 쉘)

gem install jekyll bundler
```

하면 jekyll이 성공적으로 설치되는 것을 보실 수 있을겁니다 ^^ <br><br>

테스트 화면으로 썼던 index.html을 제거해주신 후, <br>
[username].github.io 위치에서 아래 커맨드를 입력해주세요.

```console
jekyll new .
```

jekyll이 생성되었어요. 그럼 이제 install을 해볼까요? <br>
아래 커맨드를 입력해주면 jekyll을 실행할 수 있어요

```console
bundle install

bundle exec jekyll serve

```

![jekyll 실행 이미지](/assets/img/blog_image/jekyll_execute.png)

실행 후 화면에 표기된 127.0.0.1:4000 으로 접속하면 <br>
아래 이미지처럼 생성된 git blog를 보실 수 있습니다.

![jekyll 웹 이미지](/assets/img/blog_image/jekyll_view.png)

이상 git blog 생성까지 해보았습니다. <br>
다음 편에서는 jekyll theme를 적용하는 포스팅을 해볼게요~ <br>
감사합니다.