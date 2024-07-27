---
layout: post
related_posts:
  - _posts/study/2024-03-02-프록시.markdown
title: CORS
categories:
  - study
  - computerscience
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  CORS가 뭘까??
---

* toc
{:toc}

협업을 하면서 서버를 배포했는데 HTTPS가 아닌 HTTP라면 이런 경고를 마주치게됩니다.

<img src="/assets/img/computerscience/HTTP 보안.png" />

위의 이미지는 프론트 서버를 HTTP로 배포했을 경우 나타납니다.

백엔드 서버도 마찬가지로 에러가 생길 수 있습니다. 저같은 경우는 벡엔드 서버에서 HTTP 포트로 넘겨줘서 통신을 제대로 할 수 없었습니다.

그래서 HTTP와 HTTPS가 뭐고 뭔 차이인지 알아볼려고 합니다.

# HTTP

## HTTP란?

**HTTP**(HyperText Transfer Protocol)는 클라이언트와 서버 간 통신을 위한 규칙 세트 또는 프로토콜입니다.

<img src="/assets/img/computerscience/HTTP 통신.png" />

사용자가 웹 사이트를 방문하면 사용자 브라우저가 웹 서버에 HTTP 요청을 전송하고 웹 서버는 HTPP 응답으로 응답합니다.

> 포로토콜이란?
> 컴퓨터 내부에서, 또는 컴퓨너 사이에서 데이터의 교환 방식을 정의하는 규칙 체계이다. 기기 간 통신은 교환되는 데이터의 형식에 대한 상호 합의를 요구합니다. 이러한 형식을 정의하는 규칙의 집합입니다.

- 기본 포트인 80번 포트에서 서비스 대기 중이며, 클라이언트가 TCP 80 포트를 사용해 연결하면 서버는 요청에 응답합니다.
- HTTP는 정보를 "텍스트"로 주고 받으며, 전송 신호를 인터셉트하는 경우 데이터 유출이 발생할 수 있습니다.

## HTTP 어떻게 작동하나요?

HTTP는 OSI(Open Systems Interconnection) 네트워크 통신 모델의 애플리케이션 계층 프로토콜로, 여러 유형의 요청과 응답을 정의하고 있습니다.

HTTP는 여러 유형의 요청과 응답을 정의합니다.

예를 들어, 웹 사이트는 HTTP GET(POST, PUT 등)과 같은 요청을 전송합니다. 그리고 서버는 숫자 코드 및 데이터 양식으로 다양한 유형의 HTTP 응답을 전송합니다.

다음은 몇 가지 예입니다.

| 번호  | 설명                               |
| --- | -------------------------------- |
| 200 | OK(정상)                           |
| 400 | Bad request(잘못된 요청)              |
| 404 | Resource not found(리소스를 찾을 수 없음) |

HTTP는 암호화되지 않은 데이터를 전송해, 제 3자가 그 정보를 가로채고 읽을 수 있어 위험합니다.

---
# HTTPS

## HTTPS란?

**HTTPS**(HyperText Transfer Protocol Secure)란 HTTP의 확장 버전으로 보다 안전한 버전입니다.

HTTPS는 443번 포트를 사용하며, 전송 내용을 암호화합니다. 암호화를 통해 발신자와 수신자를 제외한 중간 매개체에서 통신 내용을 확인할 수 없기 때문에, 발신자가 전송한 암호 및  기밀문서를 보호할 수 있습니다.

- SSL/TLS 인증서를 통해 세션 데이터를 암호화합니다.

<img src="/assets/img/computerscience/HTTPS 보안.png />

## SSL/TLS

SSL/TLS 인증서는 시스템에서 ID를 확인한 이후에 Secure Sockets Layer/전송 계층 보안(SSL/TLS) 프로토콜을 사용하여 다른 시스템에 대한 암호화된 네트워크 연결을 설정할 수 있도록 하는 디지털 객체입니다.

- SSL은 웹사이트와 브라우저 사이에 전송되는 데이터를 암호화하여 인터넷 연결을 보호하기 위한 표준 기술입니다.
- TLS(Transport layer Security)은 전송 계층 보안으로 인터넷 상의 커뮤니케이션을 위한 개인 정보와 데이터 보안을 용이하게 하기 위해 설계되어 널리 채택된 보안 프로토콜입니다.

