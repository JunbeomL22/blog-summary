# 이준범의 업무노트 - 블로그 포스트 요약

> 블로그: [https://jblee.dev](https://jblee.dev)
>
> AI 활용, 금융수학, 매매 시스템 설계에 대한 기술 노트 모음

---

## AI 활용

### 망분리 환경 AI 배포 시리즈

증권사 내부망과 같은 인터넷 단절(폐쇄망) 환경에서 로컬 LLM 서버를 구축하고, Claude Code / Mistral-Vibe 등 에이전트 코딩 도구를 활용하는 실전 가이드.

- [시리즈 소개](https://jblee.dev/posts/ai-intro/) — 망분리 환경 AI 배포 시리즈의 전체 구성과 목차. 모델 선택부터 실전 배포까지 총 12개 글로 구성된 시리즈 로드맵을 한눈에 볼 수 있습니다.

- [(1) 목표: 망분리 환경에서 에이전트 코딩](https://jblee.dev/posts/ai-airgap-goal/) — 증권사 망분리(인터넷망/업무망) 구조의 현실과 제약을 설명합니다. 업무망에선 인터넷이 안 되니 API 호출이 불가능하고, 결국 로컬에서 돌아가는 AI가 필요합니다. 3,000만~6,000만 원 예산으로 Sonnet 4.0급 성능의 환경을 구축하는 것이 시리즈의 최종 목표입니다.

- [(2) 모델 선택과 메모리 요구량](https://jblee.dev/posts/ai-airgap-model-1/) — SWE-bench, Terminal Bench 등 벤치마크 분석과 함께, 커뮤니티 평가가 항상 정확하지 않을 수 있다는 점을 Devstral-small-2와 GLM-4.5-air의 실사용 경험으로 보여줍니다. 벤치마크 성능 못지않게 VRAM 요구량이 모델 선택의 핵심 제약이며, KV Cache 메모리 산정법도 함께 다룹니다.

- [(3) 추론 속도와 MoE 모델 이해](https://jblee.dev/posts/ai-airgap-model-2/) — Prefill(입력 병렬 처리)과 Decode(출력 순차 생성) 단계별 TPS 개념을 설명합니다. 실사용에서 체감 속도는 Decode 단계에 달려 있고 이는 GPU 메모리 대역폭에 크게 좌우됩니다. MoE 모델이 Dense 모델 대비 실제 활성화 파라미터가 적어 동일 VRAM에서 더 빠른 추론이 가능한 이유도 다룹니다.

- [(4) vLLM, Ollama, llama.cpp](https://jblee.dev/posts/ai-airgap-serving/) — 서빙 프레임워크 비교. 개인 테스트엔 Ollama가 간편하고, 여러 사용자가 동시에 사용하는 프로덕션 환경에는 PagedAttention으로 KV Cache를 효율적으로 관리하는 vLLM이 표준적인 선택입니다. SGLang, llama.cpp까지 각각의 특징과 망분리 환경 적합성을 정리합니다.

- [(5) 양자화와 정밀도 선택 가이드](https://jblee.dev/posts/ai-airgap-quantization/) — 모델 가중치를 FP32에서 FP16, FP8, INT4 등 낮은 정밀도로 변환해 메모리를 절약하는 양자화 기법을 설명합니다. FP16 vs BF16의 차이부터 FP8, AWQ, GPTQ, GGUF까지 다양한 방식의 특징과 서빙 프레임워크별 호환성을 정리하여, 메모리-성능 트레이드오프의 판단 기준을 제시합니다.

- [(6) Claude Code Router 설정](https://jblee.dev/posts/ai-airgap-ccrcode/) — Claude Code가 사용하는 Anthropic API 형식과 vLLM의 OpenAI API 형식 간의 차이를 claude-code-router로 해결합니다. 인터넷 환경에서 OpenRouter로 먼저 테스트한 뒤, 의존성 패키지를 오프라인으로 묶어 망분리된 Windows 환경에 배포하는 전체 과정을 단계별로 다룹니다.

- [(7-1) 실전 배포: 하드웨어/소프트웨어 설계](https://jblee.dev/posts/ai-airgap-requirement-roadmap/) — 8명 팀에 AI 서비스를 제공하기 위한 구체적인 GPU 서버 사양(RTX Pro 6000 Blackwell 96GB x 2 등)과 소프트웨어 스택을 설계합니다. KV Cache 메모리 계산을 통해 동시 사용자 시나리오별 메모리 요구량을 분석하고, `--swap-space` 옵션으로 KV Cache를 CPU RAM에 오프로드하는 방법도 제시합니다.

- [(7-2) 실전 배포: Docker 환경 구축](https://jblee.dev/posts/ai-airgap-docker/) — vLLM은 새 모델 지원을 위해 빈번히 업데이트되며 CUDA 버전과 복잡한 의존성이 있어 Docker가 사실상 필수입니다. Docker 이미지를 pull → save → 전송 → load 방식으로 폐쇄망에 배포하는 과정과 NVIDIA Container Toolkit 오프라인 설치를 다룹니다.

- [(7-3) 실전 배포: vLLM 서버 설치](https://jblee.dev/posts/ai-airgap-vllm/) — 인터넷망에서 vLLM Docker 이미지와 HuggingFace 모델을 다운로드해 tar 파일로 저장하고, 망간 자료 전송 시스템을 통해 폐쇄망으로 옮겨 서비스를 구동합니다. nightly 버전 이미지의 장단점, 주요 vLLM 서빙 옵션, 헬스 체크 방법까지 실전에 바로 적용 가능한 내용입니다.

- [(7-4) 실전 배포: Open-WebUI 설치](https://jblee.dev/posts/ai-airgap-openwebui/) — vLLM과 연동해 폐쇄망에서 ChatGPT 유사 웹 인터페이스를 제공하는 Open-WebUI를 Docker로 설치합니다. RAG 기능을 위한 임베딩 모델(`all-MiniLM-L6-v2`) 오프라인 준비와 `OFFLINE_MODE=true` 실행 설정까지 포함합니다.

- [(7-5) 실전 배포: Mistral-Vibe 설치](https://jblee.dev/posts/ai-airgap-mistral-vibe/) — Claude Code와 유사한 CLI 에이전트 코딩 도구 Mistral-Vibe를 각 개발자 PC에 설치합니다. `pip download`로 50~60개 의존성 패키지를 미리 내려받아 폐쇄망으로 옮긴 뒤 Python 가상환경에서 오프라인 설치하고, vLLM 서버와 연동하는 과정을 다룹니다.

- [(7-6) 실전 배포: Qwen-Code 설치](https://jblee.dev/posts/ai-airgap-qwencode/) — Alibaba의 CLI 코딩 에이전트 Qwen-Code를 Node.js 기반으로 폐쇄망에 설치합니다. `npm install`로 의존성을 로컬에 내려받아 압축 후 전송하고, `npm link`로 전역 등록합니다. vLLM 실행 시 `--tool-call-parser qwen3_xml` 옵션으로 tool calling을 활성화하는 방법도 설명합니다. 시리즈 마지막 편.

---

## 금융수학

실무 중심의 금융수학 적용 사례. 데이터 분석 기법, 파생상품 가격 결정, 변동성 관리 등을 다룹니다.

- [시리즈 소개](https://jblee.dev/posts/quant-intro/) — 금융수학 시리즈의 전체 구성을 안내합니다. 차익거래 계산, 옵션 가격 결정, 변동성 관리 등 실무 중심의 금융수학 적용 사례를 다룰 예정입니다.

### 데이터 분석

- [EWMA & EWMS - 메모리 O(1)과 Irregular Interval 처리](https://jblee.dev/posts/ewma/) — EWMA(지수가중이동평균)는 직전 평균값만 재활용하는 재귀식으로, 메모리 O(1)만으로 지수 가중 평균을 계산합니다. 피쳐가 많아질수록 캐시 압박이 커지는 매매 시스템에서 왜 메모리 효율이 중요한지 설명하고, 반감기(half-life)를 도입해 데이터가 불규칙한 간격으로 들어오는 실제 매매 환경에도 동일한 프레임워크를 적용하는 방법을 다룹니다. EWMS(지수가중이동합)도 함께 정의합니다.

- [피쳐 정규화 - EWMA 기반 Z-Score와 스케일 안정화](https://jblee.dev/posts/normalizer/) — 트레이딩 피쳐의 절대값만으로는 크고 작음을 판단하기 어려우므로 "평소 대비 얼마나 벗어났는가"로 변환하는 정규화가 필요합니다. 트레이딩에서 표준인 rolling z-score를 EWMA로 구현해 메모리 O(1)을 달성하는 방법과, 분모(표준편차)가 0에 수렴할 때의 스케일 안정화 기법(워밍업 카운트, 출력 클리핑, 장기 변동성 혼합)을 설명합니다. Min-max 정규화가 금융 데이터에 부적합한 이유도 다룹니다.

- [(Feature 1) Order Flow Imbalance](https://jblee.dev/posts/ofi/) — OFI는 호가창 최우선 호가의 변화를 추적하여 매수/매도 압력을 하나의 스칼라로 수치화한 지표입니다. Cont et al. (2010) 논문의 단순화된 호가창 모델에서 OFI와 가격 변화의 선형 관계를 유도하고, 실제 사용하는 수식을 매수호가 상승/유지/하락 세 경우로 나누어 설명합니다. EWMS를 적용해 메모리 O(1)로 계산하는 방법도 포함합니다.

---

## 매매 시스템

DMA(Direct Market Access) 관점에서 매매 시스템의 인프라, OMS 설계, 저지연 최적화를 다룹니다. 한국거래소(KRX) 기준, 코드는 Rust로 작성.

- [시리즈 소개](https://jblee.dev/posts/trading-systems-intro/) — 인프라, OMS, 최적화 세 파트의 전체 목차와 설계 철학을 소개합니다. 매매 시스템 설계에는 정답이 없으며, 속도가 중요한 DMA 관점에서 설명한다는 점을 미리 밝힙니다.

### 인프라

- [(1) HTS/MTS 전산 매매 과정](https://jblee.dev/posts/infra-intro/) — HTS/MTS로 매매할 때 시세가 KRX → 코스콤 → 증권사 FEP → 시세서버 → BEP → 클라이언트로 전달되는 전체 흐름을 다이어그램으로 설명합니다. 이 흐름을 이해하면 어디를 단축하거나 개선할 수 있는지 파악할 수 있으며, 매매 시스템 설계의 출발점입니다.

- [(2) FEP와 BEP](https://jblee.dev/posts/fep-bep/) — 증권사에서 자주 쓰는 FEP/BEP라는 용어가 1960~70년대 IBM 메인프레임 시대에서 유래했음을 설명합니다. 현대 금융권에서는 FEP가 거래소/코스콤 등 대외 기관과 연동하는 게이트웨이 서버를 의미하도록 변화했으며, 왜 아직도 이 이름을 쓰는지 역사적 맥락을 짚어줍니다.

- [(3) 코로케이션과 빛의 속도](https://jblee.dev/posts/collocation/) — 빛의 속도로도 물리적 거리는 극복할 수 없으므로, 거래소에 최대한 가깝게 서버를 배치하는 코로케이션이 저지연 매매의 핵심 인프라 전략입니다. 광케이블, 마이크로웨이브 기반 서울-부산 간 RTT를 실제로 계산하고, KT IDC 코로케이션의 효과를 비교합니다.

- [(4) 미니원장](https://jblee.dev/posts/mini-ledger/) — 일반 원장은 매 주문마다 DB 조회를 반복해 네트워크/디스크 I/O 지연이 누적됩니다. 미니원장은 DMA에 필요한 계좌 ID, 포지션, 주문 한도 등 최소한의 정보만 메모리에 유지해 시스템 홉 수와 지연을 대폭 줄이는 경량화 원장입니다. 체결 후 정산(후처리) 과정도 함께 설명합니다.

- [(5) Kernel Bypass](https://jblee.dev/posts/kernel-bypass/) — 일반 패킷 수신은 NIC → 커널 드라이버 → TCP/IP 스택 → 애플리케이션 순으로 인터럽트와 컨텍스트 스위칭이 발생합니다. Kernel Bypass는 커널을 거치지 않고 NIC가 유저 공간 버퍼에 직접 DMA 전송하고 애플리케이션이 폴링으로 읽어 지연과 jitter를 최소화합니다. OpenOnload, libvma, DPDK와 AMD/NVIDIA Smart NIC을 비교합니다.

- [(6) 오버클럭 서버](https://jblee.dev/posts/overclock-server/) — HFT용 오버클럭 서버는 단순히 클럭을 올리는 게 아니라, 주파수 고정, C-state/P-state 비활성화, 고품질 칩 선별(silicon lottery)까지 포함합니다. C-state 전환 지연을 없애 jitter를 줄이는 원리와 Xenon(호주), Blackcore(영국) 등 대표 업체의 제품을 비교합니다.

### OMS (Order Management System)

- [(1) 인트로](https://jblee.dev/posts/oms-intro/) — OMS 시리즈의 전체 구성을 안내합니다. 정수 기반 가격 관리, 시세 수신, 오더북 설계 등 각 항목의 개요와 인프라/최적화 파트와의 연관 관계를 설명합니다.

- [(2) 중요한 숫자 관리는 정수로](https://jblee.dev/posts/oms-integer-price/) — IEEE 754 부동소수점은 이진수 기반이라 `0.1 + 0.2 = 0.30000000000000004` 같은 오차가 발생합니다. 수백만 건 처리 시 오차가 누적되므로, OMS 내부의 가격/잔고 등 중요한 숫자는 정수(fixed-point)로 관리해야 비교 연산의 신뢰성과 HashMap 키 사용의 안전성을 확보할 수 있습니다.

- [(3) 시세 수신](https://jblee.dev/posts/receive-data/) — 한국거래소 시세는 코스콤 MDCS를 통해 배포되며, 접속표준서 엑셀 파일에 메시지 포맷이 정의되어 있습니다. 파생 호가 메시지를 예시로 종목코드, 호가, 잔량, 건수 등 각 필드 구조를 상세히 보여주어 실제 시세 파싱 구현에 필요한 기초 지식을 익힐 수 있습니다.

- [(4) 오더북이 필요한 이유](https://jblee.dev/posts/why-orderbook/) — 가격/수량만 담는 단순 호가창(snapshot)이 아닌, 각 가격 레벨의 주문 순서까지 담는 L3 오더북이 왜 필요한지 설명합니다. 큐에서 내 주문의 위치(queue position)를 추정해 체결 품질을 판단하거나, 주문 단위로 백테스트를 재구성할 때 반드시 필요한 구조입니다.

- [(5) 오더북 구성](https://jblee.dev/posts/orderbook-implementation/) — Level(VecDeque로 주문 큐), Ladder(BTreeMap으로 정렬된 가격 레벨), cache(AHashMap으로 주문 ID → 가격 O(1) 조회) 3계층 자료구조로 오더북을 설계합니다. 표준 HashMap 대신 AHashMap을 사용해 해시 연산을 2~5배 빠르게 하는 등 자료구조 선택 이유를 하나씩 설명합니다.

### 최적화

- [최적화 목록](https://jblee.dev/posts/optimization-intro/) — 최적화 파트의 전체 목차. 메모리 얼라인먼트, 비트 패킹, 저지연 로깅, 코어 격리 네 가지 주제를 다룹니다.

- [(1) 메모리 얼라인먼트와 병목](https://jblee.dev/posts/memory-alignment/) — 데이터를 N의 배수 주소에 배치해 CPU가 한 번의 메모리 접근으로 읽을 수 있게 하는 기법입니다. 구조체 필드 순서를 잘못 배치하면 컴파일러가 패딩을 삽입해 메모리를 낭비하므로, 큰 타입부터 내려가는 순서로 배치하면 패딩을 줄일 수 있습니다. L1 캐시와 DRAM의 레이턴시 차이(75배)도 함께 다룹니다.

- [(2) 비트 패킹으로 주문 ID 최적화](https://jblee.dev/posts/order-id/) — 구조체로 주문 ID를 표현하면 패딩으로 9바이트가 16바이트가 되고 해시 비용도 두 배로 늘어납니다. u64 하나에 상위 8비트는 거래소(venue), 하위 56비트는 주문 번호로 비트 패킹하면 8바이트로 줄이고 단일 해시 연산으로 처리할 수 있습니다.

- [(3) 저지연 로깅](https://jblee.dev/posts/logger/) — HFT에서 로깅의 문자열 포맷팅, 타임스탬프 생성, I/O는 주문 경로에 지연을 누적시킵니다. 컴파일 타임 레벨 체크로 불필요한 로그 코드를 바이너리에서 완전히 제거하고, 클로저 기반 지연 평가와 전용 로거 스레드로 로깅 오버헤드를 단계적으로 줄이는 방법을 설명합니다.

- [(4) 코어 격리](https://jblee.dev/posts/core-isolation/) — Core affinity만으로는 커널이 조용히 설정을 덮어쓸 수 있어 스레드가 코어를 이탈할 수 있습니다. isolcpus로 OS 스케줄러의 간섭을 차단하고 스레드를 전용 코어에서 실행해야 L1/L2 캐시를 보존하고 나노초 단위 지연을 안정적으로 유지할 수 있습니다. C, Rust, Java에서의 설정 방법도 함께 다룹니다.
