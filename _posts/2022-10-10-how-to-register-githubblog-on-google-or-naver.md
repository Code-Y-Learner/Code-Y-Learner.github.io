---
layout: post
title: 깃허브 블로그 검색엔진에 등록하기
subtitle: 구글 및 네이버 등록
categories: blog
tags: jekyll blog github SEO
---

## 검색 엔진 최적화(Search Engine Optimization)
SEO(검색 엔진 최적화)는 웹사이트가 유기적인 검색 방식을 통해 검색 엔진에서 상위에 노출될 수 있도록 최적화하는 과정이다.
jekyll기반의 깃허브 블로그를 구글에 등록하는 과정을 통해 하나씩 진행하였다.


### 1. Sitemap
<li>사이트맵은 사이트에 있는 페이지, 동영상 및 기타 파일과 그 관계에 관한 정보를 제공하는 파일</li>
<li>Google과 같은 검색엔진은 이 파일을 읽고 사이트를 더 효율적으로 크롤링합니다.</li>
<li> 사이트맵은 내가 사이트에서 중요하다고 생각하는 페이지와 파일을 Google에 알리고 중요한 관련 정보를 제공</li>

~~~
---
layout: null
---
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd" xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
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
~~~

먼저 sitemap.xml 파일을 생성 후 위 코드를 붙여넣고 여러분의 깃허브 블로그 폴더 root경로(보통 index.html이 있는 곳으로)에 넣어준다.

해당 파일이 깃허브페이지에 올라간 것을 확인하면 블로그에 접속해 주소 맨 뒤에 /sitemap.xml을 입력한 후 잘 나오는지 확인한다

블로그의 글들 _posts가 잘 연동되었는지를 확인하고 깨진 문자가 보이면 jekyll기준 한글명으로 된 파일이 _posts등등에 있는지 확인한다.

만약 한글 또는 특수문자가 있다면 깨지기 때문에 파일명을 영어로 바꿔주자

### 2. RSS 추가하기 

<li>RSS는 R really Simple S yndication 의 약자 입니다.</li>
<li>RSS를 사용하면 사이트 콘텐츠를 신디케이트할 수 있습니다.</li>
<li>RSS는 헤드라인과 콘텐츠를 쉽게 공유하고 볼 수 있는 방법을 정의합니다.</li>
<li>RSS 파일은 자동으로 업데이트될 수 있습니다.</li>
<li>RSS는 다양한 사이트에 대한 개인화된 보기를 허용합니다.</li>
<li>RSS는 XML로 작성됩니다.</li>

~~~
---
layout: null
---
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <title>{{ site.title | xml_escape }}</title>
    <description>{{ site.description | xml_escape }}</description>
    <link>{{ site.url }}{{ site.baseurl }}/</link>
    <atom:link href="{{ "/feed.xml" | prepend: site.baseurl | prepend: site.url }}" rel="self" type="application/rss+xml"/>
    <pubDate>{{ site.time | date_to_rfc822 }}</pubDate>
    <lastBuildDate>{{ site.time | date_to_rfc822 }}</lastBuildDate>
    <generator>Jekyll v{{ jekyll.version }}</generator>
    {% for post in site.posts limit:30 %}
      <item>
        <title>{{ post.title | xml_escape }}</title>
        <description>{{ post.content | xml_escape }}</description>
        <pubDate>{{ post.date | date_to_rfc822 }}</pubDate>
        <link>{{ post.url | prepend: site.baseurl | prepend: site.url }}</link>
        <guid isPermaLink="true">{{ post.url | prepend: site.baseurl | prepend: site.url }}</guid>
        {% for tag in post.tags %}
        <category>{{ tag | xml_escape }}</category>
        {% endfor %}
        {% for cat in post.categories %}
        <category>{{ cat | xml_escape }}</category>
        {% endfor %}
      </item>
    {% endfor %}
  </channel>
</rss>
~~~

feed.xml 파일을 생성후 코드를 붙여넣고 root 폴더에 넣은후 깃허브 페이지에 푸쉬하자

다시 블로그 주소 맨뒤에 /feed.xml을 추가하고 제대로 등록되었는지 확인해보자

###  robots.txt
검색엔진 로봇을 위한 파일으로 robots.txt 파일에 sitemap 파일 위치를 등록해두면 sitemap 파일을 더 쉽게 찾을 수 있게 도와주고 

만약 특정 사이트에서만 검색이 되게 해당 검색엔진만 내 웹사이트를 크롤링하는걸 허용 할수가 있다.

robots.txt 파일을 생성 후 아래코드를 붙여넣어줍니다. 똑같이 root 위치에 파일을 넣어줍니다.

~~~
User-agent: *
Allow: /

Sitemap: https://code-y-learner.github.io/sitemap.xml
~~~

아까 말했듯이 특정 사이트에서만 검색이 되게 하려면 허용할 검색엔진 명을 User-age에 넣어주면 된다.
예를들어 구글, 네이버일 경우 아래와 같이 하시면 됩니다.

~~~
User-agent: Googlebot
User-agent: NaverBot
Allow: /

Sitemap: https://code-y-learner.github.io/sitemap.xml
~~~

Sitemap에 여러분의 주소가 맞는지 확인해보고 *을 하면 모든 검색엔진을 허용한다는 뜻이다.

### 검색등록
마지막으로 이제 사이트에 등록을 해주자

### Google Search Console
[Google Search Console](https://developers.google.com/search#?modal_active=none) 에 접속 후
시작하기 버튼을 누르고

![Google_Search_image](https://elliethe.sirv.com/Images/googlesearch.PNG)

오른쪽 최상단 Search Console버튼을 누른다.
![Search_Console_button_image](https://elliethe.sirv.com/Images/search%EB%B2%84%ED%8A%BC.PNG)

속성 유형 선택화면이 나오면 URL 접두어 방식을 선택 후 자신의 블로그 주소를 등록한다.
![](https://elliethe.sirv.com/Images/4.PNG)
메타 태그를 이용한 사이트 소유권을 확인한 뒤 사이트맵을 등록해주면 된다. 구글 애널리틱스에 같은 계정으로 가입이 되어있으면 이미 소유 확인이 된다.

![](https://elliethe.sirv.com/Images/register-sitemap.PNG)

### Naver Webmaster
[네이버 웹마스터 도구](https://searchadvisor.naver.com/) 에 들어가 로그인하고 블로그 주소를 등록해 사이트를 추가해 주면 된다.

웹마스터 도구에서 제공하는 html 파일을 다운받아 깃허브 페이지에 푸쉬하여 본인 블로그에 업로드하거나 구글에서처럼 메타태그를 이용하는 방법으로 사이트 소유권을 확인한다.
마지막으로 요청 메뉴로 들어가 사이트맵을 제출해준다.

여러분들의 웹사이트가 잘 등록되길 기원하면 추후에 구글에드센스를 통해 광고도 등록할 수 있을것이다.
