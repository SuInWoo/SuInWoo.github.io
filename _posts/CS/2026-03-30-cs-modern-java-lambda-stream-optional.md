---
layout: post
title: "[CS] Modern Java: Lambda, Stream & Optional"
date: 2026-03-30 00:00:00 +0900
categories: [CS]
tags:
  - CS공부
---

# [CS] Modern Java: Lambda, Stream & Optional

과거의 자바가 "어떻게(How) 루프를 돌릴까"에 집중했다면, 현대 자바는 **"무엇(What)을 하고 싶은가"**에 집중합니다.

---

## 정리

## 1. 패러다임의 전환: 명령형(How)에서 선언형(What)으로

과거의 자바가 "어떻게(How) 루프를 돌릴까"에 집중했다면, 현대 자바는 **"무엇(What)을 하고 싶은가"**에 집중합니다.

- 명령형: for문을 돌면서 if로 거르고, 새 리스트에 add한다. (코드가 길고 의도가 한눈에 안 들어옴)

- 선언형(Stream): "필터링하고, 변환해서, 수집해라." (비즈니스 로직이 한눈에 보임)

## 2. 람다 식 (Lambda Expression): "함수를 변수처럼"

람다는 익명 함수를 만드는 방식이지만, 본질은 **"행위(Behavior)를 파라미터로 전달하는 것"**입니다.

### 2.1 함수형 인터페이스 (Functional Interface)

- 람다를 쓰려면 단 하나의 추상 메서드만 가진 인터페이스가 필요합니다.

- @FunctionalInterface 어노테이션을 붙여 컴파일 타임에 체크하는 것이 관례입니다.

- 자주 쓰는 인터페이스: Predicate<T> (조건 체크), Consumer<T> (소비), Function<T, R> (변환).

## 3. 스트림 API (Stream API): "데이터의 흐름"

스트림은 데이터 소스(컬렉션, 배열 등)를 추상화하여 연속적으로 처리하는 도구입니다. 원본 데이터를 변경하지 않는다는 점이 가장 중요합니다.

### 3.1 스트림의 3단계 파이프라인

1. 생성 (Source): list.stream()

1. 중간 연산 (Intermediate): filter, map, sorted, distinct (결과가 다시 스트림이므로 연결 가능)

1. 최종 연산 (Terminal): collect, forEach, count, anyMatch (스트림을 닫고 결과를 반환)

### 3.2 💡 실무 체감 예시 (물류 시스템 시나리오)

> 상황: 배차 대기 중인 주문 리스트에서 "서울" 지역 주문만 골라 주문 ID 리스트로 변환해야 함.

- Old 방식 (for-each):

Java

```java
List<Long> seoulOrderIds = new ArrayList<>();
for (Order order : orders) {
    if ("SEOUL".equals(order.getRegion()) && "PENDING".equals(order.getStatus())) {
        seoulOrderIds.add(order.getId());
    }
}          // 결과 수집
```

- Modern 방식 (Stream):

```java
List<Long> seoulOrderIds = orders.stream()
    .filter(o -> "SEOUL".equals(o.getRegion())) // 조건 필터링
    .filter(o -> "PENDING".equals(o.getStatus())) // 연속 필터링 가능
    .map(Order::getId)                           // ID만 추출 (Method Reference)
    .collect(Collectors.toList());               // 결과 수집
```

## 4. Optional: "Null과의 전쟁 종식"

NullPointerException(NPE)은 개발자의 영원한 숙제입니다. Optional은 값이 "있을 수도 있고 없을 수도 있음"을 명시적으로 표현하는 컨테이너입니다.

### 4.1 잘못된 사용 vs 올바른 사용

- Bad: if (opt.isPresent()) { return opt.get(); } (기존 null 체크와 다를 게 없음)

- Good: 함수형 메서드 활용

- opt.orElse("Default"): 없으면 기본값 반환.

- opt.orElseThrow(): 없으면 예외 던지기.

- opt.ifPresent(v -> ...): 있을 때만 로직 실행.

## 5. 공부하며 느낀 Deep Insight (Human-like Notes)

### 🚀 지연 연산 (Lazy Evaluation)의 마법

스트림의 중간 연산은 최종 연산이 호출되기 전까지 절대 실행되지 않습니다. * "100만 개 데이터 중 filter 후 findFirst"를 하면, 100만 개를 다 거르는 게 아니라 조건을 만족하는 첫 번째 데이터를 찾는 순간 멈춥니다. 성능 최적화가 자동으로 일어나는 지점입니다.

### ⚠️ 주의할 점 (Pitfalls)

1. 가독성 vs 복잡성: 스트림이 무조건 좋은 건 아닙니다. 로직이 너무 복잡해지면 오히려 for문보다 읽기 힘들어집니다. "한 줄이 너무 길어지면 끊어가자."

1. 병렬 스트림(Parallel Stream): parallelStream()은 멀티코어를 쓰지만, 데이터가 적거나 순서가 중요하면 오히려 더 느리고 위험합니다. 신중히 써야 합니다.

1. Optional은 반환 타입으로만: 필드 값이나 파라미터로 Optional을 쓰는 건 설계 의도에 어긋나며 성능 저하를 유발합니다.

## 6. Self-Quiz (정리용 질문)

