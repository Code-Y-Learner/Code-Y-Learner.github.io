---
layout: post
title: 깃허브 블로그 관련 팁들
subtitle: jekyll 기반 오류 사례들 포함
categories: blog
description: Tips for post or using github blog
tags: jekyll blog github
---

## github blog를 운영하면서 느낀점들 tips

### 이미지 올리기
github page에서 이미지를 올리려면 github page는 정적 사이트만 지원하기 때문에 보통 내가 직접 이미지를 폴더에 넣어서 써야할 경우가 있다.

하지만 이미지를 여러번 캡쳐해서 올려야 하는 경우에는 귀찮을 수가 있는데 꼼수로 issue를 이용하는 방법이 있다.

![capture_image](https://elliethe.sirv.com/Images/issue%EC%9D%B4%EB%AF%B8%EC%A7%80.PNG)
바로 깃허브 issue에 이미지를 복붙하면 자동으로 이미지를 링크까지 걸어준다. issue를 등록할 필요도 없다. 깃허브 어딘가에 저장된 이 이미지는 어느 정도 시간이 사라지면 사라질 수 있다는 것에 주의하자

그렇다면 다른 방법은 없을까? 무료 이미지 CDN 사이트등을 활용하면 좋을 수 있다.

이미지 CDN 사이트들은 사이트에 맞춰 또 그 사이트를 보는 뷰어에 맞춰 이미지를 제공해서 최적화 및 로딩시간을 줄여준다.

특히 불필요하게 깃허브에 이미지를 올리는 것보다 이미지 cdn 사이트에 업로드 후 링크를 활용하는 것이 더 깔끔할 수 있다.


### Syntax Highlight 오류
깃헙에서는 "```"을 써서 문법강조가 가능한데

````python
```
import random
print(random.randint(1,10))
```
````

이런식으로 마크다운 언어에 써서 올리면 아래처럼 깔끔하게 코드가 보이게 할 수 있다.
```python
import random
print(random.randint(1,10))
```

하지만 jekyll의 riquid문법을 highlight로 감싸 마크다운 언어에서 보이게 하려는데
뜬금없이 riquid문법이 보여지는 게 아니라 실제로 작동해서 애를 먹었다.

밑에 예시를 들면 다음과 같은 코드가 보여지는게 아니라 실행이 되어버렸는데

{% highlight liquid %}
{% raw %}
---
layout: null
---
  {% for post in site.posts %}
    <url>
      <loc>{{ site.url }}{{ post.url }}</loc>
    </url>
  {% endfor %}
</urlset>
{% endraw %}
{% endhighlight %}

덕분에 글 하나가 엉망진창이 되었다.

고치는 방법도 잘 찾아지지 않아 애를 먹었는데
직접 리퀴드를 하이라이트 하여 고칠 수 있었다.


### giscus google analytics 등록
댓글 기능을 giscus로 등록하였다. disqus 등등 많은 jekyll사이트용 댓글기능이 있는데 여러글들을 보면서 장단점을 보면서 giscus를 등록하기로 하였다.
또한 google analytics를 등록하여 내 사이트의 방문자등을 검토 가능하게 하였다.


