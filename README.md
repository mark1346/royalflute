# Royal Flute Studio

`royal-flute-studio.com` — Rebecca Park의 산호세 플루트 레슨 스튜디오 사이트.

GitHub Pages로 정적 호스팅 (custom domain은 `CNAME` 파일).

---

## 구조

```
.
├── index.html         # 홈
├── info.html          # 레슨 목표 + 가격
├── about-me.html      # 강사 소개
├── book-online.html   # Google Form 예약
├── styles.css         # 사이트 전체 스타일
├── assets/            # 이미지
└── CNAME              # 커스텀 도메인
```

빌드 단계 없음. HTML/CSS만 있는 정적 사이트.

---

## 자주 바뀌는 것 — 어디서 고치나

| 바꾸고 싶은 것 | 파일 | 어디 |
|---|---|---|
| 전화번호 | 4개 HTML 파일 footer의 `tel:+14086589050` | "Phone" 섹션 |
| 이메일 | 4개 HTML 파일 footer의 `mailto:` | "Email" 섹션 |
| Instagram/Facebook 링크 | 4개 HTML 파일 footer의 `socials` 부분 | "Follow" 섹션 |
| 가격 / 레슨 설명 | `info.html` | `.tier` 블록 안 |
| 강사 소개 본문 | `about-me.html` | `.bio` 블록 안 |
| 예약 폼 (Google Form) | `book-online.html` | `<iframe src="...">` URL 교체 |
| 사진 | `assets/` 폴더에 새 파일 넣고 HTML의 `<img src=...>` 경로 수정 |
| 색상 / 폰트 | `styles.css` 맨 위 `:root` 변수만 바꾸면 사이트 전체 반영 |

> 팁: 같은 footer가 4개 페이지에 반복되니, 연락처/SNS는 4곳 다 같이 고쳐주세요.

---

## 로컬에서 미리보기

```bash
python3 -m http.server 8000
# 그리고 브라우저로 http://localhost:8000
```

또는 그냥 HTML 파일을 더블클릭해도 열림 (단, 폰트 CDN과 Google Form은 인터넷 필요).

---

## 배포

`master` 브랜치에 push하면 GitHub Pages가 자동 배포합니다.

```bash
git add -A
git commit -m "메시지"
git push origin master
```

도메인 설정은 `CNAME` 파일 안에 `royal-flute-studio.com` 한 줄만 들어있으면 됩니다. 건드릴 일 거의 없음.

---

## 변경 이력 메모

- 이전에는 Wix로 만든 사이트를 HTTrack으로 그대로 미러링해서 배포했음 (~500KB짜리 HTML 4개, Wix CDN에 36개 의존).
- 유지보수가 어려워서 같은 콘텐츠/사진을 깔끔한 정적 HTML+CSS로 재작성 (페이지당 ~5KB).
- Wix CDN 의존성 0개. 외부 의존은 Google Fonts와 Google Form 임베드뿐.
