---
layout: post
title: "[CS] Object-Oriented Programming & SOLID Principles"
date: 2026-03-26 00:00:00 +0900
categories: [CS]
tags:
  - CS공부
---

# [CS] Object-Oriented Programming & SOLID Principles

객체 지향은 단순히 변수와 함수를 묶는 것이 아니라, 객체 간의 메시지 송수신을 통해 시스템을 구축하는 것입니다.

---

## 정리

## 1. 객체 지향의 본질: "책임과 협력"

객체 지향은 단순히 변수와 함수를 묶는 것이 아니라, 객체 간의 메시지 송수신을 통해 시스템을 구축하는 것입니다.

- 자율적 객체: 객체는 스스로의 상태를 관리하고, 외부의 요청에 어떻게 응답할지 스스로 결정해야 함.

- 추상화(Abstraction)의 두 얼굴: 1. 구체적인 사물들의 공통점을 뽑아내는 것 (예: 사과, 바나나 → 과일).
2. 불필요한 세부 사항을 제거하고 핵심만 남기는 것.

## 2. SOLID 원칙의 실무적 해석 (Deep Interpretation)

### ① SRP: 단일 책임 원칙 (Single Responsibility)

- 판단 기준: "어떤 코드를 변경해야 할 때, 그 영향이 어디까지 미치는가?"

- 실무 Tip: 클래스 이름이 Manager, Util, Service처럼 너무 포괄적이면 SRP를 위반할 확률이 높습니다. UserValidator, UserPasswordHasher처럼 구체적으로 쪼개세요.

### ② OCP: 개방-폐쇄 원칙 (Open-Closed)

- 핵심 기술: 인터페이스(Interface)와 상속.

- 적용 사례: JDBC 드라이버. 자바 애플리케이션은 동일한 DB 접근 코드를 사용하지만, 드라이버만 교체하면 MySQL, Oracle, PostgreSQL 등 어떤 DB와도 연결됩니다. 애플리케이션 코드는 수정(Closed)하지 않고, DB 지원은 확장(Open)된 사례입니다.

### ③ LSP: 리스코프 치환 원칙 (Liskov Substitution)

- 주의사항: 단순히 문법적 컴파일 에러가 안 나는 것이 아니라, **부모 클래스의 규약(계약)**을 지켜야 합니다.

- Bad Example: Bird 클래스에 fly() 메서드가 있는데, 이를 상속받은 Penguin 클래스에서 "저는 못 날아요"라며 예외(Exception)를 던지면 LSP 위반입니다. (날 수 있는 새만 fly()를 가져야 함)

### ④ ISP: 인터페이스 분리 원칙 (Interface Segregation)

- 핵심: "덩치 큰 인터페이스 하나보다, 구체적인 인터페이스 여러 개가 낫다."

- 실무 Tip: 스프링 프레임워크의 인터페이스들을 보면 Serializable, Cloneable처럼 아주 작게 쪼개져 있는 것을 볼 수 있습니다. 필요한 기능만 골라 조합(Multi Implements)하기 위함입니다.

### ⑤ DIP: 의존 역전 원칙 (Dependency Inversion)

- 핵심: "변하기 쉬운 것(구현체)에 의존하지 말고, 변하지 않는 것(인터페이스)에 의존하라."

- 비유: 콘센트(인터페이스)에 의존해야지, 특정 브랜드의 가전제품 선에 직접 연결하면 안 되는 것과 같습니다.

## 3. 심화 주제: "상속보다는 합성을 사용하라" (Composition over Inheritance)

많은 초보 개발자가 재사용을 위해 '상속(Extends)'을 쓰지만, 실무에서는 '합성(Composition)'을 권장합니다.

- 상속(Inheritance)의 문제점: 부모와 자식 관계가 너무 강하게 결합됨(Strong Coupling). 부모 클래스의 작은 변경이 자식 모두에게 치명적인 버그를 일으킬 수 있음.

- 합성(Composition)의 장점: 클래스 내부에 다른 객체를 필드로 가지고 있는 방식. 실행 시점에 의존성을 교체할 수 있어 훨씬 유연함.

```java
// [합성 예시]
public class Robot {
    private MoveStrategy moveStrategy; // 인터페이스를 필드로 가짐 (합성)

    public void setMoveStrategy(MoveStrategy strategy) {
        this.moveStrategy = strategy; // 달리기 모드, 비행 모드 등을 자유롭게 교체
    }
}
```

## 4. 실전 디자인 패턴과의 연결 (Bridge to Design Patterns)

SOLID 원칙을 잘 지키면 자연스럽게 디자인 패턴으로 이어집니다.

- OCP/DIP를 지키면? → 전략 패턴(Strategy Pattern), 템플릿 메서드 패턴.

- SRP를 지키면? → 프록시 패턴(Proxy Pattern), 데코레이터 패턴.

## 5. 학습 포인트: "왜(Why)를 생각하기"

1. 인터페이스 없이 개발하면 어떤 일이 벌어지는가? (코드 수정의 지옥)

1. 왜 스프링은 모든 서비스 객체를 주입(DI)받아 사용하는가? (DIP를 지키기 위해)

1. 내가 진행했던 프로젝트에서 SRP를 가장 잘 지킨 클래스는 무엇인가?

