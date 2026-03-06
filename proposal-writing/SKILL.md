---
name: proposal-writing
description: RFP 요구사항(Skill 1)과 사내 데이터(Skill 2)를 결합하여 제안서 초안을 작성하는 스킬. 최종 결과물은 구조화된 JSON 파일로 출력되며, 미리보기 화면 표시 및 Skill 4(HWPX 변환)로 전달된다.
---

# AI 제안서 작성 스킬 (Skill 3)

## 개요

이 스킬은 **Skill 1(RFP 뼈대)** + **Skill 2(회사 데이터)**를 결합하여 제안서 초안을 작성하고, **구조화된 JSON 형식**으로 출력합니다.

---

## ⚠️ 핵심 원칙: JSON 출력 형식

> **Skill 3의 최종 결과물은 반드시 `proposal-full.json` 형태로 저장되어야 한다.**

이 JSON 파일은:
1. **미리보기 화면**에서 즉시 렌더링
2. **Skill 4**로 전달되어 HTML + HWPX 생성

---

## 주요 기능

### 1. 데이터 기반 컨텐츠 합성

**입력:**
- **Skill 1**: RFP에서 추출한 Backbone (섹션 구조)
- **Skill 2**: Reference 제안서 + Raw 데이터 (수치, 팩트)

**처리:**
- Reference의 논리 구조와 톤 학습
- Raw 데이터의 핵심 키워드/수치 추출
- 섹션별로 적절한 데이터 매칭

**출력:**
- 구조화된 JSON 파일 (`proposal-full.json`)

---

### 2. 분량 최적화 엔진

설정된 프리셋(5만~20만 자)에 맞춰:
- 중요 섹션 → Reference의 상세한 논리 전개
- 일반 섹션 → 요약된 사실 중심 서술

---

### 3. 근거 기반 컬러링 (인라인 마커)

출처 텍스트를 **문장 안에 자연스럽게 녹여** 넣고, 구절 단위로 색상 마커를 표시한다:
- 🔴 `{{red:구절}}`: Raw 데이터 기반
- 🟢 `{{green:구절}}`: Reference/RFP 기반
- 마커 없음: AI 생성 텍스트 (기본 검정)

JSON 예시:
```json
{
  "level": 4,
  "text": "본 훈련과정은 {{red:20010105 빅데이터분석 세분류}}를 적용하며, {{green:산업 현장의 실무 역량 강화}}를 핵심 목표로 설계되었다."
}
```

> **핵심 원칙 — 융합 작성**: 출처 텍스트를 별도 블록으로 나열하지 않는다. 반드시 AI가 작성한 문장 흐름 안에 출처 구절을 자연스럽게 섞어 넣는다.

---

## JSON 출력 형식

### 전체 구조

```json
{
  "metadata": {
    "title": "제안서 제목",
    "organization": "기관명",
    "date": "2026. 2. 14.",
    "model": "gemini_3.0_flash",
    "preset": "제안서",
    "rfp_source": "원본 RFP 파일명",
    "total_chars": 150000
  },
  "content": [
    {
      "type": "section",
      "id": "section1",
      "title": "훈련과정명",
      "items": [
        {
          "level": 3,
          "text": "과정 명칭의 정의 및 기획 배경"
        },
        {
          "level": 4,
          "text": "본 훈련과정은 {{green:인공지능(AI)의 광범위한 개념을 확립하고}} 실무 적용 역량을 강화하기 위해 설계되었다."
        },
        {
          "level": 5,
          "text": "{{red:넷플릭스의 추천 알고리즘}}과 같이 우리 곁의 AI 사례를 통해 기술의 실체를 파악한다."
        }
      ]
    },
    {
      "type": "table",
      "id": "table1",
      "title": "훈련 내용",
      "headers": ["회차", "모듈명", "주요 내용"],
      "rows": [
        ["1차수", "AI란 무엇인가?", "인공지능의 정의 및 기술 범위 학습"],
        ["2차수", "우리 곁의 AI", "실생활 AI 사례 연구"]
      ],
      "style": {
        "headerBg": "#2563eb",
        "headerColor": "#ffffff",
        "borderColor": "#cbd5e1"
      }
    }
  ]
}
```

---

## 레벨 시스템

> level 1만 제목체(돋움체). level 2~6은 본문(바탕체).

