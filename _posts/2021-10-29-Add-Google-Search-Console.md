---
layout: post
title: "Jekyll 블로그 검색엔진에 노출시키기"
date: "2021-10-31 19:34:32"
categories: [Blog]
tags: [Blog, Jekyll]
---



[Google Search Console](https://search.google.com/search-console/about) 을 통해 내 블로그 게시글을 검색엔진에 노출시킬 수 있다.



접속 후에 시작하기 버튼을 누른 후, 인증 작업을 수행한다.

---

### 1. 소유권 인증

<img src="https://i.imgur.com/1b9epdh.png" alt="1" style="zoom: 33%;" />

도메인을 구입하지 않고 Github에서 제공해주는 URL을 그대로 사용하고 있기 때문에,  
도메인이 아닌 URL 접두어에 블로그 URL을 넣어준다.



<img src="https://i.imgur.com/pv1lrHf.png" alt="2" style="zoom: 50%;" />

HTML 파일 업로드 외에도 인증 방식이 여러가지 있는데,  
HTML 파일 업로드 방식이 제일 쉬우니 이걸로 진행하였다.

해당 HTML 파일 다운로드 후, 블로그 폴더 루트 경로(_config.yml 파일이 있는 곳) 에 업로드한다.

업로드 한 후 로컬 환경에서 `http://localhost:4000/HTML파일명` 으로 들어가 아래와 같이 잘 업로드 되었는지 확인한다.

![3](https://i.imgur.com/9LuoQBN.png)

---

### 2. sitemap.xml 작성

sitemap을 작성해줘야 구글 검색 크롤러가 주기적으로 크롤링 작업을 수행한 후 블로그 게시글이 표시될 수 있다.

```xml
---
layout: null
---
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd"
        xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    {% for post in site.posts %}
    <url>
        <loc>{{ site.url }}{{ post.url }}</loc>
        {% if post.lastmod == null %}
        <lastmod>{{ post.date | date_to_xmlschema }}</lastmod>
        {% else %}
        <lastmod>{{ post.lastmod | date_to_xmlschema }}</lastmod>
        {% endif %}

        {% if post.sitemap.changefreq == null %}
        <changefreq>weekly</changefreq>
        {% else %}
        <changefreq>{{ post.sitemap.changefreq }}</changefreq>
        {% endif %}

        {% if post.sitemap.priority == null %}
        <priority>0.5</priority>
        {% else %}
        <priority>{{ post.sitemap.priority }}</priority>
        {% endif %}

    </url>
    {% endfor %}
</urlset>
```

[출처](https://pilot376.tistory.com/43)

아까 업로드했던 HTML 파일과 동일한 위치에 sitemap.xml 파일을 업로드한다.

똑같이 로컬 환경에서 https://localhost:4000/sitemap.xml 접속 후 잘 올라갔는지 확인해준다.

<img src="https://i.imgur.com/O7zJJ8o.png" alt="5" style="zoom: 25%;" />



잘 나오는지 확인해보고 html 파일, sitemap.xml 두 파일 커밋 한 후,

<img src="https://i.imgur.com/oJd4pX6.png" alt="4" style="zoom: 33%;" />

Google Search Console에서 Sitemap을 제출해주면 된다.

처음 등록하자마자 바로 상태가 바뀌는게 아니라 빠르면 일주일 내, 느리면 한달 정도 걸린다고 하니 기다리면 된다.



### 결과

<img src="https://i.imgur.com/fN2Fina.png" alt="6" style="zoom:33%;" />

10/31  
나는 이틀만에 상태가 바뀌었다.  
사이트맵 제출이 성공한다 하더라도 인덱스 생성은 좀 더 걸린다고 한다.

<img src="https://i.imgur.com/cuYbiTj.png" alt="7" style="zoom: 25%;" />

11/3  
사이트맵 제출부터 구글링 노출까지 5일 정도 걸렸다!  
자세히 확인해보니 인덱스가 생성되어 제대로 검색되는 페이지도 있고, 제대로 생성되지 않은 페이지도 있었다.

### 참고

1. Index 생성 여부 확인하는 방법  
   site:블로그URL 구글에서 검색
2. Google Search Console에 사이트맵 크롤링 요청하는 방법  
   http://www.google.com/ping?sitemap=블로그URL

