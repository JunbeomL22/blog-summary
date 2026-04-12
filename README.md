# 이준범의 업무노트 - 블로그 포스트 요약

> 블로그: [https://jblee.dev](https://jblee.dev)
>
> AI 활용, 매매 시스템 설계에 대한 기술 노트 모음

---

## AI 활용

### 망분리 환경 AI 배포 시리즈

증권사 내부망과 같은 인터넷 단절(폐쇄망) 환경에서 로컬 LLM 서버를 구축하고, Claude Code / Mistral-Vibe 등 에이전트 코딩 도구를 활용하는 실전 가이드.

| # | 제목 | 요약 |
|---|------|------|
| - | [시리즈 소개](https://jblee.dev/posts/ai-intro/) | 망분리 환경 AI 배포 시리즈의 전체 구성과 목차. vLLM 기반 AI 서비스 구축 로드맵 |
| 1 | [목표: 망분리 환경에서 에이전트 코딩](https://jblee.dev/posts/ai-airgap-goal/) | 증권사 망분리(인터넷망/업무망) 구조의 현실과 제약. 로컬 LLM이 필요한 이유와 시리즈 전체 목표 설정 |
| 2 | [모델 선택과 메모리 요구량](https://jblee.dev/posts/ai-airgap-model-1/) | LLM 벤치마크(LiveCodeBench 등) 분석, FP8 모델의 VRAM 요구량과 KV Cache 메모리 산정법 |
| 3 | [추론 속도와 MoE 모델 이해](https://jblee.dev/posts/ai-airgap-model-2/) | Prefill/Decode 단계별 TPS 개념, MoE(Mixture of Experts) vs Dense 모델 비교와 실무 선택 기준 |
| 4 | [vLLM, Ollama, llama.cpp](https://jblee.dev/posts/ai-airgap-serving/) | 서빙 프레임워크 비교 — vLLM, SGLang, llama.cpp, Ollama의 특징과 망분리 환경 적합성 |
| 5 | [양자화와 정밀도 선택 가이드](https://jblee.dev/posts/ai-airgap-quantization/) | FP8, AWQ, GPTQ, GGUF 등 양자화 기법 비교. 메모리-성능 트레이드오프와 프레임워크별 호환성 |
| 6 | [Claude Code Router 설정](https://jblee.dev/posts/ai-airgap-ccrcode/) | claude-code-router로 로컬 vLLM 서버와 Claude Code를 연동하는 방법. OpenRouter 테스트 및 오프라인 패키지 이전 |
| 7-1 | [실전 배포: 하드웨어/소프트웨어 설계](https://jblee.dev/posts/ai-airgap-requirement-roadmap/) | 8명 팀 기준 GPU 서버 사양 설계. vLLM + Open-WebUI + Claude Code 소프트웨어 스택 구성 |
| 7-2 | [실전 배포: Docker 환경 구축](https://jblee.dev/posts/ai-airgap-docker/) | 폐쇄망에서 Docker와 NVIDIA Container Toolkit 오프라인 설치. vLLM 컨테이너 배포 사전 준비 |
| 7-3 | [실전 배포: vLLM 서버 설치](https://jblee.dev/posts/ai-airgap-vllm/) | Docker 이미지 pull→save→전송→load 과정. HuggingFace 모델 오프라인 다운로드 및 vLLM 서빙 |
| 7-4 | [실전 배포: Open-WebUI 설치](https://jblee.dev/posts/ai-airgap-openwebui/) | 폐쇄망에서 Open-WebUI로 ChatGPT 유사 웹 인터페이스 구축. vLLM 서버 연동 설정 |
| 7-5 | [실전 배포: Mistral-Vibe 설치](https://jblee.dev/posts/ai-airgap-mistral-vibe/) | CLI 에이전트 코딩 도구 Mistral-Vibe를 개발자 PC에 설치하고 vLLM 서버와 연동 |
| 7-6 | [실전 배포: Qwen-Code 설치](https://jblee.dev/posts/ai-airgap-qwencode/) | Alibaba의 CLI 코딩 에이전트 Qwen-Code를 Node.js 기반으로 오프라인 설치. 시리즈 마지막 편 |

---

## 매매 시스템

DMA(Direct Market Access) 관점에서 매매 시스템의 인프라, OMS 설계, 저지연 최적화를 다룹니다. 한국거래소(KRX) 기준, 코드는 Rust로 작성.

### 인프라

증권 전산의 기본 구조와 배경 지식.

| # | 제목 | 요약 |
|---|------|------|
| - | [시리즈 소개](https://jblee.dev/posts/trading-systems-intro/) | 매매 시스템 전체 구성 소개. 인프라/OMS/최적화 세 파트의 목차와 설계 철학 |
| 1 | [HTS/MTS 전산 매매 과정](https://jblee.dev/posts/infra-intro/) | HTS/MTS로 주식 매매 시 시세 수신부터 주문 체결까지의 네트워크 흐름. FEP, BEP, 시세서버, 주문서버, 원장의 역할 |
| 2 | [FEP와 BEP](https://jblee.dev/posts/fep-bep/) | IBM 메인프레임 시대에서 유래한 FEP/BEP 용어의 역사. 현대 금융권 대외계 게이트웨이로의 변화 |
| 3 | [코로케이션과 빛의 속도](https://jblee.dev/posts/collocation/) | 빛의 속도, 광케이블, 마이크로웨이브 기반 지연시간 계산. 서울-부산 RTT와 코로케이션 효과 비교 |
| 4 | [미니원장](https://jblee.dev/posts/mini-ledger/) | 원장 경유 시 발생하는 지연을 회피하기 위한 미니원장. 인메모리 캐시 기반 저지연 구현과 체결 후 정산 |
| 5 | [Kernel Bypass](https://jblee.dev/posts/kernel-bypass/) | 일반 패킷 수신 경로와 kernel bypass(OpenOnload, libvma, DPDK) 기술 비교. AMD/NVIDIA Smart NIC |
| 6 | [오버클럭 서버](https://jblee.dev/posts/overclock-server/) | HFT용 오버클럭 서버의 개념. 주파수 고정, C-state 비활성화, 칩 선별. Xenon, Blackcore, Aravision 제품 비교 |

### OMS (Order Management System)

주문 관리 시스템의 설계와 구현.

| # | 제목 | 요약 |
|---|------|------|
| 1 | [인트로](https://jblee.dev/posts/oms-intro/) | OMS 시리즈 전체 구성. 주문 상태 관리, 체결 처리, 포지션 추적 등 개요 |
| 2 | [중요한 숫자 관리는 정수로](https://jblee.dev/posts/oms-integer-price/) | IEEE 754 부동소수점 오차 문제. 가격/금액을 float 대신 정수(fixed-point)로 변환하여 관리하는 이유 |
| 3 | [시세 수신](https://jblee.dev/posts/receive-data/) | 코스콤 MDCS 가입부터 접속표준서 확인, 파생 호가 메시지 구조까지. 거래소 시세 데이터 가공 과정 |
| 4 | [오더북이 필요한 이유](https://jblee.dev/posts/why-orderbook/) | 단순 호가창(snapshot)이 아닌 L3 오더북이 필요한 이유. 내 주문 추적(queue position)과 백테스트 활용 |
| 5 | [오더북 구성](https://jblee.dev/posts/orderbook-implementation/) | Level/Ladder/Orderbook 3계층 자료구조 설계. BTreeMap, HashMap 등 자료구조 선택과 구현 |

### 최적화

나노초 단위 성능 향상을 위한 저지연 기법.

| # | 제목 | 요약 |
|---|------|------|
| - | [최적화 목록](https://jblee.dev/posts/optimization-intro/) | 최적화 파트 전체 목차 |
| 1 | [메모리 얼라인먼트와 병목](https://jblee.dev/posts/memory-alignment/) | 메모리 자연 정렬(Natural Alignment)의 정의. 캐시 라인 경계와 성능 병목 지점 분석 |
| 2 | [비트 패킹으로 주문 ID 최적화](https://jblee.dev/posts/order-id/) | u64 하나에 거래소(venue)와 주문 ID를 함께 담는 비트 패킹. 메모리 절약과 해시 성능 개선 |
| 3 | [저지연 로깅](https://jblee.dev/posts/logger/) | 컴파일 타임 로그 레벨 필터링, 클로저 기반 지연 평가, 전용 로거 스레드로 로깅 오버헤드 최소화 |
| 4 | [코어 격리](https://jblee.dev/posts/core-isolation/) | Core Affinity만으로는 부족한 이유. isolcpus로 OS 스케줄러 간섭 차단, 전용 코어 확보 방법 |
