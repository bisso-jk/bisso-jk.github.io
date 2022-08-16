---
title: Marshalling vs Serialization
author: Bisso
date: 2022-08-17 08:20:00 +0900
categories: [Java, Theory]
tags: [writing]
---

# Mashalling vs Serialization

## Mashalling

### 설명

> [Wikipedia](https://ko.wikipedia.org/wiki/%EB%A7%88%EC%83%AC%EB%A7%81_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99))
한 객체의 메모리에서 표현방식을 저장 또는 전송에 적합한 다른 데이터 형식으로 변환하는 과정이다.
또한 이는 데이터를 컴퓨터 프로그램의 서로 다른 부분 간에 혹은 한 프로그램에서 다른 프로그램으로 이동해야 할 때도 사용된다.
마셜링은 직렬화(serialization)와 유사하며 한 오브젝트(여기서는 직렬화 된 오브젝트)로 멀리 떨어진 오브젝트와 통신하기 위해 사용된다
>

## Serialization

### 설명

> [Wikipedia](https://ko.wikipedia.org/wiki/%EC%A7%81%EB%A0%AC%ED%99%94)
>
>
> 직렬화(serialization)는 [컴퓨터 과학](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99)의 데이터 스토리지 문맥에서 [데이터 구조](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EA%B5%AC%EC%A1%B0)나 [오브젝트](https://ko.wikipedia.org/wiki/%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8) 상태를 동일하거나 다른 컴퓨터 환경에 저장(이를테면 [파일](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%ED%8C%8C%EC%9D%BC)이나 메모리 [버퍼](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EB%B2%84%ED%8D%BC)에서, 또는 [네트워크](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC) 연결 링크 간 전송)하고 나중에 재구성할 수 있는 포맷으로 변환하는 과정이다.
>
> 오브젝트를 직렬화하는 과정은 오브젝트를 [마샬링](https://ko.wikipedia.org/wiki/%EB%A7%88%EC%83%AC%EB%A7%81_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99))한다고도 한다. 반대로, 일련의 바이트로부터 데이터 구조를 추출하는 일은 역직렬화(deserialization)이라고 한다.
>

Serialization은 객체 전송을 위해서 Byte Stream 으로 변환하여 파일 또는 네트워크를 통해 스트림이 가능하도록 하게 하는 것

Mashalling은 바이트 스트림에 보낼 수 있는 객체로 변환하는 작업을 뜻함

## 참고

[https://weicomes.tistory.com/63](https://weicomes.tistory.com/63)

[https://lueseypid.tistory.com/42](https://lueseypid.tistory.com/42)