스트림은 원본 리스트의 요소를 정렬하면 원본도 정렬될까? (정답: 아니오)

map과 flatMap의 차이는 무엇인가? (중첩 구조를 펴주는 과정의 이해)

왜 Optional.get()을 바로 호출하는 것을 지양해야 할까?

## 1. 패러다임의 전환: 명령형(How)에서 선언형(What)으로

과거의 자바가 "어떻게(How) 루프를 돌릴까"에 집중했다면, 현대 자바는 **"무엇(What)을 하고 싶은가"**에 집중합니다.

- 명령형: for문을 돌면서 if로 거르고, 새 리스트에 add한다. (코드가 길고 의도가 한눈에 안 들어옴)

- 선언형(Stream): "필터링하고, 변환해서, 수집해라." (비즈니스 로직이 한눈에 보임)

## 2. 람다 식 (Lambda Expression): "함수를 변수처럼"

람다는 익명 함수를 만드는 방식이지만, 본질은 **"행위(Behavior)를 파라미터로 전달하는 것"**입니다.

## 2.1 함수형 인터페이스 (Functional Interface)

- 람다를 쓰려면 단 하나의 추상 메서드만 가진 인터페이스가 필요합니다.

- @FunctionalInterface 어노테이션을 붙여 컴파일 타임에 체크하는 것이 관례입니다.

- 자주 쓰는 인터페이스: Predicate<T> (조건 체크), Consumer<T> (소비), Function<T, R> (변환).

## 3. 스트림 API (Stream API): "데이터의 흐름"

스트림은 데이터 소스(컬렉션, 배열 등)를 추상화하여 연속적으로 처리하는 도구입니다. 원본 데이터를 변경하지 않는다는 점이 가장 중요합니다.

## 3.1 스트림의 3단계 파이프라인

1. 생성 (Source): list.stream()

1. 중간 연산 (Intermediate): filter, map, sorted, distinct (결과가 다시 스트림이므로 연결 가능)

1. 최종 연산 (Terminal): collect, forEach, count, anyMatch (스트림을 닫고 결과를 반환)

## 3.2 💡 실무 체감 예시 (물류 시스템 시나리오)

> 상황: 배차 대기 중인 주문 리스트에서 "서울" 지역 주문만 골라 주문 ID 리스트로 변환해야 함.

- Old 방식 (for-each):

Java

```java
List<Long> seoulOrderIds = new ArrayList<>();
for (Order order : orders) {
    if ("SEOUL".equals(order.getRegion()) && "PENDING".equals(order.getStatus())) {
        seoulOrderIds.add(order.getId());
    }
}          // 결과 수집
```

- Modern 방식 (Stream):

```java
List<Long> seoulOrderIds = orders.stream()
    .filter(o -> "SEOUL".equals(o.getRegion())) // 조건 필터링
    .filter(o -> "PENDING".equals(o.getStatus())) // 연속 필터링 가능
    .map(Order::getId)                           // ID만 추출 (Method Reference)
    .collect(Collectors.toList());               // 결과 수집
```

## 4. Optional: "Null과의 전쟁 종식"

NullPointerException(NPE)은 개발자의 영원한 숙제입니다. Optional은 값이 "있을 수도 있고 없을 수도 있음"을 명시적으로 표현하는 컨테이너입니다.

## 4.1 잘못된 사용 vs 올바른 사용

- Bad: if (opt.isPresent()) { return opt.get(); } (기존 null 체크와 다를 게 없음)

- Good: 함수형 메서드 활용

- opt.orElse("Default"): 없으면 기본값 반환.

- opt.orElseThrow(): 없으면 예외 던지기.

- opt.ifPresent(v -> ...): 있을 때만 로직 실행.

## 5. 공부하며 느낀 Deep Insight (Human-like Notes)



## 🚀 지연 연산 (Lazy Evaluation)의 마법

스트림의 중간 연산은 최종 연산이 호출되기 전까지 절대 실행되지 않습니다. * "100만 개 데이터 중 filter 후 findFirst"를 하면, 100만 개를 다 거르는 게 아니라 조건을 만족하는 첫 번째 데이터를 찾는 순간 멈춥니다. 성능 최적화가 자동으로 일어나는 지점입니다.

## ⚠️ 주의할 점 (Pitfalls)

1. 가독성 vs 복잡성: 스트림이 무조건 좋은 건 아닙니다. 로직이 너무 복잡해지면 오히려 for문보다 읽기 힘들어집니다. "한 줄이 너무 길어지면 끊어가자."

1. 병렬 스트림(Parallel Stream): parallelStream()은 멀티코어를 쓰지만, 데이터가 적거나 순서가 중요하면 오히려 더 느리고 위험합니다. 신중히 써야 합니다.

1. Optional은 반환 타입으로만: 필드 값이나 파라미터로 Optional을 쓰는 건 설계 의도에 어긋나며 성능 저하를 유발합니다.

## 6. Self-Quiz (정리용 질문)

스트림은 원본 리스트의 요소를 정렬하면 원본도 정렬될까? (정답: 아니오)

map과 flatMap의 차이는 무엇인가? (중첩 구조를 펴주는 과정의 이해)

왜 Optional.get()을 바로 호출하는 것을 지양해야 할까?


