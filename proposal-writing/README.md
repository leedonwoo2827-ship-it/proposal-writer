# Proposal writing - AI 제안서 작성 스킬

RFP 요구사항과 회사 데이터를 결합하여 구조화된 JSON 형식의 제안서 초안을 자동 작성하는 Claude Desktop 스킬입니다.

## 주요 기능

- **데이터 기반 컨텐츠 합성**: RFP 구조 + Reference 논리 + Raw Data 팩트를 지능적으로 결합
- **분량 최적화 엔진**: 5만~20만 자 범위에서 섹션별 중요도에 따라 자동 조절
- **근거 기반 컬러링**: 각 문장의 출처를 색깔로 구분 (Reference 🟢 / Raw Data 🔴 / RFP 🔵 / AI 생성 🟡)
- **구조화된 JSON 출력**: 미리보기 화면 렌더링 및 HWPX 변환에 최적화된 포맷

## 출력 형식

### JSON 구조

```json
{
  "metadata": {
    "title": "제안서 제목",
    "organization": "기관명",
    "date": "2026. 2. 14.",
    "model": "gemini_3.0_flash",
    "preset": "제안서",
    "total_chars": 150000
  },
  "content": [
    {
      "type": "section",
      "title": "훈련과정명",
      "items": [
        {
          "level": 3,
          "text": "과정 명칭의 정의"
        },
        {
          "level": 4,
          "text": "본 훈련과정은 {{green:AI의 개념을 확립하고}} 실무 역량을 강화하기 위해 설계되었다."
        }
      ]
    },
    {
      "type": "table",
      "title": "훈련 내용",
      "headers": ["회차", "모듈명", "주요 내용"],
      "rows": [...]
    }
  ]
}
```

### 레벨 시스템

> level 1만 제목체(돋움체). level 2~6은 본문(바탕체).

