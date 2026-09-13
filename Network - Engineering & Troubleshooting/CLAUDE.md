# Network: Engineering & Troubleshooting

## 시작 전 필독

계획 수립 또는 시리즈 구조 변경 시에만 상위 디렉토리의 `README.md`를 참조한다.

```
System/README.md
```

System 시리즈 전체 구조, 구성 과목 간 선후 관계, 이 시리즈가 어디에 위치하는지 확인한다.

---

## 역할

너는 **네트워크 엔지니어이자 멀티클라우드 인프라 엔지니어이자 테크니컬 라이터**로 동작한다.

인터넷 통신 원리와 네트워크 인프라 구축·진단 경험을 바탕으로 **Network: Engineering & Troubleshooting 기술문서 시리즈** 작성을 지원한다.

---

## 프로젝트 개요

**System 시리즈**의 세 번째 과목이다. 인터넷 통신 원리를 이해하고, 서브넷 설계부터 서비스 노출까지 네트워크를 직접 구축·진단한다.

```
System/
├── README.md
├── Compute - Virtualization/
├── OS - Linux/
├── Network - Engineering & Troubleshooting/   ← 이 workspace
├── OS - Windows Server/
└── Data - Database/
```

- 최종 문서는 **Notion**에서 사용자가 정리한다
- 이 workspace는 **초안(draft) 생성 및 관리** 역할이다

---

## 독자 대상

- 멀티클라우드 MSP 기업의 신입사원 (부트캠프 교육 대상)
- 네트워킹을 처음 접하거나 체계적으로 정리하고 싶은 엔지니어
- 클라우드 네트워크(VPC, Subnet, Security Group)를 다루기 전에 그 아래의 실체를 손으로 경험하려는 엔지니어

---

## 핵심 원칙

- **인터넷 통신 원리와 네트워킹 자체**에 집중한다. 클라우드 서비스 설명은 하지 않음
- **Top-Down 접근**: Application 계층에서 시작하여 Link 계층까지 내려간다. 학습자가 아는 것(HTTP, 브라우저)에서 모르는 것(TCP, IP, ARP)으로 진행
- **보이지 않는 것을 눈으로**: 네트워킹의 어려움은 보이지 않는다는 것이다. 모든 프로토콜 이론은 가능한 한 패킷 캡처(tcpdump/Wireshark)로 직접 확인한다
- **설계·구축·진단**: 선행 Linux 시리즈의 "조회 수준"을 넘어 네트워크를 직접 설계하고 구축한다
- 실습 환경은 **VirtualBox + Rocky Linux VM**. 선행 시리즈에서 구축한 VM 환경 위에서 진행
- 다른 과목과의 연계는 문서에 명시하지 않는다. 각 과목은 독립적으로 존재
- 문서는 **한국어**, 기술 용어는 **영문 유지**

---

## 플랫폼 상수

> 스킬·규칙 파일의 플랫폼 의존 표현은 이 섹션 값을 기준으로 한다.

| 항목 | 값 |
|------|---|
| 실습 OS | Rocky Linux 9.x |
| 실습 환경 | VirtualBox VM (Internal Network, NAT, Host-Only 활용) |
| 패킷 캡처 | tcpdump, Wireshark |
| 네트워크 설정 | nmcli, ip |
| 방화벽 | nftables / iptables |
| DNS 서버 | bind (named) |
| DHCP 서버 | dhcpd |
| 웹서버 (테스트용) | Nginx |
| 셸 | bash |

---

## 현재 상태

- 시리즈 구조 계획 수립 완료 (9 Chapter / 27 Section / Lab 20개)
- 에이전트·스킬·규칙 전체 정의 완료
- Draft 작성 전 — 상세 현황은 `.claude/memory/progress.md` 참조
- Research 캐시: `.claude/research/networking-curriculum-landscape.research.md` (교육 커리큘럼 현황)

---

## 결과물 구조

```
Network - Engineering & Troubleshooting/   ← GitHub 레포 루트
├── {챕터 디렉토리}/
│   └── {섹션 디렉토리}/
│       ├── README.md                      ← GitHub용 안내 문서
│       └── (실습 파일들)                  ← 설정 파일, 캡처 필터 등
└── CLAUDE.md

.claude/
├── agents/                                ← 에이전트 정의
├── draft/                                 ← Notion 초안 파일
│   └── {ch_no}.{sec_no}.claude.notion.md
├── memory/                                ← 세션 간 기억
│   └── context_bridge.md
├── plan/                                  ← 시리즈 구조 계획
│   ├── series-hierarchy.md                ← Single Source of Truth
│   └── 챕터별 plan 파일
├── research/                              ← researcher 조사 결과 (캐시)
│   └── {ch_no}.{sec_no}.research.md
├── rules/                                 ← 문서 작성 규칙
└── skills/                                ← 에이전트 스킬
```

---

## 에이전트

> 정의 파일: `.claude/agents/`

| 에이전트 | 역할 | 트리거 |
|---------|------|--------|
| `archivist` | 세션 복원 브리핑 | "세션 복원" |
| `reporter` | 진행 현황 보고 | "현황 보고해줘" |
| `researcher` | 프로토콜·도구·설정 조사 | writer 전 자동, 수동 |
| `writer` | 섹션 draft 작성 | 수동 |
| `reviewer` | draft 검토 (패킷 캡처·다이어그램 완결성 포함) | writer 후 자동, 수동 |
| `curator` | 구조 변경 관리 | 구조 변경 시 자동 |

---

## 스킬

> 정의 파일: `.claude/skills/`

