---
layout: post
title: M1 유저를 위한 Jekyll로 깃블로그 열기
date: 2022-04-14 20:37:00
description: deploy your personal github page with Jekyll
tags: blog jekyll mac m1 ruby
categories: learnings
---

<span style=" font-family: 'Gothic A1', sans-serif;font-size: 25px; font-weight: 500;">[M1 유저를 위한 Jeklly Gitblog 열기!]</span>

<p><span style="font-weight: 500;">M1 Mac 유저분들 중 Jekyll을 이용해 깃블로그를 처음 열고자 한다면 아래 준비사항을 미리 체크하자!</span><br><br>
정보도 가이드도 없이 혼자 이것 저것 해보려다가 장장 열흘을 소모하며 깨달은 것들이니<br> 
나처럼 M1 맥북도 처음, 개발도 처음, Github pages도 처음이라면 마음 급하게 먹지 말고<br>
준비사항을 모두 완료한 후 진행해보도록 하자.</p>

```
1. MacOS를 최신으로 업데이트 하기. 업데이트 후 꼭 재부팅 먼저!!
2. M1 유저의 경우 Terminal은 "Start with Roessta" 해제하기.
2-1. Finder/Applications/ 경로의 terminal 앱에서 우클릭 후 get info를 통해 해제 가능..
3. Xcode 설치 완료하기. (몇 시간이 걸릴지 모르는 고통스러운 과정이므로 인내심 필요! 미리 깔려있다면 Lucky.)
```

위 사항들이 모두 마무리 되었다면, 이제 순서대로 진행해보자.

```
1. Homebrew 설치
2. Ruby 와 Jekyll 설치
3. Jekyll theme fork하고 레포지토리 이름 바꾸기
4. 블로그 호스팅
```

<p style="font-weight:600; font-size: 1.2rem"><br><br>[STEP 1. Homebrew 설치하기]</p>
Terminal 앱을 켠후 아래 코드를 복사 붙여넣고 Enter를 누른다.
Homebrew는 macOS용 패키지 관리자이다.   
Homebrew를 통해 Appple 혹은 Linux 시스템에서 제공하지 않는 유용한 패키지를 다운받고 관리할 수 있다.

{% highlight terminal %}

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
{% endhighlight %}

설치가 완료되었다면, 터미널에 `which brew` 명령어를 통해 설치 경로를 확인한다.  
M1 유저의 경우 아래와 같이 나왔다면 정상적으로 설치 된 것이다.  
설치 후 `[command] + q`를 통해 터미널을 완전히 껐다 켜야 한다.

```s
/opt/homebrew/bin/brew
```

만약 설치 후 homebrew가 몇 가지 command를 실시하라고 할 수도 있는데,  
그럴 경우에만 아래의 코드들을 차례로 실행해주자.

{% highlight terminal %}
echo "eval $(/opt/homebrew/bin/brew shellenv)" >> ~/.zprofile
eval $(/opt/homebrew/bin/brew shellenv)
{% endhighlight %}

<p style="font-weight:600; font-size: 1.2rem"><br><br>[STEP 2. Ruby 와 Jekyll 설치하기]</p>

Ruby를 철치하기에 앞서 Ruby Manager를 설치해야 한다.  
Ruby Manager는 Ruby의 버전관리를 위한 것들로 여러 종류가 있다. RVM, rbenv, asdf, chruby 등등. 그러나 서로 호환이 되지 않아 한 가지만 설치해야 한다. Jekyll 깃블로그를 열고 싶은데 Ruby 설치에서만 여러번 좌절해본 결과, chruby라는 놈이 나를 ERROR의 굴레로부터 벗어나게 해주었기 때문에 본 포스팅은 chruby를 기준으로 한다.

그 전에 다른 ruby managers가 설치되어 있는지 확인해보자.

```s
rvm help
```

```s
rbenv help
```

```s
asdf --help
```

이 Jobobdev 블로그 주인처럼 자신이 초보라면, 깔끔하게 기존의 것들을 지우고 아래의 chruby 가이드를 따르길 권장한다.

<span style="font-weight:500; font-size: 1.1rem">아래의 명령어를 통해 chruby와 Ruby를 설치한다.</span>

```s
brew install chruby ruby-install
```

설치가 완료 되었다면 차례대로 아래의 명령어들을 실행해주자.

<span style="font-weight:500; font-size: 1.1rem">M1 Mac</span>

```c
echo "source /opt/homebrew/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source /opt/homebrew/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.1.1" >> ~/.zshrc
```

<span style="font-weight:500; font-size: 1.1rem">혹시 Intel Mac 이라면</span>

