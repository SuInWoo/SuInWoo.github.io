---
layout: post
title: "[CS] Java Deep Dive: JVM & Memory Management"
date: 2026-03-26 00:00:00 +0900
categories: [CS]
tags:
  - CS공부
---

# [CS] Java Deep Dive: JVM & Memory Management

- OS 독립성: 바이트코드(.class)는 JVM 위에서 실행되므로, 윈도우에서 짠 코드가 리눅스 서버에서도 동일하게 작동함.

---

## 정리

## 1. JVM의 핵심 역할: 가상화와 자동화

- OS 독립성: 바이트코드(.class)는 JVM 위에서 실행되므로, 윈도우에서 짠 코드가 리눅스 서버에서도 동일하게 작동함.

- 자동 메모리 관리: C/C++처럼 개발자가 free()를 호출할 필요 없이, JVM이 알아서 가비지(Garbage)를 치움.

## 2. JVM 내부 구조 상세 (Internal Architecture)

### 2.1 Class Loader: "클래스를 언제, 어떻게 가져오는가?"

- Lazy Loading (지연 로딩): 모든 클래스를 한꺼번에 올리지 않고, 실제 코드에서 참조될 때 로드함.

- 단계: Loading(읽기) → Linking(검증/준비) → Initialization(static 값 초기화).

### 2.2 Execution Engine: "바이트코드를 기계어로 바꾸는 두 가지 방식"

- Interpreter: 바이트코드를 한 줄씩 읽어서 실행. 빠르지만 반복 작업엔 비효율적.

- JIT (Just-In-Time) Compiler: 자주 실행되는 'Hot Method'를 찾아내서 통째로 기계어로 컴파일한 뒤 캐싱함.

- 예시: 반복문이 1만 번 돌아가면 인터프리터 대신 JIT가 개입해 실행 속도를 폭발적으로 높임.

## 3. Runtime Data Area: "데이터가 어디에 저장되는가?"

### 3.1 Stack vs Heap (가장 중요한 구분)

실제 코드를 예시로 보면 이해가 빠릅니다.

```java
public void study() {
    int age = 25;              // Stack에 저장 (기본 타입 변수)
    String name = new String("Gemini"); // 'name' 레퍼런스는 Stack, "Gemini" 객체는 Heap에 저장
}
```

### 3.2 Method Area (Static Area)

- 클래스 이름, 부모 클래스 이름, 메서드 데이터, static 변수가 저장됨.

- 프로그램 시작부터 종료까지 메모리에 남아있으므로 과도한 static 사용은 메모리 낭비를 초래함.

## 4. 가비지 컬렉션 (GC) 상세 메커니즘

### 4.1 Reachability: "누구를 지울 것인가?"

- Root Set: Stack의 변수, Static 변수 등에서 참조가 시작됨.

- Reachable: Root Set으로부터 연결 고리가 있는 객체. (생존)

- Unreachable: 연결 고리가 끊긴 객체. (GC 대상)

### 4.2 Heap의 세대별 관리 (Generational Strategy)

객체의 90%는 금방 쓰레기가 된다는 통계적 사실에 기반합니다.

1. Young Generation (Eden, S0, S1):

- 새로 만든 객체가 들어감. 꽉 차면 Minor GC 발생.

- 살아남은 객체는 Survivor 영역으로 이동하며 'Age'가 올라감.

1. Old Generation:

- 일정 Age(보통 15회) 이상 살아남은 '장수 객체'가 이동함.

- 여기가 꽉 차면 Major GC (Full GC) 발생. (이때 Stop-the-world 발생)

## 5. 실무 적용 및 트러블슈팅 (Use Case)

### 5.1 Stop-the-world (STW)

- 현상: GC를 실행하기 위해 JVM이 애플리케이션의 모든 스레드를 일시 정지시키는 것.

- 해결: 최신 GC(G1, ZGC)는 이 멈춤 시간을 밀리초(ms) 단위로 줄이는 방향으로 발전 중.

### 5.2 메모리 누수(Memory Leak) 사례

- 정적 컬렉션 사용: static List에 객체를 계속 넣고 비우지 않으면, GC가 "아직 참조 중이네?"라고 판단해 절대 치우지 않음. 결국 OutOfMemoryError(OOM) 발생.

- 리소스 미폐쇄: DB 연결(Connection)이나 파일 스트림을 열고 close() 하지 않으면 메모리가 점유된 상태로 남음.

