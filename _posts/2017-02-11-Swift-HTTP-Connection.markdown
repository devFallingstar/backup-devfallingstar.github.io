---
layout: post
title: "[Swift] 인터넷 연결 권한 오류"
date: 2017-02-11 00:18:00 +0900
categories: ["Development", "iOS", "Swift"]
---


한동안 iOS 개발을 잘 안하다보니 부가적인 보안 업데이트의 내용을 확실히 몰랐나보다.

http 프로토콜을 사용하여 인터넷에 접속하기 위해서는 안드로이드OS의 Manifest를 수정하는 것 처럼 권한을 지정해줘야 하는데, info.plist에서 이런 설정을 해줄 수 있다.

http 프로토콜은 nen-secured 기반이기 때문에 이런 보안 및 권한 설정이 필요한 것 같다.

Information Property List의 value 중에서 App Transport Security Setting를 찾고(없으면 만들고), Allow arbitary Loads라는 Boolean 기반 Key를 추가해준 뒤, YES로 값을 정하면 된다.

![적용 결과](/pictures/Swift-HTTP-connection.png)

보다 자세한 설명은 [공식 문서](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW33)에서 확인할 수 있다.