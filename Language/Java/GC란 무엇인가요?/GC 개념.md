## 🗑️ GC(Garbage Collector)

프로그램에서 더 이상 사용하지 않는 객체를 자동으로 메모리에서 제거하는 기술

> 개발자가 명시적으로 해제하지 않아도, GC가 알아서 회수해줌.
주로 Java, Kotlin, Scala, C#, Node.js 등에서 사용되고,
C, C++은 수동으로 maloc/free 또는 new/delete 사용.
> 

## 🤷🏻‍♀️ 등장 배경(왜 나왔을까?)

- C/C++ 같은 경우 개발자가 직접 메모리를 관리해야해서 실수하기 쉬웠음.
    - 메모리 누수(memory leak)
    - 해제 후 참조(use-after-free) 등
- 그래서 Java는 :”메모리 관리 자동화”를 목표로 GC 내장 → 안정성 → 생산성 증가

## ⚙️ GC의 기본 동작 방식(Java 기준)

1. 객체 생성
    1. JVM 힙(Heap)에 객체가 생성 됨.
2. 참조 유무 판단
    1. 더 이상 참조되지 않는 객체는 GC 대상이 됨.
3. GC 수행
    1. 해당 객체가 자동으로 제거됨. 이걸 Garbage Collection이라 부름

## ⛰️ Java의 메모리 구조와 GC 대상 영역

| 영역 | 설명 | GC 대상 여부 |
| --- | --- | --- |
| Heap | new 객체가 저장되는 공간 | ✅ GC 대상 |
| Stack | 메서드 호출, 지역 변수 | ❌ GC 대상 아님(메서드 끝나면 자동 해제) |
| Method Area | 클래스 정보, static 변수 | ❌ |
| Young Generation | 새로 생성된 객체들(Eden + Survior) | ✅ |
| Old Generation | 오래 살아남은 객체 | ✅ |
| PermGen / Metaspace | 클래스 메타데이터 저장 영역 | 일부만 |

## 🔩 Java GC 세부 구조(Generational GC 개념)

> 대부분 JVM은 세대별 GC(Gnerational GC)전략을 씀
> 

### Young GC (Minor GC)

- Eden → Survivor1 → Survivor2 → Old로 객체가 이동
- 새로 생성된 객체가 Eden에 있다가, 일정 시간 지나면 GC 수행

### Old GC (Major/Full GC)

- 오래 살아남은 객체가 Old 영역에 쌓이면 발생
- 속도가 느리고, STW(stop-the-world)시간 길어짐 → 성능에 민감

## 💼 GC알고즘 종류

| 이름 | 특징 | 비고 |
| --- | --- | --- |
| Serial GC | 단일 스레드 | 작은 애플리케이션용 |
| Parallel GC (패러럴) | 여러 스레드 | 기본값(자바 8) |
| CMS GC | 응답 속도 ⬆️ , STW ⬇️ | 자바 9 부터 |
| G1 GC | Young/Old 동시 관리 | 자바 9+의 기본 |
| ZGC / Shenandoah | STW 시간 매우 짧음 | Java 11+, 대용량 서비스용 |

## 🧠 면접에서 나올 수 있는 꼬리 질문

| **질문** | **요지** |
| --- | --- |
| GC가 언제 발생하나요? | Heap에 객체가 너무 많아지고, Eden 꽉 차면 Minor GC 발생 |
| GC 대상은 어떻게 판단하나요? | 참조 그래프(Reachability Analysis)를 통해 판단 |
| GC가 성능에 미치는 영향은? | Full GC는 STW 발생 → 응답 지연 |
| GC 튜닝 해본 적 있나요? | Heap 사이즈, GC 방식, JVM 옵션 조정한 경험 |
| GC 로그는 어떻게 확인하나요? | -Xlog:gc 또는 -XX:+PrintGCDetails 옵션 |
| Java에서 GC가 대상이 아닌 영역은? | Stack, Static 영역 등 |

## 📰 추가 용어 정리

### STW(Stop-The-World)

> JVM이 애플리케이션의 모든 스레드를 멈추고(GC 전용 스레드만 남기고), GC 작업을 수행하는 시점
> 
- 사용자가 서비스 요청했는데, JVM이 멈춰있어서 아무 응답도 못하면 장애로 직결될 수 있음.
- 따라서 알고리즘 튜닝과 같은 방법으로 해결할 수 있음.

