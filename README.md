# Job System
비동기 작업(Job)과 작업 큐(JobQueue), 글로벌 큐(GlobalQueue) 기반의 작업 스케줄러입니다. 
---

## 주요 구성

### Job
- `std::function<void()>` 타입 콜백을 래핑하는 클래스
- 멤버 함수 호출 지원 (소유 객체 + 멤버 함수 포인터 + 인자)
- `Execute()` 호출 시 콜백 실행

### JobQueue
- 작업(Job)을 큐에 저장 후 순차 실행
- `DoAsync()`로 람다 또는 멤버 함수 작업 예약 가능
- `DoTimer()`로 지정 시간 후 작업 예약 가능
- 작업 개수 카운터를 통해 실행 스케줄 관리

### GlobalQueue / GlobalDBQueue
- 여러 JobQueue를 스레드 안전하게 관리하는 글로벌 큐

### LockQueue (템플릿)
- 내부에 `std::queue`를 사용하고 뮤텍스로 동기화한 스레드 안전 큐
--- 
# flowchart
```mermaid
   A[작업 생성 (Job 생성)] --> B[작업 큐에 작업 추가 ]
    B --> C{작업 큐 실행 중?}
    C -- 아니오 --> D[즉시 작업 실행 ]
    C -- 예 --> E[글로벌 큐에 작업 큐 등록]
    D --> F[작업 실행 루프 시작]
    F --> G[큐에서 작업 모두 가져오기]
    G --> H[각 작업 Execute 호출]
    H --> I{남은 작업이 있나?}
    I -- 예 --> E
    I -- 아니오 --> J[작업 종료]
    E --> K[다른 스레드에서 작업 실행 대기]
```
------
# 사용 예시
![image](https://github.com/user-attachments/assets/d0868f19-d437-42ae-9975-c581e39df2d7)
- jobQueue를 상속받은 클래스에서 DoAsnyc를 통해 해야할 일을 job으로 만들어 queue에 넣어 실행.