```c
echo "source /usr/local/share/chruby/chruby.sh" >> ~/.zshrc
echo "source /usr/local/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.1.1" >> ~/.zshrc
```

`[command] + q`로 Terminal을 재부팅 후 잘 설치 되었는지 버전을 확인하자.

```
ruby -v
```

`ruby 3.1.1p18` 버전 이상이면 문제 없다.

이제 Jekyll 차례이다.  
터미널에 아래의 명령어로 [Jekyll](https://jekyllrb.com/)을 설치한다.

```s
gem install bundler jekyll
```

설치가 완료 되었다면, 다음으로 넘어가보자.

<p style="font-weight:600; font-size: 1.2rem"><br><br>[STEP 3. Jekyll theme fork하고 레포지토리 이름 바꾸기]</p>

Github는 사용자들의 github page를 이용한 블로그 제작을 위해 몇 가지 테마를 제공하지만  
성에 차지 않으므로, Jekyll theme을 다운받아 사용할 것이다.

Github의 `username.github.io` Repository를 먼저 생성한 후 Jekyll theme repository를 자신의 repository로 clone하는 방법도 있지만, 항상 문제가 발생했으므로 훨씬 쉬운 방법을 소개하고자 한다.

먼저, 아래의 사이트들 중에서 마음에 드는 Jekyll 테마를 고른다.

- [GitHub.com #jekyll-theme repos](https://github.com/topics/jekyll-theme)
- [jamstackthemes.dev](https://jamstackthemes.dev/ssg/jekyll/)
- [jekyllthemes.org](http://jekyllthemes.org/)
- [jekyllthemes.io](https://jekyllthemes.io/)
- [jekyll-themes.com](https://jekyll-themes.com/)

사이트마다 차이가 있지만, '다운로드' 혹은 'Github', 'Homepage' 등의 버튼을 통해 테마의 repository로 이동한다.

이젠 내가 적용한 `'al-folio'`테마 제작자의 방법을 따라할 것이다.  
**_(각자의 테마 제작자가 README.md에 설명해놓은 절차를 그대로 따르는 것을 매우매우 추천한다.)_**

- 테마의 Github repository에서 자신의 `username.github.io`로 fork 한다.

<div class="row mt-3">
        {% include figure.html path="assets/img/al-folio-fork.png" class="img-fluid rounded z-depth-1" %}
</div>
<div class="row mt-3">
        {% include figure.html path="assets/img/fork-to-my-repo.png" class="img-fluid rounded z-depth-1" %}
</div>

- 자신의 gitblog 프로젝트를 저장하고자 하는 폴더의 경로로 이동 후 jekyll을 설치한다.

```s
cd /Users/myname/Desktop/myfolder/
git clone https://github.com/username/username.github.io.git
cd <your-repo-name>
bundle install
bundle exec jekyll serve
```

- 모든 명령어가 문제 없이 실행 되었다면 마지막 명령어 이후 local server를 통해 자신의 블로그를 미리 볼 수 있다.

{% highlight jekyll %}

AutoPages: Disabled/Not configured in site.config.
Pagination: Complete, processed 1 pagination page(s)
Jekyll Diagrams: Command Not Found: mmdc
done in 0.952 seconds.
Auto-regeneration: enabled for '/jobobdev.github.io'
Server address: http://127.0.0.1:4000
Server running... press ctrl-c to stop.

{% endhighlight %}

<p style="font-weight:600; font-size: 1.2rem"><br><br>[STEP 4. 블로그 호스팅 하기]</p>
지금 단계까지 문제 없지 잘 진행했다면, 이제 블로그를 자신의 것으로 만든 후 호스팅하는 것만 남았다!
아직 나 역시 그 단계를 진행중이므로 더 많은 걸 소개할 순 없지만, 당장 로컬에서 말고 실제로 자신의 github page에 블로그 페이지가 올라간 것을 확인하고 싶을 것이기 때문에 빠르게 아래의 단계를 실행해보자.  
  
- 먼저, username.github.io 폴더의 _config.yml 파일을 열고 기본적인 정보들을 수정한다.
- 아래의 사진처럼, `baseurl`은 비워두고 `url`에 자신의 github page 주소를 넣어준다.
<div class="row mt-3">
        {% include figure.html path="assets/img/config-edit-first.png" class="img-fluid rounded z-depth-1" %}
</div>
- 그 다음 변경 사항들을 커밋하고 push해주면 자신의 github page 주소로 블로그가 호스팅 된 것을 확인할 수 있다.
```
cd [블로그 repository 경로]
git add .
git commit -m "test blog"
git push
```
  
**모든 단계를 잘 마무리 했다면 이제 편집과의 싸움이다!! 난 아직 싸우는 중...**
