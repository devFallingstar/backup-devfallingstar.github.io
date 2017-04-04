<p align = "center">
<img src="http://i.imgur.com/JhbQ03z.png"/>
</p>
---

If there's any issue you are facing in setting up this theme I'm there for you. Just create an issue in this repository (<http://github.com/hemangsk/Gravity>), (<https://help.github.com/articles/creating-an-issue/>) and I'll get back to you asap.

![alt-tag](http://i.imgur.com/HyyXWQ0.png)
<img src="http://i.imgur.com/cPwoX3E.png"/>
<img src="http://i.imgur.com/3TMoBGj.png"/>
<img src="http://i.imgur.com/Z6h3uCp.png"/>
<img src="http://i.imgur.com/bB7IIHr.png"/>

***

# INSTALLATION

### Dependencies

Gravity uses Jekyll and it's built-in SCSS compiler for the associated CSS, so the first thing you'll need is Jekyll itself:

```bash
$ gem install jekyll
```

In case you don't have the `bundler` gem installed already, you can install it as follows:

```bash
$ gem install bundler
```

For pagination, Gravity uses the [jekyll-paginate](https://jekyllrb.com/docs/pagination/) gem :

```bash
$ gem install jekyll-paginate
```

***

# Usage

Once you have the required gems, you can go ahead and clone the
[Gravity repository](https://github.com/hemangsk/Gravity) or [download](https://github.com/hemangsk/Gravity/archive/master.zip)
a zip of the master branch.

Run :

```bash
$ jekyll serve
```

Jekyll should now be generating your content!

***

# 포스트 및 페이지 작성법
<br>
<div class="manual-post">
  <div class="manual manual-title">
  <h3><strong>포스트 작성</strong></h3>
  </div>
<p> <div class="manual-content">
  - <code class="highlighter-rouge">_posts</code> 폴더에 .markdown 파일을 생성합니다.<br>
  - 다음의 포맷을 따라 파일명을 작성합니다: YY-MM-DD-[포스트 제목]<br>
      <code>2016-03-30-i-love-development.markdown</code><br>
  - Front Matter와 내용을 작성합니다
  <div class="example">
    <span class='manual'>작성 형식</span><BR>
    <pre>---
layout: post | default | page
title:  String<span class="hint"> Post Title</span>
date:   Time Stamp
categories: String | Array of Strings<span class="hint"> Category / Categories </span>
---
</pre>
  </div>
  <div class="example">
    <pre>---
layout: post
title:  "The One with the Blackout"
date:   2016-03-30 19:45:31 +0530
categories: ["life", "friends"]
---
</pre>
  </div>
  </div>
</p>
</div>
<br>
<div class="manual-post">
  <div class="manual manual-title">
  <h3><strong>페이지 생성</strong></h3>
  </div>
<p>  <div class="manual-content">
  - 루트 디렉토리에 .md 파일을 생성합니다.<br>
  - 원하는 이름으로 파일명을 작성합니다.<br><code>development.md</code><br>
  - Front Matter와 내용을 작성합니다
  <div class="example">
    <span class='manual'>작성 형식</span><BR>
    <pre>---
layout: page
title: String <span class="hint">Title of the webpage</span>
permalink: / String / <span class="hint">Permalink for the webpage</span>
tagline: String <span class="hint">Optional Gravity Feature : Tagline for the page</span>
---
</pre>
  </div>
  <div class="example">
    <pre>---
layout: page
title:  "Science"
permalink:   /science/
tagline : "Humanity is overrated."
---
</pre>
  </div>
  </div>
</p>
</div>
<br>
<div class="manual-post">
  <div class="manual manual-title">
</div><br>
<div class="archiveIntro">
  <p>
    페이지 아카이브란?<br></p>
  <span class="archive-intro"> 페이지 아카이브는 <code>'archive'</code> 레이아웃을 통해 <br/>특정 카테고리에 속해있는 다수의 포스트를 하나의 페이지에 표시하는 방법입니다.
</span>
</div>
<br>
<p>  <div class="manual-content">
<div class="manual manual-title">
<h3><strong>아카이브 생성</strong></h3>
</div>
  - 루트 디렉토리에 .md 파일을 생성합니다.<br>
  - 파일명을 작성합니다. 파일명은 카테고리의 이름과 같아야합니다.<br> <code>life.md</code><br>
  - Front Matter와 내용을 작성합니다
  <div class="example">
    <span class='manual'>작성 형식</span><BR>
<pre>
---
layout: archive<span class="hint"> Archive Page Layout</span>
title: String <span class="hint">Title of the webpage</span>
permalink: / String / <span class="hint">Permalink for the webpage</span>
tagline: String <span class="hint"> Tagline for the page</span>
category : String <span class="hint"> Name of the category of which the page will show posts.</span>
---
</pre>
  </div>
  <div class="example">
    <pre>
---
layout: archive
title:  "Design"
permalink : "Design"
category: "design"
tagline: "It's all about perception."
---
</pre>
</div><br>
  </div>
</p>
</div>


#### DIRECTORY STRUCTURE

```
├── css                                         # => Output of the combined SASS files
│   └── style.scss
├── _includes                                   # => Contains partials that can be used with your layouts
│   ├── footer.html
│   ├── header.html
│   ├── head.html
│   ├── icon-github.html
│   ├── icon-github.svg
│   ├── icon-twitter.html
│   └── icon-twitter.svg
├── _layouts                                    # => Layout related HTML files
│   ├── archive.html
│   ├── default.html
│   ├── page.html
│   └── post.html
├── _posts                                      # => posts, dynamic content. Follow the format: YEAR-MONTH-DAY-title.MARKUP
│   ├── 2016-03-30-design-stories.markdown
│   ├── 2016-03-30-science0.markdown
│   ├── 2016-03-30-science.markdown
│   └── 2016-03-30-welcome-to-jekyll.markdown
└── _sass                                       # => SASS partials for styling
|   ├── _base.scss
|   ├── _layout.scss
|   └── _syntax-highlighting.scss
├── about.md
├── _config.yml                                 # => Configuration options or flags for your site go here
├── design.md
├── download.md
├── feed.xml
├── index.html
├── LICENSE.txt                                 # => Licensing information
├── README.md
└── science.md
```
