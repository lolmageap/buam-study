# Chapter4. CPU 구조
CPU 내부는 크게 ALU, 제어장치, 레지스터로 이루어져 있다.

## ALU (Arithmetic Logic Unit)
- ALU는 산술 및 논리 연산을 수행하는 핵심 구성 요소 중 하나.
- ALU는 주어진 입력에 대해 다양한 산술 및 논리 연산을 수행하고 결과를 출력한다.

#### ALU의 계산수행방식
- 레지스터로부터 피연산자를 받고 제어장치로부터 제어신호를 받는다.
- 예를들면 우리가 1 + 2 라는 계산을 수행할때 레지스터로부터 1,2의 값을 받고 제어장치로부터 덧셈이라는 신호를 받아서 계산하게 된다.
- 계산된 결과는 레지스터로 반환(저장)한다.
- 결과 외에도 부가정보를 플래그를 저장하는데 다음과 같은 플래그로 구성되어있다.
  - 부호플래그
    - 연산결과의 부호를 나타낸다. (양수, 음수)
  - 제로플래그
    - 연산결과가 0인지 나타낸다.
  - 캐리플래그
    - 연산결과 올림, 빌림이 발생했는지 나타낸다.
  - 오버플로우플래그
    - 연산결과가 레스터에 비해 너무 큰지 나타낸다.
  - 인터럽트플래그
    - 인터럽트가 가능한지 나타낸다.
  - 슈퍼바이저플래그
    - 커널모드인지 사용자모드인지 나타낸다. (커널 : 1, 사용자 : 0)

## 제어장치

#### 받아들이는 정보
- 명령어 레지스터로부터 해석할 명령어를 받는다.
- 플래그 레지스터로부터 플래그 값을 받는다.
- 입출력장치 등 주변장치로부터 제어신호를 받는다.

#### 내보내는 정보
- 레지스터로 정보 입력, 저장 등 행동 신호
- ALU로 수행할 연산에 대한 제어 신호
- CPU 외부 메모리로 입력, 저장, 읽기 등 행동 신호
- CPU 외부 입출력 장치로 사용, 정보저장, 테스트 등 행동 신호

#### 클럭(Clock)
- 부품, 명령어의 수행타이밍을 나타내는 시간 단위 (hz)

## 레지스터
- CPU 내부의 임시 저장장치
- 명령어나 데이터를 레지스터에 저장하고 관리한다.
- 여러가지 레지스터가 존재하는데 역할이 각각 존재한다.

#### 레지스터의 종류 및 역할
1. 프로그램 카운터 - 메모리에서 가져올 명령어의 주소를 저장
2. 명령어 레지스터 - 해석할 명령어를 저장
3. 메모리 주소 레지스터 - 메모리의 주소를 저장
4. 메모리 버퍼 레지스터 - 메모리와 주고 받을 값 저장
5. 플래그 레지스터 - 연산결과 또는 CPU 상태에 대한 부가적인 정보 저장
6. 범용 레지스터 - 주소, 버퍼 등 상황에 따른 유동적인 정보 저장
7. 스택 포인터 - 스택의 주소 정보를 저장
8. 베이스 레지스터 - 변위(기준) 주소 정보를 저장

#### 변위주소 지정 방식
연산 코드, 프로그램 카운터(레지스터의 값), 오퍼랜드의 값을 받아 주소를 계산한다.

- 상대 주소 지정 방식
  - 프로그램 카운터가 10, 오퍼랜드가 +3이라면 13의 주소를 나타낸다.
  - 프로그램 카운터가 10, 오퍼랜드가 -3이라면 7의 주소를 나타낸다.
- 베이스 레지스터 주소 지정 방식
  - 오퍼랜드 값과 베이스 레지스터의 값을 더해서 주소를 나타낸다.
  - 베이스레지스터가 100이고 오퍼랜드값이 50이라면 50만큼 100으로부터 떨어진 주소를 나타낸다.

## 인터럽트
- 인터럽트란 중단, 정지 신호를 이야기한다.
- 일반적인 CPU 사이클 흐름을 중간에 방해하여 잠시 끊고 빠른 처리가 필요하거나 먼저 처리해야할 때 발생한다.

#### 명령어 사이클
1. 인출사이클 - 메모리로부터 명령을 가져온다.
2. 실행사이클 - 메모리로부터 가져온 명령을 실행한다.
3. 간접사이클 - 추가로 메모리 접근이 필요한 경우 메모리에 접근해서 필요한 정보를 가져온다.

#### 동기 인터럽트
- CPU가 예기치 못한 상황을 접했을때 발생한다.
- 원하는 데이터가 없다거나 실행할 수 없거나 디버깅 시 발생한다.
- 폴트, 트랩, 중단, 소프트웨어 인터럽트의 종류가 있다.

#### 비동기 인터럽트
- 입출력장치에서 주로 발생하는 하드웨어 인터럽트
- 알림과 같은 역할을 수행한다.
- 완료, 입력 등과 같은 처리가 필요한 상황에 발생하는 것이 주요 상황이다.
- 입출력 작업 도중에 효율적인 명령어 처리를 위해서 필요하다.
  - 지속적으로 확인할 필요 없이 완료나 입력이 발생했을때 알림을 발생해 다른작업을 수행할 수 있게 한다.

#### 하드웨어 인터럽트 처리 순서
1. 입출력장치는 CPU에 인터럽트 요청 신호를 보낸다.
2. CPU는 실행사이클이 끝나고 새로운 명령어를 가져오기 전 인터럽트 요청신호 여부를 체크한다.
3. 인터럽트가 발생했다면 요청을 확인하고 인터럽트 플래그를 통해 현재 인터럽트를 받을 상황인지 체크한다.
4. 가능하다면 현재 작업을 백업한다.
5. 인터럽트 벡터를 참조해서 인터럽트 서비스루틴을 실행한다.
6. 인터럽트 서비스루틴 실행이 끝나면 백업했던 작업을 복구하고 이어서 실행한다.

- 인터럽트 요청신호 - 입출력장치 등이 인터럽트가 가능한지 신호를 발생시킨다.
- 인터럽트 플래그 - 현재 받을 수 있는 상황인지 신호를 가지고 있다. 이 값이 0으로 받지 않는다고 해도 막을 수 없는 경우도 있다.
- 인터럽트 서비스 루틴 - 메모리 내에서 실행될 인터럽트 프로그램 (각 요청지에 따른 가이드)