| 레벨 | 기호 | 폰트 | 용도 | 예시 |
|------|------|------|------|------|
| 1 | (없음) | 돋움체 Bold 18pt | 제목 (#) | — 자동 적용 — |
| 2 | (없음) | 바탕체 Medium 11pt | 일반 본문 | "이 단계적 설계는..." |
| 3 | □ | 바탕체 Medium 11pt | 본문 항목 | "과정 명칭의 정의 및 기획 배경" |
| 4 | ○ | 바탕체 Light 10pt | 세부 항목 | "본 훈련과정은..." |
| 5 | ― | 바탕체 Light 10pt | 보충 항목 | "넷플릭스 추천 알고리즘..." |
| 6 | ※ | 바탕체 Light 10pt | 참고/주석 | "명시적 프로그래밍 없이..." |

---

## 색상 마커 (인라인 방식)

출처 구별은 JSON의 `text` 필드 안에 인라인 마커로 표현한다:

| 출처 | 마커 형식 | 설명 |
|------|-----------|------|
| Raw 데이터 (수치, 팩트) | `{{red:구절}}` | 🔴 적색 |
| Reference / RFP 기반 | `{{green:구절}}` | 🟢 녹색 |
| AI 생성 텍스트 | 마커 없음 | ⚫ 기본 검정 |

hwpx-writer가 마커를 인식하여 해당 색상의 글자로 자동 변환한다.

### 융합 작성 규칙
1. 문장 단위로 섞어 쓴다 — AI 텍스트(검정)와 출처 텍스트(적색/녹색)가 하나의 문장 안에서 자연스럽게 이어져야 한다.
2. 출처 텍스트를 문장의 일부로 편입한다 — 앞뒤에 접속사, 서술어, 맥락 설명을 붙여 완성된 문장으로 만든다.
3. 절대로 출처 텍스트만 따로 블록으로 나열하지 않는다.
4. 마커는 구(phrase) 또는 절(clause) 단위로 감싼다 — 문장 전체가 아니라 핵심 구절만 마킹한다.

---

## 사용 방법

### 1. 입력 준비

```javascript
const rfpBackbone = await skill1.extractBackbone(rfpPdf);
const companyData = await skill2.loadData({
  references: ['past-proposal-1.pdf', 'past-proposal-2.pdf'],
  rawData: ['tech-spec.xlsx', 'team-cv.pdf', 'meeting-notes.srt']
});
```

### 2. 제안서 생성

```javascript
const proposal = await skill3.generateProposal({
  backbone: rfpBackbone,
  data: companyData,
  preset: {
    targetChars: 150000,
    model: 'gemini_3.0_flash'
  }
});
```

### 3. JSON 저장

```javascript
fs.writeFileSync('proposal-full.json', JSON.stringify(proposal, null, 2));
```

---

## 미리보기 연동

생성된 JSON은 자동으로:
1. **좌측 뼈대 패널**: 섹션 리스트 표시
2. **우측 미리보기**: HTML 렌더링 (색깔 포함)

```html
<div class="level2" style="color: #16a34a">
  본 훈련과정은 인공지능(AI)의 광범위한 개념을...
</div>
```

---

## Skill 4 연동

`proposal-full.json` 파일이 자동으로 Skill 4로 전달되어:
- **HTML 미리보기** 생성 (`제안서_gemini_3.0_flash_2026-2-14.html`)
- **HWPX 문서** 생성 (`제안서_gemini_3.0_flash_2026-2-14.hwpx`)

---

## 품질 보증

### 검증 항목

- ✅ 모든 섹션에 최소 1개 이상의 level 3 항목 존재
- ✅ 본문 아이템 레벨이 3~6 범위 내
- ✅ 표의 헤더와 행 개수 일치
- ✅ `{{red:}}`/`{{green:}}` 마커가 구절 단위로 적용
- ✅ 전체 글자 수가 목표 범위(±10%) 내

### JSON 스키마 검증

`content-schema.json`과 비교하여 형식 검증:

```bash
node validate-proposal.mjs proposal-full.json
```

---

## 참고 문서

- **JSON 스키마**: `content-schema.json`
- **스타일 규정**: `스타일_규정.md`
- **Skill 4 연동**: `skills/4_hwpx_generation/SKILL.md`
