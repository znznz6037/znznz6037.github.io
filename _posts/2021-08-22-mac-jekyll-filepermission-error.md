---
layout: post
title: "맥 Mac에서 Gem::FilePermission Error 해결 방법"
date: "2021-08-22 13:41:30"
categories: [Blog]
tags: [Mac, Jekyll, Blog]
---

## 원인

![Gem::FilePermission Error](https://i.imgur.com/kp24Z3T.png)

 rbenv 로 관리되는 루비가 아닌, 시스템 루비로 설치되어 있어서 생기는 에러

---

## 해결방법

먼저 rbenv를 설치한다.

{% highlight zsh%}

brew install rbenv ruby-build

rbenv install {루비 버전 입력}

rbenv global {루비 버전 입력}

{% endhighlight %}

<br/>

설치 가능한 루비 버전은 아래 명령으로 확인 가능 

{% highlight zsh %}

rbenv install -l

{% endhighlight %}

<br/>

![ruby versions](https://i.imgur.com/3mRmFpY.png)

설치가 잘 되었다면, asterisk가 위와 같이 system이 아닌 rbenv 앞에 있어야 한다.

<br/>

마지막으로 환경변수를 추가해줘야 하므로 쉘 설정 파일을 열어 다음 코드를 추가한다.

{% highlight zsh%}

vi ~/.zshrc

{% endhighlight %}

{% highlight zsh%}

export PATH={$Home}/.rbenv/bin:$PATH && \

eval "$(rbenv init -)"

{% endhighlight %}

<br/>

변경 사항 적용

{% highlight zsh%}

source ~/.zshrc

{% endhighlight %}

