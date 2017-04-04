---
layout: post
title: "[Swift] UIWebView 쿠키 얻어오기"
date: 2017-02-12 23:31:00 +0900
categories: ["Development", "iOS", "Swift"]
---

웹뷰를 사용할 때, 웹뷰에서 현재 사용중인 쿠키 값을 확인하여, 그에 맞는 flow를 작성해야할 때가 있다.

그럴 때, NSHTTPCookieStorage를 이용하여, 웹뷰가 현재 사용하는 쿠키와 그것의 값을 얻어올 수 있다.

먼저 쿠키 객체를 Array의 형식으로 가져오려면 다음과 같은 코드를 사용할 수 있다.

{% highlight swift %}
if let cookies = NSHTTPCookieStorage.sharedHTTPCookieStorage().cookies {
    for cookie in cookies {
        //cookie값을 확인 가능
    }
}
{% endhighlight %}

각 쿠키의 값이나 이름을 가져오고 싶다면 cookie 객체의 name 혹은 value 함수를 호출하면 된다.

{% highlight swift %}
if cookie.name == "Something" {
    //cookie.name에 따라 요리하자.
}
{% endhighlight %}
OR
{% highlight swift %}
if cookie.value == "Something" {
    //cookie.value에 따라 요리하자.
}
{% endhighlight %}