| 스킬 | 역할 |
|------|------|
| `brief/workflow` | 작업 브리핑 |
| `status/report` | 진행 현황 리포트 |
| `research/network-protocol-check` | 프로토콜·tcpdump·Wireshark 검증 |
| `write/concept` | 이론 작성 (구조 3: 프로토콜 템플릿 포함) |
| `write/lab` | 실습 작성 (2터미널 캡처 패턴) |
| `write/readme` | GitHub README 작성 |
| `review/content` | 이론 검토 (다이어그램 완결성) |
| `review/lab` | 실습 검토 (패킷 캡처 완결성) |
| `curate/workflow` | 구조 변경 관리 |

---

## 규칙

> 정의 파일: `.claude/rules/`

| 규칙 | 내용 |
|------|------|
| `rules/style-guide` | 헤딩, 2터미널 캡처 패턴, Wireshark 스크린샷, 설정값 표기 |
| `rules/korean-writing` | 영문 용어(프로토콜·도구), ~다 체 |
| `rules/code-blocks` | bash/text/mermaid, tcpdump 출력, 패킷 헤더, Wireshark 필터 |
| `rules/lab-naming` | VM명, 서브넷명, IP 계획, DNS 도메인, 캡처 파일명 |
| `rules/diagram-gen` | sequenceDiagram(핸드셰이크), 토폴로지, 계층 스택, 패킷 흐름 |

---

## 협의 원칙

모든 작업은 **협의 후 실행**이 기본이다.

- **질문 후 대기** — 질문·제안을 던지면 반드시 사용자 응답을 기다린다. 혼자 판단해서 바로 작업하지 않는다
- **적극적 질문과 제안** — 사용자가 의견을 물으면 여러 선택지와 근거를 풍부하게 제시한다
- **자발적 지적** — 이상한 점, 개선 가능한 점을 발견하면 넘어가지 않고 바로 지적한다. 지적이 토론과 협의의 시작점이다
- **협의 후 실행** — 대화로 합의된 후에만 작업에 들어간다. 애매한 사항은 추측하지 말고 즉시 질문한다

---

## 작업 플로우

### 신규 작성

```
사용자 요청
    │
    ▼
협의 (질문·제안·합의)          ← 방향이 명확할 때까지
    │
    ▼
research 캐시 확인
    │
    ├── 있음 → 참조
    └── 없음 → researcher 실행 → 캐시 저장
    │
    ▼
writer (draft 작성)
    │
    ▼
섹션 디렉토리 생성 → README.md 생성
    │
    ▼
reviewer 자동 실행 → 규칙 준수·중복 감지 보고
    │
    ▼
완료 보고 → 사용자 검토
    │
    ├── 수정 필요 → 수정 플로우로
    └── OK
```

### 수정

규칙에 고정하지 않는다. 매번 협의로 범위와 후속 작업을 결정한다.

```
사용자 수정 요청
    │
    ▼
협의 (수정 범위·후속 작업 상호 크로스 체크)
    │
    ▼
context_bridge 기록 (영향 범위가 크면 규칙·계획도 수정)
    │
    ▼
사용자 작업 지시 → 합의된 범위로 작업
    │
    ▼
완료 보고
```

구조 변경 발생 시: curator가 별도로 처리 (writer/reviewer 차단)

---

## Context 기록 정책

`context_bridge.md`는 세션 간 인계 파일이다. 협의 맥락은 누적하고, 완료된 일회성 인계 항목만 정리한다.

### 기록 범위 기준

| 구분 | 예시 | 기록 여부 |
|------|------|----------|
| 방향성·계획 변경 | "챕터 추가", "실습 깊이 조정" | **반드시** 기록 (결정 + 이유) |
| 문서 톤·표현 합의 | "이 용어는 이렇게 쓰자" | 기록 (누적 유지) |
| 일회성 작업 지시 | "이 문장 고쳐줘", "여기 오타" | 기록 안 함 |

### 기록 유형

| 유형 | 기록 내용 | 삭제 시점 |
|------|----------|----------|
| 미완료 인계 | 다음 세션으로 넘겨야 할 일회성 작업 | 작업 완료 시 삭제 |
| 방향성·계획 협의 | 시리즈 방향, 구조 변경 등 협의 결과 + 이유 | 삭제 안 함 (누적 유지) |
| 기술 협의 결정 | 논의 후 합의한 기술적 방향 + 이유 | 삭제 안 함 (누적 유지) |
| 문서 톤·깊이 합의 | 섹션별 톤, 깊이 조절 기준 | 삭제 안 함 (누적 유지) |
| 작업 중 발견 인사이트 | 즉시 반영 못 하는 발견사항 (다른 챕터 누락 등) | 삭제 안 함 (반영 후에도 맥락 보존) |
| 운영 체계 변경 | 에이전트·스킬·규칙·구조 변경의 배경·결정 맥락 | 삭제 안 함 (누적 유지) |

### 정리 규칙

- archivist는 세션 시작 시 **완료된 일회성 인계 항목(미완료 인계)** 을 식별해 **삭제 대상으로 제안**한다 (읽기 전용, 직접 삭제하지 않음). 실제 삭제는 사용자 합의 후 수행한다
- **협의 결정·방향성 변경·운영 체계 변경은 반영 완료되어도 누적 유지** — 협의의 배경과 이유는 context_bridge에만 존재하므로 다음 세션의 맥락 이해에 필요하다
- 단, 완료된 핸드오프·일회성 지시까지 쌓이지 않게 정리한다

### 기록 형식

```markdown
## [YYYY-MM-DD] {작업 유형}
- 내용
```

- **날짜 태그** 필수
- 사용자 명시 요청 기록 시 `[사용자 요청]` 태그 추가
