# 오늘의 밑줄 — HTML 제작 가이드

## 기본 정보
- 파일명: `volN.html` (예: `vol40.html`)
- 발행 주기: 매일
- 배포: GitHub Pages (jucheolkim.github.io)

---

## HTML 구조 (4섹션 고정)
① 창업 철학 아티클 1
② 창업 철학 아티클 2
③ AI·테크 브리핑
④ 오늘의 신제품 (ProductHunt 기반)

각 섹션은 독립 `<article>` 태그. 신제품은 AI브리핑 안에 합치지 않는다.

---

## 페이지 내 순서 (body 기준)

```
.header
.meta
.nav-wrap        ← 이전 호 / 전체 보기
article × 4
.editor          ← 에디터 한마디
.footer
```

---

## nav (이전호/전체보기)

**모든 vol 파일에 반드시 포함.** 누락 시 발행 불가.

위치: `.meta` 바로 아래, 첫 번째 아티클 위.

```html
<div class="nav-wrap">
  <a href="volN-1.html">← 이전 호</a>
  <a href="archive.html">전체 보기</a>
</div>
```

CSS (style 태그 안에 포함):
```css
.nav-wrap { display: flex; justify-content: space-between; margin-bottom: 24px; font-size: 11px; font-family: Arial, sans-serif; }
.nav-wrap a { color: #888; text-decoration: none; }
.nav-wrap a:hover { text-decoration: underline; }
```

---

## CSS 스펙

### 폰트
- 헤더 타이틀, 아티클 제목, 에디터 한마디: `Nanum Myeongjo` (Google Fonts, weight 400/700/800)
- 본문: `Georgia, serif`
- 태그/메타/버튼/라벨: `Arial, sans-serif`

### 레이아웃
- `html` 배경: `#f0efeb`
- `body`: 흰색, `max-width: 680px`, 중앙 정렬, `padding: 40px 20px`, `box-shadow: 0 0 40px rgba(0,0,0,0.06)`
- `word-break: keep-all`

### 헤더
```
ONEDAY DIGEST — 11px, #888, letter-spacing 2px
오늘의 밑줄 — 36px, Nanum Myeongjo, font-weight 800
날짜 · Vol. N — 11px, #888
위아래: border 3px solid #1a1a1a
```

### 아티클
- `.article-tag`: 10px, #888, `border-top: 2px solid #1a1a1a`, `padding-top: 8px`
- `.article-title`: 24px, Nanum Myeongjo, bold, `letter-spacing: -0.5px`
- `.article-summary`: 13px, #555, `line-height: 1.75`
- 더보기 버튼: `border: 1px solid #ccc`, `border-radius: 6px`
- 상세 내용: `border-left: 2px solid #e0e0e0`, `padding-left: 16px`

### 인사이트 단락
- `font-size: 15px`, `color: #333`, `line-height: 1.85`
- 강조: `.highlight` — `font-weight: bold`, `color: #1a1a1a`

### 에디터 한마디
- `font-size: 16px`, `font-weight: 800`, `font-style: italic`
- `font-family: Nanum Myeongjo`
- `border-top: 2px solid #1a1a1a`

---

## 아티클 내부 구조

### 창업 철학 아티클 (1·2번)
```
article-tag / article-title / article-summary
더보기 토글 버튼
[detail 내부]
  이 사람은 누구인가
  주요 활동과 이력
  성공한 것과 실패한 것 (2단 그리드 .box)
  인사이트 (15px, .highlight 포함)
  출처
```

### AI·테크 브리핑 (3번)
```
각 뉴스 아이템:
  .badge (카테고리) + .news-title
  무슨 일인가
  왜 중요한가
  인사이트
출처
```

### 오늘의 신제품 (4번)
```
각 제품 아이템:
  .product-name
  한 줄 설명 (p)
출처: ProductHunt 날짜
```

---

## 콘텐츠 기준

### 톤
- 짧고 단호하게. 사실 나열보다 관점 있게.
- 인사이트는 한 번 더 생각하게 만드는 문장
- 자극적·궁상스러운 디테일 금지. 어려움은 결정의 무게·전환의 의미로 표현
- 동음이의어 오독 가능 표현 사전 체크 (예: '분', '기사')
- 의역 시 직역보다 자연스러운 한국어 우선

### 에디터 한마디
- 해당 호 전체 아티클의 공통 주제를 한 문장으로 도출
- 고정 문구 사용 금지
- 발행인 검토 후 확정

---

## archive.html 관리

- 새 호 한 줄을 VOLS 배열 **맨 앞에만** 추가 (prepend only)
- 기존 항목 절대 수정·삭제 금지
- 작업 시작 시 GitHub archive fetch → 최신 Vol 번호 확인 필수
- 메모리와 archive가 다르면 발행인에게 먼저 확인
- **push 후 반드시 Chrome fetch로 VOLS 항목 수 검증** — 46개 미만이면 누락 발생한 것

### VOLS 항목 구조
```javascript
{ vol: N, date: "2026년 X월 X일", title: "아티클1 제목", tags: "인물1 · 인물2" }
```
- `tags`: article-tag에서 인물 이름만 추출 (카테고리 제외)
- 최신 호가 배열 맨 앞

---

## GitHub push 방식

Desktop Commander PowerShell이 아닌 **Chrome MCP javascript_tool로 GitHub API 직접 호출**.

```javascript
// SHA 조회
fetch("https://api.github.com/repos/jucheolkim/jucheolkim.github.io/contents/volN.html", {
  headers: {"Authorization": "token TOKEN"}
})

// PUT (신규: sha 없음 / 수정: sha 필요)
fetch("https://api.github.com/repos/REPO/contents/volN.html", {
  method: "PUT",
  headers: {"Authorization": "token TOKEN", "Content-Type": "application/json"},
  body: JSON.stringify({ message: "feat: VolN", content: btoa(...), sha: "..." })
})
```

---

## 레퍼런스
- Vol.31: 표준 구조 레퍼런스 (nav 포함)
- Vol.46: nav-wrap CSS 방식 최신 레퍼런스
