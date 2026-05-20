# 오늘의 밑줄 — 자동화 세팅 가이드
(2026-05-20 업데이트)

---

## 개요

매 발행 시 Claude가 처리하는 것:
1. HTML 작성
2. GitHub push (volN.html + archive.html)
3. Google Drive 저장 (HTML 2개 + TEXT 2개)

발행인이 직접 하는 것:
- Threads 복붙 발행
- choi님 소스 카톡 복붙

---

## GitHub Push

**방식:** Chrome MCP `javascript_tool`로 GitHub API 직접 호출

**토큰·저장소:**
```
GITHUB_TOKEN=프로젝트 지침 하단 참조
GITHUB_REPO=jucheolkim/jucheolkim.github.io
GITHUB_BRANCH=main
```

**Push 흐름:**
```javascript
// 1. 기존 파일 SHA 조회 (수정 시 필요, 신규는 생략)
fetch("https://api.github.com/repos/REPO/contents/volN.html", {
  headers: {"Authorization": "token TOKEN"}
})

// 2. PUT으로 push
fetch("https://api.github.com/repos/REPO/contents/volN.html", {
  method: "PUT",
  headers: {"Authorization": "token TOKEN", "Content-Type": "application/json"},
  body: JSON.stringify({
    message: "feat: VolN",
    content: btoa(unescape(encodeURIComponent(html))),
    sha: "기존 SHA (신규면 생략)"
  })
})
```

**Push 대상 파일:**
- `volN.html`
- `archive.html`

---

## Google Drive 저장

**방식:** Drive MCP `create_file`로 직접 업로드

**폴더 구조:**
```
진행_개인프로젝트/오늘의밑줄_202603/
├── HTML/   ← vol{N}.html + archive.html
└── TEXT/   ← thread_vol{N}_article1.txt + thread_vol{N}_article2.txt
```

**폴더 ID:**
| 폴더 | ID |
|------|-----|
| HTML | `1PQtwMHVMtXoyhR4UP4cJont7kMVdYYQm` |
| TEXT | `1VbbWbaPoJdS1j8A9mDKH_EmMSrsrm4i9` |

**저장 대상 파일:**
- `vol{N}.html`
- `archive.html`
- `thread_vol{N}_article1.txt`
- `thread_vol{N}_article2.txt`

**주의:** Drive MCP에 파일 수정(update) 기능 없음. 같은 이름으로 새로 생성되므로, 수정 시 중복 파일이 쌓인다. 최신본만 남기고 구버전은 발행인이 직접 Drive에서 삭제.

---

## 발행 순서 (자동화 포함)

```
① HTML 작성 (Claude)
② HTML 검토·확정 (발행인)
③ 스레드 작성 (Claude)
④ GitHub push — vol{N}.html + archive.html (Claude)
⑤ Google Drive 저장 — HTML 폴더 + TEXT 폴더 (Claude)
⑥ Threads 발행 (발행인 직접)
```

---

## 업로드 후 검증

GitHub push 완료 후 Chrome fetch로 확인하는 항목:
1. `header-date` — 날짜 일치 여부
2. Vol 번호 — 올바른 번호인지
3. 첫 아티클 tag — 섹션 구조 정상 여부
4. 파일 size — 잘린 파일 아닌지 (너무 작으면 재push 필요)

---

## 주요 제약 사항

- **GitHub raw URL fetch**: bash_tool에서 403 차단 → Chrome MCP javascript_tool 사용
- **Drive update 없음**: 수정 시 새 파일 생성됨 → 중복 수동 삭제 필요
- **Chrome javascript_tool 보안 제한**: 큰 문자열(HTML 전체) 반환 시 차단될 수 있음 → btoa로 인코딩 후 push
