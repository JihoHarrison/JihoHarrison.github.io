---
layout: post
title:  "[안드로이드] Android 앱 버전 설정하기"
date:   2021-01-10 18:34:10 +0700
categories: [안드로이드]
---

> __참고 레퍼런스__
>
> ✍🏻 [Android Developer 도큐먼트 - 앱 버전 지정하기](https://developer.android.com/studio/publish/versioning)

## 1️⃣ What is 앱 버전(Version)?

* __앱 버전이란?__

    * <img width="250" alt="01" src="https://user-images.githubusercontent.com/31889335/104104315-9aed4d00-52ea-11eb-8859-3b0afef7e5f8.png">

    * 사용자들은 앱을 설치하기 전, 해당 앱 버전을 확인할 수 있다.(위 그림은 Google Play Store 캡처 사진)

    * 보통 개발자는 앱을 첫 출시 후, 앱의 버전을 계속 늘려가며 새로운 기능을 추가하거나 버그를 해결하여 개선해나간다.

* __앱 버전을 설정하고, 앱 버전을 관리해야 하는 이유__

    * 앱 버전은 앱 업그레이드와 앱 유지 보수 전략을 생각하면 중요한 요소다.

    * 사용자는 자신의 디바이스 기기에 설치된 앱의 버전을 확인할 수 있길 원하고, 설치할 수 있는 업그레이드 버전을 확인하길 원한다.

* __앱 버전 설정하기__

    * 모듈 수준 build.gradle 파일의 android{ } > defaultConfig{ } 블럭 내부에서 default 앱 버전을 지정해줄 수 있다.

    * <img width="605" alt="02" src="https://user-images.githubusercontent.com/31889335/104104563-4054f080-52ec-11eb-845c-b14cc885117c.png">

    * __versionCode__ : 현재 APK에는 앱의 N 번째 출시 버전이 포함되어 있음을 나타낸다. 양의 정수로 표현하며 번호가 높을수록 최신 버전이다. 사용자에게 보여지지 않는 내부 버전 번호이다.

    * __versionName__ : 사용자에게 보여질 앱 버전 문자열을 나타낸다. Android 시스템은 versionName을 사용하여 사용자가 더 낮은 앱 버전의 APK를 설치하지 못하도록 한다.(다운그레이드 방지)

    * 각 빌드 타입/제풀 버전 별로 앱 버전을 다르게 할 수도 있다.

        * 참고 자료 --> [빌드 타입/제풀 버전 별로 앱 버전 지정하기](https://developer.android.com/studio/build/build-variants?hl=ko#product-flavors)

        * 만약 빌드 타입/제품 버전 별 설정에 versionCode 나 versionName 설정이 없다면 defaultConfig에서 설정해준 기본 앱 버전 정보를 해당 빌드 타입/제품 버전의 앱 버전으로 사용한다.

        * 빌드 타입에 관해서는 [이 블로그의 다른 포스팅 - 앱 빌드 및 실행](https://choheeis.github.io/newblog//articles/2021-01/app-build-run)에서 알 수 있다.

# 끝!

