# Royal Flute Studio

`royal-flute-studio.com` — Rebecca Park의 산호세 사설 플루트 스튜디오 사이트.

순수 HTML/CSS 정적 사이트. 빌드 단계 없음. GitHub Pages로 호스팅.

---

## 구조

```
.
├── index.html         # 홈
├── info.html          # 레슨 철학 + 가격 (Volume I)
├── about-me.html      # 강사 소개 (Profile II)
├── book-online.html   # Google Form 예약 (Volume III)
│
├── design-tokens.css  # 색·폰트·여백 토큰 + editorial 유틸리티 클래스
├── styles.css         # 사이트 chrome (masthead + footer)
│                       # 페이지별 스타일은 각 HTML 안 <style>에 인라인
│
├── assets/            # 이미지 (logo, teacher-portrait)
├── favicon.ico        # multi-resolution (16/32/48)
├── favicon-{16,32}.png
├── apple-touch-icon.png  # 180x180, iOS 홈화면용
│
├── sitemap.xml        # 검색엔진용
├── robots.txt
└── CNAME              # 커스텀 도메인
```

---

## 자주 바뀌는 것 — 어디서 고치나

| 바꾸고 싶은 것 | 파일 | 위치 |
|---|---|---|
| 색·폰트·여백 토큰 (사이트 전체) | `design-tokens.css` | 맨 위 `:root` 블록 |
| 전화번호 | 4개 HTML 파일 | footer의 `tel:+14086589050` (4곳) |
| 이메일 | 4개 HTML 파일 | footer의 `mailto:` (4곳) |
| Facebook/Instagram | 4개 HTML 파일 | footer의 `<div class="socials">` (4곳) |
| 가격 / 레슨 설명 | `info.html` | `.info-pricing__tier` 블록 |
| 강사 소개 본문 | `about-me.html` | `.about-article__body` 단락들 |
| 예약 폼 (Google Form) | `book-online.html` | `<iframe src="...">` URL |
| 사진 | `assets/` 폴더 | 같은 파일명으로 덮어쓰거나 HTML의 `<img src=...>` 경로 수정 |

> 헤더(masthead)와 푸터는 4개 페이지에서 똑같이 반복됩니다. 연락처·SNS 바꿀 땐 4곳 다 같이 고치세요.

---

## 로컬 미리보기

```bash
python3 -m http.server 8000
# http://localhost:8000
```

---

## 배포

`master` 브랜치에 push하면 GitHub Pages가 자동 배포합니다 (1-2분).

```bash
git add -A
git commit -m "메시지"
git push origin master
```

`CNAME`에 `royal-flute-studio.com` 한 줄. 도메인 안 바뀌면 건드릴 일 없음.

---

# 운영 / 배포 가이드

## DNS — Mailplug → Cloudflare 마이그레이션

**왜?** 무료 CDN(전 세계 캐싱) · DDoS 방어 · 빠른 anycast DNS · 무료 SSL · 분석 대시보드. 호스팅(GitHub Pages)은 그대로 두고 **DNS만** 옮기면 됨.

### 1단계 — Cloudflare에 도메인 등록

1. https://dash.cloudflare.com 가입 (무료)
2. `Add a Site` → `royal-flute-studio.com` 입력
3. **Free 플랜** 선택
4. Cloudflare가 현재 Mailplug의 DNS를 스캔. 다음 레코드가 있는지 확인 (없으면 추가):

   | Type | Name | Content | Proxy |
   |---|---|---|---|
   | A | `@` | `185.199.108.153` | **DNS only (회색)** |
   | A | `@` | `185.199.109.153` | DNS only |
   | A | `@` | `185.199.110.153` | DNS only |
   | A | `@` | `185.199.111.153` | DNS only |
   | AAAA | `@` | `2606:50c0:8000::153` | DNS only |
   | AAAA | `@` | `2606:50c0:8001::153` | DNS only |
   | AAAA | `@` | `2606:50c0:8002::153` | DNS only |
   | AAAA | `@` | `2606:50c0:8003::153` | DNS only |
   | CNAME | `www` | `mark1346.github.io` | DNS only |

   → 이 IP들은 GitHub Pages 공식 IP. 변경되면 [GitHub 공식 문서](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site) 참고.

5. Cloudflare가 nameserver 2개 알려줌 (예: `xxx.ns.cloudflare.com`, `yyy.ns.cloudflare.com`)

### 2단계 — Mailplug에서 nameserver 변경

1. Mailplug 도메인 관리 페이지 접속
2. `royal-flute-studio.com` → 네임서버 설정
3. 현재 Mailplug nameserver를 **Cloudflare가 알려준 2개로 교체**
4. 저장 후 보통 5분 ~ 24시간 대기 (대부분 30분 안에 반영됨)

### 3단계 — GitHub Pages SSL 재발급 확인

Cloudflare로 옮기면 GitHub가 새 IP에서 들어오는 검증 요청을 받아야 SSL이 다시 발급됨. 그래서 **처음엔 모든 레코드를 DNS only (회색 구름)** 으로.