## HTTPS 어떻게 작동하나요?

HTTPS는 HTTP 요청 및 응답을 SSL/TLS 기술에 결합합니다.

HTTPS 웹 사이트는 독립된 인증 기간(CA)에서 SSL/TLS 인증서를 획득해야 합니다.

이러한 웹 사이트는 신뢰를 구축하기 위한 데이터를 교환하기 전에 브라우저와 인증서를 공유하며 SSL 인증서는 암호화 정보도 포함하므로 서버와 웹 브라우저는 암호화된 데이터나 스크램블된 데이터를 교환할 수 있습니다.

1. 사용자 브라우저의 주소 표시줄에 `https://` URL 형식을 입력하여 HTTPS 웹 사이트를 방문합니다.
2. 브라우저는 서버의 SSL 인증서를 요청하여 사이트의 신뢰성을 검증하려고 시도합니다.
3. 서버는 퍼블릭 키가 포함된 SSL 인증서를 회신합니다.
4. 웹 사이트의 SSL 인증서는 서버 아이덴티티를 증명합니다. 브라우저에서 인증되면, 브라우저가 퍼블릭 키를 사용하여 비밀 세션 키가 포함된 메시지를 암호화하고 전송합니다.
5. 웹 서버는 개인 키를 사용하여 메시지를 해독하고 세션 키를 검색합니다. 그런 다음, 세션 키를 암호화하고 브라우저에 승인 메시지를 전송합니다.
6. 이제 브라우저와 웹 서버 모두 동일한 세션 키를 사용하여 메시지를 안전하게 교환하도록 전환합니다.

## HTTPS는 SEO에 영향을 주나요?

HTTPS는 HTTP보다 안정하여 **안정성을 얻고 신뢰성을 얻습니다.**

그런데 **SEO에도 영향을 준다고 합니다.**

주요 검색 엔진(SEO)는 SSL/TLS 보호를 검색 엔진 최적화의 순위 요소로 만들어 졌으며 SSL/TLS 보안 웹 사이트는 SSL/TLS 인증서가 없는 유사한 웹 사이트보다 검색 엔진에서 더 높은 순위를 차지합니다.

# HTTP와 HTTPS 차이점

|     | HTTP                        | HTTPS                              |
| --- | --------------------------- | ---------------------------------- |
| 의미  | Hypertext Transfer Protocol | Hypertext Transfer Protocol Secure |
| 포트  | 기본 포트 80                    | 기본 포트 443                          |
| 용도  | 이전 텍스트 기반 웹  사이트            | 모든 최신 웹 사이트                        |
| 보안  | 추가 보안 기능 없음                 | 퍼블릭 키 암호화에 SSL 인증서 사용              |
| 이점  | 인터넷을 통한 통신 지원               | 웹 사이트에 대한 권위, 신뢰성 및 검색 엔진 순위 개선    |

HTTP와 HTTPS의 가장 큰 차이점은 보안입니다. 

또한 검색엔진 최적화(SEO)에 있어서 HTTPS가 더 좋습니다.

---
# 참고

> <a href="https://velog.io/@winsome_joo/HTTP-HTTPS">HTTP & HTTPS</a>
> 
> <a href="https://rnclf1005.tistory.com/11">[WEB] HTTP / HTTPS</a>
> 
> <a href="https://seo.tbwakorea.com/blog/https-http/">HTTPS란? – HTTP와의 차이점 및 전환의 중요성</a>
> 
> <a href="https://aws.amazon.com/ko/compare/the-difference-between-https-and-http/">HTTP와 HTTPS의 차이점은 무엇인가요?</a>
> 
> <a href="https://aws.amazon.com/ko/what-is/ssl-certificate/">SSL/TLS 인증서란 무엇인가요?</a>
> 
> <a href="https://www.cloudflare.com/ko-kr/learning/ssl/transport-layer-security-tls/">SSL, TLS, HTTPS는 무엇인가요?</a>