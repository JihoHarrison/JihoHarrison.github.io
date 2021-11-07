---
layout: post
title:  "[안드로이드] WebView ShouldOverrideUrlLoading"
date:   2021-11-07 15:34:10 +0700
categories: [안드로이드]
---

## 프롤로그

 * 여러가지 안드로이드 프로젝트를 진행하다가 결국 마주하게 되었다.. WebView에 대한 문제를... 발단은 평소 문제없이 진행되던 메드나 서비스의 __PASS앱을 통한 사용자 인증 절차__ 가 웹뷰 내부에서 __intent://__ 로 시작하는 url로 redirect 될 시 먹통이 되었다.

 * 평소 웹뷰라는 뷰에 대한 학습을 소홀이 했던 것을 후회하며 천천히 문제점을 찾아 본 결과, __ShouldOverrideUrlLoading__ 함수에서, 웹뷰 내에서 처리되는 url에 대한 예외처리를 해 주지 않아서 발생한 오류였다.

<img src="https://user-images.githubusercontent.com/27722059/140637024-b84ce35c-b008-4cdb-99d1-00513eaf020b.png" width="600" height="800"/>
<img src="https://user-images.githubusercontent.com/27722059/140637030-24c5cd5d-a742-4ea6-994d-f03548d576e1.png" width="600" height="800"/>

## ShouldOverrideUrlLoading
 * 쉽게 말해서 현재 내가 사용하고 있는 웹뷰 내에서 다른 url을 호출하며 돌아다닐 때 제어권(?)을 요청하는 함수이다. 처음 액티비티에 로드 될 때는 문제가 없지만 웹뷰 상에서 페이지 이동이 일어날 경우 디바이스 브라우저로 실행되거나 나처럼 특정 앱의 스키마를 사용해야하는 경우는 페이지를 로드할 수 없다는 에러를 뱉게될 수 있다.

![ShouldOverrideUrlLoadingDeprecated](https://user-images.githubusercontent.com/27722059/140636493-5738d772-53ae-47b2-b842-d4cacd03940d.png){: width="800" height="600"}
![ShouldOverrideUrlLoading](https://user-images.githubusercontent.com/27722059/140636518-39c6bc85-19de-4329-8d2b-8220104b93ea.png){: width="800" height="600"}

 * 기존에 사용되고 있었던 __ShouldOverrideUrlLoading__ 은 __deprecated__ 되었다. 하지만 업데이트 된 새로운 함수를 찾아보니 기존 함수와 동일한 이름의 함수였고, 차이점이라 하면 매개변수로 받고있는 String 형식의 url이 __WebResourceRequest__ 형식의 __request__ 로 바뀌었다는 점이다. 기존에 매개변수의 url을 직접 사용했던 부분은 __request.url__ 을 통해서 가져올 수 있다.

## 문제가 됐던 기존 형식
![image](https://user-images.githubusercontent.com/27722059/140636772-a129061e-cd1f-463e-b861-98f2afa6d9cd.png)
 * 문제가 되었던 기존 코드의 형태이다. 보이는 것과 같이 __shouldOverrideUrlLoading__ 함수 내에서 __request.url__ 로 url을 호출하고 있지만 해당 url에 대한 예외처리 등을 처리해주는 부분이 없던 것이다.
 * PASS 앱을 통한 사용자 인증 절차가 수행되는 url의 형식은 __intent://__ 로 시작된다. 웹뷰 내에서 또 다른 앱을 불러오는 형식이다. __http, https__ 형식이라면 새로운 브라우저 창을 실행시키던가 해서 어떻게든 실행이 되겠지만 전화 기능과 연결시키는 __tel:, 또는 mailTo:, sms:__ 와 같이 새로운 앱 또는 기능을 띄워주는 형식은 __shouldOverrideUrlLoading__ 에서 각각의 url에 대한 처리를 진행 해 주어야 한다.

## intent://의 행동 처리

 * 다음과 같이 __intent://__ 로 시작하는 url에 대한 예외 처리를 진행 해 주었고 설치되어있지 않을 시 __package name__ 을 추출하여 market으로 이동하는 로직을 구현 해 주었다.

```java
override fun shouldOverrideUrlLoading(
      view: WebView,
      request: WebResourceRequest?
  ): Boolean {
      val url: String = request?.url.toString()
      if (url.startsWith("intent://")) {
        var intent: Intent? = null
        return try {
            intent = Intent.parseUri(url, Intent.URI_INTENT_SCHEME)
            val uri = Uri.parse(intent.dataString)
            startActivity(Intent(Intent.ACTION_VIEW, uri))
            true
        } catch (ex: URISyntaxException) {
            false
        } catch (e: ActivityNotFoundException) {
            if (intent == null) return false
            val packageName = intent.getPackage()
            if (packageName != null) {
                startActivity(
                Intent(
                    Intent.ACTION_VIEW,
                    Uri.parse("market://details?id=$packageName")
                )
            )
            return true
        }
        false
        }
    }
    view.loadUrl(url)
    return false
}
```

![PASSsuccess01](https://user-images.githubusercontent.com/27722059/140638325-eb2db37c-3060-424d-b8ad-b7a639ea18f6.png)
![PASSsuccess02](https://user-images.githubusercontent.com/27722059/140638345-7001ddd5-9193-498b-aae0-aff231f1777a.png)