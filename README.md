flowchart TD
    A[사용자가 DoAsync 호출] --> B[Job 객체 생성]
    B --> C[JobQueue::Push 호출]
    C --> D{_jobCount == 0?}
    D -- Yes --> E[현재 스레드에서 Execute()]
    D -- No --> F[GlobalQueue에 등록]
    E --> G[JobQueue::Execute 루프 시작]
    G --> H[모든 Job 실행]
    H --> I{더 이상 Job 없음?}
    I -- Yes --> J[Execute 종료]
    I -- No --> G
```
