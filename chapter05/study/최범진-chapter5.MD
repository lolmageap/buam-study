# 빠른 CPU를 위한 설계 기법

## **클럭**

클럭 속도는 CPU가 명령어 사이클을 반복하는 속도를 나타내며, 클럭 속도가 높을수록 CPU와 연계된 다른 부품들도 더 빠르게 작동한다. 클럭 속도는 헤르츠(Hz) 단위로 측정되며, 클럭의 반복 주기를 의미한다. 오버클럭킹을 통해 고성능이 필요한 경우에 클럭 속도를 일시적으로 증가시킬 수 있고, 필요하지 않을 때는 클럭 속도를 낮추어 전력 소모와 발열을 관리할 수 있다.

### **코어와 멀티코어**

코어는 CPU 내부에서 명령어를 실행하는 기본 단위이며, 멀티코어 CPU는 여러 코어를 내장하여 복수의 작업을 동시에 처리할 수 있다. 코어가 많을수록 병렬 처리 능력이 향상되지만, 작업이 코어 간에 적절히 분배되지 않으면 예상한 만큼의 성능 향상을 보지 못할 수도 있다.

## **스레드와 멀티 스레드**

### **하드웨어적 스레드**

하드웨어적 스레드는 코어가 동시에 처리할 수 있는 명령어 단위를 의미하며, 이를 통해 하나의 코어에서 여러 개의 스레드를 동시에 처리하는 멀티스레드 CPU가 구현된다. 레지스터를 복수로 갖춤으로써 하나의 코어에서 동시에 여러 명령어를 처리할 수 있으며, 이로 인해 하나의 코어가 논리적으로 여러 프로세서처럼 작동할 수 있다.

### **소프트웨어적 스레드**

소프트웨어적 스레드는 하나의 프로그램 내에서 독립적으로 실행되는 실행 흐름의 단위이다. 멀티스레딩을 지원하는 프로그래밍 언어와 운영체제를 통해, 하나의 응용 프로그램이 여러 작업을 동시에 처리할 수 있다.

# 명령어 병렬 처리 기법

명령어 병렬 처리 기법은 CPU가 명령어를 동시에 처리하여 효율적으로 작동할 수 있게 하는 기술이다.

## 명령어 파이프라인

명령어 파이프라인은 다수의 명령어가 겹치지 않는 여러 단계에서 동시에 처리되도록 함으로써, 한 단계가 완료될 때마다 다음 단계로 명령어를 전달하여 연속적으로 처리할 수 있게 한다. 이는 각 단계에서 동시에 다른 명령어를 처리할 수 있기 때문에, 전체적인 처리 속도를 크게 향상시킬 수 있다. 예를 들어, 한 명령어가 인출되는 동안 다른 명령어는 실행되고 또 다른 명령어의 결과는 저장될 수 있다.

## 파이프라이닝 위험

파이프라이닝이 특정 상황에서는 성능 향상에 실패하는 경우가 생긴다.

### 데이터 위험

- 다음 명령어가 이전 명령어의 결과에 의존할 때 발생
- 연산 결과가 저장되기 전에 그 결과를 필요로 하는 다른 명령어가 실행되는 경우, 파이프라인이 일시적으로 멈추어야 한다.

```java
R1 = R2 + R3
R4 = R1 + R5 => R1 에 의존하기에 연산이 실행 중이라면 예상한 결과와 다른 결과가 생김
```

### 제어 위험

- 분기 등으로 인한 프로그램 카운터의 갑자스러운 변화에 의해 발생
- 프로그램이 어디로 분기할지 미리 예측한 후 그 주소를 인출하는 분기 예측 으로 위험 최소화

### 구조적 위험

- 명령어들을 겹처 실행하는 과정에서 서로 다른 명령어가 동시에 ALU, 레지스터 등과 같은 CPU 부품을 사용하려고 할 때 발생
- 자원 위험이라고도 부른다.

## 슈퍼스칼라

슈퍼스칼라는 여러개의 명령어 파이프라인을 통해 매 클럭 주기마다 여러 명령어를 동시에 실행할 수 있는 CPU 설계를 의미한다. 이는 이론적으로 처리 속도를 높일 수 있지만, 실제 성능은 파이프라인 위험과 같은 다른 요인에 의해 제한될 수 있다.

## 비순차적 명령어 처리(OoOE)

명령어들을 그들이 코드 상에서 나타나는 순서대로 처리하지 않고, 의존성이 없는 명령어부터 먼저 처리함으로써 효율성을 높이고 파이프라인 지연을 줄이는 기술

# **CISC와 RISC**

## **명령어 집합 구조(ISA)**

- CPU가 이해할 수 있는 명령어들의 모음으로, CPU의 언어라 할 수 있다.

## **CISC (Complex Instruction Set Computing)**

- 다양하고 복잡한 명령어를 특징으로 하는 CPU 설계 방식이다.
- 가변 길이 명령어를 활용하여 상대적으로 적은 수의 명령어로도 프로그램을 실행할 수 있다.
- **장점**
    - 초기 메모리 용량이 제한적인 환경에서 효율적인 설계가 가능하여 인기가 많았다.
- **단점**
    - 명령어의 복잡성과 다양성으로 인해 실행 시간이 불규칙하다.
    - 명령어 실행에 여러 클럭 사이클이 필요하며, 이로 인해 파이프라이닝이 비효율적이다.

## **RISC (Reduced Instruction Set Computing)**

- 짧고 표준화된 명령어를 사용하며 대부분의 명령어가 한 클럭 사이클 내외로 실행된다.
- 고정 길이 명령어를 활용하며, 레지스터를 주로 사용하는 연산이 많고 범용 레지스터도 많으나 사용 가능한 명령어 수는 CISC보다 적다.
- 메모리 접근 명령어는 로드(load)와 스토어(store)로 제한하여 단순화하고 최소화한다.
- **장점**
    - 명령어 파이프라이닝을 최적화하여 높은 처리 효율성을 제공한다.
- **단점**
    - 더 많은 명령어를 필요로 하여 프로그램이 길어질 수 있다.
    - 하드웨어 최적화에 대한 요구가 높아질 수 있다.