1. nameserver 바뀐 뒤 → GitHub repo Settings → Pages → "Enforce HTTPS" 체크박스가 다시 활성화될 때까지 대기 (5분-1시간)
2. HTTPS로 사이트 접속 가능한지 확인: https://royal-flute-studio.com

### 4단계 — Cloudflare 프록시 켜기 (CDN 활성화)

SSL이 안정화된 뒤:

1. Cloudflare DNS 페이지로 가서 4개 A 레코드 + 4개 AAAA 레코드의 **구름 아이콘을 회색 → 주황색으로 토글** (proxied 모드)
2. **SSL/TLS** 메뉴 → **Full (strict)** 선택
3. **SSL/TLS → Edge Certificates** → "Always Use HTTPS" ON, "Automatic HTTPS Rewrites" ON
4. (선택) **Speed → Optimization** → "Auto Minify" HTML/CSS/JS 켜기

이제 전 세계 방문자가 가까운 Cloudflare 엣지에서 응답받고, 트래픽 분석도 무료 대시보드에서 볼 수 있음.

> 도메인 등록 자체를 Cloudflare로 옮기고 싶으면 (Mailplug 갱신 안 하고 싶으면): Cloudflare → Registrar에서 transfer in. 1년 이상 등록된 도메인만 가능. 비싸지도 않고 마진 0(원가 그대로). 다만 굳이 안 옮겨도 됨.

---

## Google Search Console 등록

목적: 어떤 키워드로 검색해서 들어오는지, 인덱싱 안 된 페이지 있는지 등 확인.

1. https://search.google.com/search-console 접속 (Gmail 계정 필요)
2. `Add property` → **Domain** 선택 → `royal-flute-studio.com` 입력
3. Google이 TXT 레코드 알려줌 (예: `google-site-verification=abc...`)
4. **Cloudflare DNS**에 추가:
   - Type: TXT
   - Name: `@`
   - Content: `google-site-verification=...` (Google이 준 값 그대로)
   - TTL: Auto
5. Cloudflare에서 저장 → Search Console로 돌아가 `Verify`
6. 검증되면 **Sitemaps** 메뉴 → `https://royal-flute-studio.com/sitemap.xml` 제출

> 또는 더 빠른 방법: HTML meta tag로 검증. Google이 `<meta name="google-site-verification" content="...">` 줌. 4개 페이지 head에 추가 (또는 index.html에만 추가하고 root 도메인으로 검증).

---

## Google Business Profile (가장 큰 SEO 부스트 - 로컬 비즈니스용)

"flute lessons San Jose" 같은 로컬 검색에서 지도 카드에 노출되려면 필수.

1. https://business.google.com 접속
2. `Manage now` → `Royal Flute Studio` 입력
3. **카테고리**: "Music school" (주) + "Music instructor" (보조)
4. **주소**: 집 주소 입력. 학생을 집에서 받지 않으면 "Hide my address" 체크 → Service Area Business로 등록
5. **Service area**: San Jose, Bay Area
6. **전화**: 408-658-9050
7. **웹사이트**: https://royal-flute-studio.com
8. **운영 시간**: 레슨 가능 시간대
9. **인증**: 우편엽서(5-14일, 가장 흔함) 또는 전화 인증
10. 인증 완료 후 사진 업로드 (스튜디오·강사 사진), 서비스 항목 추가, Q&A 작성

복사해서 쓸 수 있는 비즈니스 description (750자 이내):

> Royal Flute Studio is a private flute studio in San Jose, founded by Rebecca Park — a Korean-American flutist trained at the Royal Academy of Music and Royal College of Music in London, with three performances on the Carnegie Hall stage and finalist honors at the Music Teachers National Association competition. The studio offers customized one-on-one instruction for students at every stage, from foundational lessons to advanced auditions and competition preparation. Trial lessons are available at the beginner rate.

---

## 배포 후 검증 도구

| 도구 | 용도 | URL |
|---|---|---|
| Rich Results Test | JSON-LD 스키마가 구글에 잘 잡히는지 | https://search.google.com/test/rich-results |
| PageSpeed Insights | Core Web Vitals (LCP, CLS, INP) 측정 | https://pagespeed.web.dev/ |
| Mobile-Friendly Test | 모바일 호환성 | https://search.google.com/test/mobile-friendly |
| Schema Markup Validator | JSON-LD 문법 검사 | https://validator.schema.org/ |

배포한 다음 위 도구들에 `https://royal-flute-studio.com/`, `/about-me.html`, `/info.html`, `/book-online.html` 각각 넣어서 확인.

---

## 변경 이력 메모

- 옛 사이트: Wix로 만든 사이트를 HTTrack으로 미러링해서 배포 (~500KB짜리 HTML 4개, Wix CDN에 36개 의존)
- 현재: editorial("Conservatory Classicism") 톤으로 전면 재작성. 페이지당 ~7-15KB. 외부 의존은 Google Fonts(Fraunces, Lora, Italiana)와 Book Online의 Google Form 임베드뿐.
