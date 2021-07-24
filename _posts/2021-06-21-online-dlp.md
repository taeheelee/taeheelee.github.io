---
layout: post
published: true
title: HTTPS API 호출과 SSL 통신
date: '2021-07-24'
subtitle: 회사 보안 네트워크에서 HTTPS API를 호출하기
---

## 요약  
개발기 / 운영기에서는 정상 동작하는 HTTPS API 호출이 로컬 개발환경(?)에서는 동작하지 않는 경우, **온라인DLP 솔루션** 등의 보안 솔루션을 의심해보자.

---

## 어느 날 특정 조건을 만족할 경우 메일을 발송하는 기능을 개발하라는 업무가 내려왔다.  
<br>
사실 메일 발송 기능 자체는 이미 구현되어 있었기 때문에, 조건 만족여부만 확인해서 메일발송 함수를 호출하면 끝나는 단순한 개발건이었다.
그런데 문제는 운영기에서 잘 동작하는 메일발송 코드가 로컬 개발환경에서는 아래와 같이 오류를 뿜어낸다는 점이었다.  

~~아니 같은 소스 복붙했는데 왜 안돼??~~
<br>
<br>

```
오류 메시지  

sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
```

이 문제를 가지고 하루종일 머리를 싸맸다.  
1. 운영기와 개발기에서 잘 도는 코드인데 왜 로컬에서는 돌지 않을까?
1. 개발환경 설정(*.xml 또는 *.config파일)을 잘못한걸까?
1. ~~원래 문제가 있던 코드인데 이제서야 발견한걸까?~~

<br>

그러던 중 [이 블로그](https://jinhokwon.github.io/devops/devops-java/ "Java PKIX path building failed 대처")에서 '회사 업무용 PC'라는 키워드를 보고 머릿속을 스치는게 있었다.

> 보통 일정 규모 이상의 회사 네트워크에는 트래픽을 감시하는 보안 장비가 존재한다.  
> 이 장비는 온라인DLP 라고도 부르며, <b>SSL 통신도 감시할 수 있는 특징</b>이 있다.

그렇다. 개발환경은 회사 네트워크를 통해 외부와 연결되어 있고, 회사 네트워크에는 보안장비가 설치되어 있다.
직원들의 PC 트래픽을 모니터링~~감시~~하기 위해 온라인DLP라는 장비를 사용하고 있다.
이 장비는 SSL 인증서가 적용된 HTTPS 환경에서 암호화 된 트래픽을 감시하기 위해 사이트의 인증서를 회사의 인증서로 중간에서 바꿔치기 한다.
회사의 인증서를 통해 통신하기 때문에 직원들이 어떤 통신을 하고있는지 모니터링할 수 있다.

그리고 이 과정에서 원래의 인증서가 변경되기 때문에 인증서 관련 오류메시지가 발생했다.
일반적으로 이 문제를 해결하기 위해서는 아래의 3가지 방법이 있다.

1. java의 신뢰할 수 있는 인증서 저장소인 cacerts에 호출하고자 하는 사이트의 인증서를 추가한다.
   1. 가장 일반적인 방법이며 권장하는 방법. java가 신뢰할 수 있는 인증서라고 인증서 목록에 추가하는 방법이다.
2. 아무 인증서나 신뢰할 수 있도록 코드를 추가한다. (보안상 비권장)
   1. 구글링하다보면 나오는 방법이지만 실제로는 아무 인증서나 안전하다고 바이패스 시키기 때문에 보안상 위험하다.  
   2. 코드는 아래를 참고
3. 사내 보안장비에서 특정 URL로 요청하는 코드는 예외처리를 진행한다.
   1. 이 문제를 해결하기 위해 선택했던 방법이다. 특정 URL로 가는 트래픽에 대해서는 온라인DLP 모니터링을 하지 않도록 예외처리를 요청하였다.
   2. 문제를 해결하기 위해서 1의 방법도 있고 3의 방법도 있다. 그러나 1을 적용하면 운영기와 개발기 환경에 차이가 발생한다. 그리고 운영기에서 나가는 트래픽은 온라인 DLP가 적용되지 않기 때문에 개발과 운영 환경을 동일하게 처리하기 위해 3번의 방법이 가장 적합하다고 생각했다.

이번 해프닝을 통해 한가지 배운 점이 있다.
코드를 복붙해서 날로 먹으려고 할 때도 항상 개발환경을 고려해야 한다.
~~이번에는 날로 먹으려다 많이 고생했지만 다음번엔 더 날로 먹어보겠다..~~
  


별로 권장하지는 않는 코드이지만 위의 1, 3 방법을 진행하기 전 임시로 테스트할 때 쓸 수는 있을 것 같다.
```java
String htmlUrl = "https://external.domain.com";

TrustManager[] trustAllCerts = new TrustManager[] { 
    new X509TrustManager() {
        public X509Certificate[] getAcceptedIssuers() {
            return null;
        }

        public void checkClientTrusted(X509Certificate[] certs, String authType) {}
        public void checkServerTrusted(X509Certificate[] certs, String authType) {}
	}
};

SSLContext sc = SSLContext.getInstance("SSL");
sc.init(null, trustAllCerts, new SecureRandom());
HttpsURLConnection.setDefaultSSLSocketFactory(sc.getSocketFactory());

// 출처 : https://jinhokwon.github.io/devops/devops-java/
```