- 추가 꼬리질문
    
    ### **✅ 1. GC의 Young / Old 영역 차이는 뭔가요?**
    
    **답변 요약**
    
    JVM 힙 메모리는 GC의 효율성을 위해 Young 영역과 Old 영역으로 나뉩니다.
    
    - **Young 영역**은 새롭게 생성된 객체들이 위치하는 곳입니다.
        
        대부분의 객체는 짧은 시간 안에 사라지므로, **빈번한 Minor GC**를 통해 빠르게 수거합니다.
        
        (Eden → Survivor → Old로 승격됨)
        
    - **Old 영역**은 Young에서 살아남은 장수 객체들이 옮겨진 공간입니다.
        
        여기서의 GC는 **Major GC (또는 Full GC)**로 비용이 크고 STW 시간이 길 수 있습니다.
        
    
    **예시로 추가**:
    
    웹 요청마다 생기는 DTO 객체는 Young 영역에서 생성되고, 캐시처럼 오랫동안 유지되는 객체는 Old 영역으로 이동하는 구조입니다.
    
    **꼬리 질문 대비**:
    
    - Minor GC와 Major GC의 STW 차이는?
    - Old 영역에 객체가 너무 많아지면 어떤 현상이 생기나요?
    
    ---
    
    ### **✅ 2. ZGC는 어떤 원리로 STW를 줄이나요?**
    
    **답변 요약**
    
    ZGC는 **색상 마킹(Coloring)**과 **메모리 리맵(Memory Remapping)** 기법을 통해 STW를 최소화합니다.
    
    - 참조되는 객체를 색깔로 표시하고, 참조 추적 작업(마킹)을 **병렬/비동기적으로 수행**합니다.
    - GC 작업 중에도 애플리케이션 스레드가 객체를 참조할 수 있으며, 이동 중인 객체는 **리맵 테이블을 통해 간접 참조**합니다.
    
    이 방식 덕분에 ZGC는 **STW 시간을 10ms 미만 수준으로 유지**할 수 있고, 대용량 힙에서도 저지연 성능을 보장합니다.
    
    **꼬리 질문 대비**:
    
    - ZGC와 G1의 차이점은?
    - ZGC는 어떤 환경에서 효과적인가요? (ex. 대규모 실시간 서비스)
    
    ---
    
    ### **✅ 3. GC 튜닝은 어떤 방식으로 진행하셨나요?**
    
    **답변 요약**
    
    GC 튜닝은 **GC 로그 분석 → 병목 구간 파악 → 적절한 GC 선택 및 힙 설정 조정** 순서로 진행했습니다.
    
    - Xlog:gc*,gc+heap=debug:gc.log 로그를 기록하고,
    - Full GC가 자주 발생하거나 STW가 1초 이상인 경우를 중심으로 분석했습니다.
    - XX:+UseG1GC → -XX:+UseZGC로 전환하고,
    - Xms, -Xmx, -XX:MaxGCPauseMillis, -XX:InitiatingHeapOccupancyPercent 등을 조정했습니다.
    
    **꼬리 질문 대비**:
    
    - G1GC 튜닝할 때 자주 조정하는 옵션은?
    - G1과 Parallel GC는 어떤 상황에서 선택하나요?
    
    ---
    
    ### **✅ 4. GC 로그를 어떻게 분석하나요?**
    
    **답변 요약**
    
    GC 로그는 GC 종류, 발생 시간, STW 시간, 힙 변화량을 확인하는 데 사용합니다.
    
    1. Xlog:gc* 또는 -verbose:gc로 로그 수집
    2. GCViewer, JClarity Censum, GCEasy 같은 도구로 시각화
    3. Full GC 빈도, 힙 사용량 추이, STW 시간 확인
    
    **예시**:
    
    “[Full GC (Allocation Failure) 4096M->2048M(8192M), 2.5 secs]” → STW 2.5초, 힙 절반이 수거됨
    
    **꼬리 질문 대비**:
    
    - GC 로그에서 Allocation Failure은 어떤 의미인가요?
    - 로그를 보고 힙이 부족하다고 판단하는 기준은?
    
    ---
    
    ### **✅ 5. GC가 잦을 경우 어떻게 대응하시나요?**
    
    **답변 요약**
    
    GC 빈도가 높다는 건 **객체 생성/제거가 과도**하거나, **힙 메모리가 부족**하다는 뜻입니다.
    
    대응 방법은 다음과 같습니다:
    
    - **객체 생명주기 개선**: 불필요한 객체 생성을 줄임 (StringBuffer, Object Pool 등)
    - **캐시 정리**: LRU 캐시 사용, 불필요한 Map 제거
    - **GC 튜닝**: 힙 사이즈 증가, 적절한 GC 알고리즘 선택
    - **로그/프로파일링 분석**: 메모리 누수 가능성 확인
    
    **꼬리 질문 대비**:
    
    - GC 빈도를 줄이기 위해 코드 레벨에서 할 수 있는 건?
    - WeakReference는 어떤 경우에 활용하나요?
    
    ---
    
    ### **✅ 6. GC 외에 메모리 누수 탐지는 어떻게 하시나요?**
    
    **답변 요약**
    
    메모리 누수 탐지를 위해 다음과 같은 툴과 방법을 사용합니다:
    
    - **VisualVM**: 실시간 힙 사용량, 클래스별 인스턴스 수, 힙 덤프 분석
    - **Eclipse MAT**: .hprof 힙 덤프 파일로 누수 경로 분석
    - **jmap -dump**, **jhat**: 커맨드라인 기반 힙 분석
    - **Heap Dump 시점**: OOM 발생 직전이나 Full GC 직후 수집
    
    **예시**:
    
    서버에서 OutOfMemoryError 발생 후 힙 덤프를 받아 MAT로 열어보니, 특정 List 객체가 계속해서 누적되는 것을 발견 → 캐시 로직 문제였음
    
    **꼬리 질문 대비**:
    
    - OutOfMemoryError가 발생하면 어떤 순서로 대응하나요?
    - Java에서 메모리 누수란 정확히 어떤 상황인가요?