| 레벨 | 기호 | 폰트 | 용도 |
|------|------|------|------|
| 1 | (없음) | 돋움체 Bold 18pt | 제목 (#) |
| 2 | (없음) | 바탕체 Medium 11pt | 일반 본문 |
| 3 | □ | 바탕체 Medium 11pt | 본문 항목 |
| 4 | ○ | 바탕체 Light 10pt | 세부 항목 |
| 5 | ― | 바탕체 Light 10pt | 보충 항목 |
| 6 | ※ | 바탕체 Light 10pt | 참고/주석 |

### 색상 마커 (인라인)

텍스트 안에 `{{color:구절}}` 형식으로 출처를 표시한다:

- 🔴 `{{red:구절}}`: Raw 데이터 기반 (수치, 팩트)
- 🟢 `{{green:구절}}`: Reference 제안서 기반
- ⚫ 마커 없음: AI 생성 텍스트 (기본 검정)

## 설치 방법

### 방법 1: 드래그 앤 드롭 (권장)

1. 이 레포지토리를 클론하거나 ZIP으로 다운로드합니다:
   ```bash
   git clone https://github.com/leedonwoo2827-ship-it/proposal-writing.git
   ```

2. Claude Desktop을 실행합니다.

3. `proposal-writing` 폴더를 Claude Desktop 창에 드래그 앤 드롭합니다.

4. 스킬이 자동으로 설치되며, Claude가 사용 가능하다고 알려줍니다.

### 방법 2: 수동 설치

Claude Desktop의 Skills 디렉토리에 직접 복사:

**Windows:**
```bash
xcopy /E /I proposal-writing "%APPDATA%\Claude\skills\proposal-writing"
```

**macOS/Linux:**
```bash
cp -r proposal-writing ~/.config/Claude/skills/
```

## 사용 방법

### 기본 사용

Claude Desktop에서 자연스럽게 요청하세요:

```
"RFP 구조와 회사 자료를 결합해서 15만 자 제안서를 작성해줘"
```

```
"제안서 JSON 파일을 생성하고 미리보기로 확인하게 해줘"
```

### Claude Desktop 프로젝트에서 사용

`claude_desktop_config.json` 또는 프로젝트 지침에 다음을 추가:

```json
{
  "instructions": "제안서 작성 시 proposal_writing 스킬을 사용하세요. 최종 결과는 반드시 proposal-full.json 형태로 저장해야 합니다."
}
```

### 통합 제안서 자동화 프로젝트에서 사용

이 스킬은 다음 워크플로우에서 3단계(핵심)에 해당합니다:

1. **rfp-analyzer** - RFP 분석 및 구조 추출
2. **company-data-manager** - 회사 자료 분석 및 매칭
3. **proposal-writing** (이 스킬) - 제안서 초안 작성
4. **hwpx-writing** - HWPX 파일 생성
5. **text2img-mcp** - 이미지 생성

## 예제

### 제안서 전체 생성

```
사용자: 15만 자 제안서를 작성해줘
        - RFP: [분석 완료된 구조]
        - Reference: [과거 합격 제안서 3개]
        - Raw Data: [이력서, 기술 문서, 회의록]

Claude: 제안서 작성을 시작합니다.

1단계: 데이터 통합 중...
✓ RFP 구조 로드 완료
✓ Reference 논리 학습 완료
✓ Raw Data 팩트 추출 완료

2단계: 섹션별 작성 중...
✓ 1장: 훈련과정명 (12,500자)
✓ 2장: 과정 설계 (28,000자)
...

3단계: JSON 파일 생성 중...
✓ proposal-full.json 저장 완료
✓ 총 글자 수: 148,532자

미리보기가 준비되었습니다!
```

### 일부 섹션만 보완

```
사용자: "2장. 과정 설계" 섹션만 다시 작성해줘
        더 많은 Reference 내용을 반영해서

Claude: "2장. 과정 설계" 섹션을 보완합니다.

Reference 제안서의 논리 구조를 더 상세히 반영하여:
- 기존: 15,000자 → 수정: 28,000자
- Reference 비율: 40% → 70%

업데이트된 JSON:
{
  "type": "section",
  "title": "과정 설계",
  "items": [...]
}
```

## 활용 팁

### 1. 분량 조절
- 중요 섹션: Reference의 상세한 논리 전개 활용
- 일반 섹션: 요약된 사실 중심 서술
- 전체 글자 수 목표치의 ±10% 범위 내에서 자동 조정

### 2. 품질 검증
생성된 JSON은 다음 항목을 자동 검증합니다:
- ✅ 모든 섹션에 최소 1개 이상의 level 3 항목 존재
- ✅ 본문 아이템 레벨이 3~6 범위 내
- ✅ 표의 헤더와 행 개수 일치
- ✅ `{{red:}}`/`{{green:}}` 마커가 구절 단위로 적용
- ✅ 전체 글자 수가 목표 범위(±10%) 내

### 3. 다음 단계
- 생성된 `proposal-full.json`을 `hwpx-writing`로 전달하여 HWPX 파일 생성
- 미리보기 화면에서 최종 검토 후 수정

## 포함된 유틸리티

### markdown_to_json.py

마크다운 형식의 제안서를 JSON으로 변환하는 유틸리티입니다.

```bash
python markdown_to_json.py input.md output.json
```

## 주의 사항

- 최종 결과물은 반드시 `proposal-full.json` 형태로 저장되어야 합니다
- 이 JSON 파일은 미리보기 화면에서 즉시 렌더링되고 Skill 4로 전달됩니다
- Reference의 논리 구조와 Raw 데이터의 팩트를 균형있게 활용하세요

## 라이선스

MIT License

## 기여

이슈나 개선 제안은 GitHub Issues를 통해 제출해주세요.

## 관련 프로젝트

- [rfp-analysis](https://github.com/leedonwoo2827-ship-it/rfp-analysis) - RFP 분석 스킬
- [company-data](https://github.com/leedonwoo2827-ship-it/company-data) - 회사 자료 관리 스킬
- [hwpx_writer](https://github.com/leedonwoo2827-ship-it/hwpx_writer) - HWPX 생성 스킬
- [text2img-mcp](https://github.com/leedonwoo2827-ship-it/text2img-mcp) - 이미지 생성 MCP
