# 🧵 IOCP 기반 비동기 네트워크 서버 + Job 시스템

이 프로젝트는 Windows의 IOCP(I/O Completion Port)를 활용한 고성능 네트워크 서버 구조 위에  
**비동기 작업 처리 시스템(Job System)**을 결합하여 멀티스레드 환경에서 높은 처리 효율을 제공합니다.

---

## 📦 주요 구성 요소

### 📌 1. IOCP 핵심 구조

#### 🔧 `IocpObject`
- IOCP에서 사용할 **Key 역할 객체**
- `GetHandle()`과 `Dispatch()`를 순수 가상 함수로 정의
- IOCP 이벤트 발생 시 해당 객체가 이벤트를 처리

#### 🔧 `IocpCore`
- `CreateIoCompletionPort()`로 IOCP 핸들 생성
- `Register()`로 `IocpObject`와 IOCP 연결
- `Dispatch()` 루프에서 `GetQueuedCompletionStatus()`를 통해 이벤트 수신

#### 🔧 `IocpEvent`
- OVERLAPPED 기반 이벤트 정보 보관
- 완료된 이벤트를 `IocpObject::Dispatch()`로 전달

---

### 🌐 IOCP 처리 흐름

```mermaid
flowchart TD
    A[시작 - 서버 초기화] --> B[IOCP 핸들 생성(CreateIoCompletionPort)]
    B --> C[IocpObject 소켓 등록]
    C -->|소켓 핸들 전달| D[IOCP와 연결]
    D --> E[비동기 I/O 요청 시작]
    E --> F[OS 커널에서 I/O 완료 이벤트 대기]
    F --> G[GetQueuedCompletionStatus 호출]
    G --> H[IocpEvent 추출]
    H --> I[IocpObject::Dispatch 호출]
    I --> J[이벤트 처리 완료]
    J --> K[다음 이벤트 처리 또는 종료]
