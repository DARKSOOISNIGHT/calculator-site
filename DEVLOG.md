# calculator-site 개발 이력 (2026-07-30)

## 프로젝트 개요
프리랜서용 "3.3% 계산기" — 지급액↔실수령액을 양방향으로 계산해주는 순수 정적 웹페이지. `blog-automation`(C:\Users\llok0\blog-automation) 프로젝트와는 별개다.

**시작 배경**: blog-automation으로 자동발행 중인 블로그 글들이 "정보성 AI 글만으로는 검색 상위 노출이 어렵다"는 우려가 있어서(신생 블로그 + 고경쟁 키워드 + 차별점 부족), 실사용 유틸리티 도구가 검색엔진에서 더 잘 먹힌다는 판단하에 시작함. 참고로 둘러본 사이트: 부동산계산기.net(16개 계산기 모아둔 사이트, 나중에 참고할 만함).

## 배포 현황
- **주소**: https://darksooisnight.github.io/calculator-site/
- **레포**: https://github.com/DARKSOOISNIGHT/calculator-site (public)
- **호스팅**: GitHub Pages, 완전 무료. `gh api repos/DARKSOOISNIGHT/calculator-site/pages`로 활성화함(source: master branch, path /).
- **로컬 경로**: `C:\Users\llok0\calculator-site`

## 구성 파일
- `index.html` — 3.3% 계산기 본체. 서버/백엔드 없이 순수 클라이언트 JS로 계산(개인정보 서버 전송 없음). "받기로 한 금액 기준" / "실수령액 기준" 두 모드 토글, 소득세(3%)+지방소득세(0.3%) 분해 내역 표시.
- `privacy.html` — 개인정보처리방침. blog-automation의 `drafts/privacy_policy.md`를 재활용해 작성.
- `ads.txt` — **아직 없음.** 애드센스 계정 확보 후 실제 publisher ID로 추가해야 함.

## 이번 세션에서 확인/결정한 것
1. **도메인**: 새 도메인 구매 안 함. 새 도메인은 SEO 지수 0부터 시작해서 비효율적이라 판단, GitHub Pages 무료 서브도메인으로 시작하고 블로그에서 링크로 트래픽 유입시키는 전략.
2. **애드센스 순서**: 계산기 사이트 단독으로 먼저 신청하지 않기로 함(콘텐츠 적어서 심사 통과 어려움 — 신생 단일 페이지는 초기 승인 기준에 약함). **blog-automation 쪽 블로그 애드센스가 먼저 승인되면, 그 계정에 이 사이트를 "추가 사이트"로 등록하는 순서로 진행 예정.** blog-automation/DEVLOG.md의 애드센스 심사 결과를 먼저 확인할 것.
3. **티스토리 페이지에 직접 JS 계산기 넣는 방안은 시도 후 포기함** — 티스토리 페이지 에디터에 HTML 모드는 있지만, 코드에디터(CodeMirror) 기반이라 자동화로 정확한 textarea를 찾기 까다로웠고, 더 근본적으로 대부분의 블로그 플랫폼은 XSS 방지 때문에 저장/게시 시점에 `<script>`나 `onclick=` 같은 실행 코드를 서버에서 걸러낼 가능성이 높다고 판단해 GitHub Pages 독립 호스팅으로 방향을 정함.
4. **Git이 시스템에 설치되어 있지 않아서 winget으로 새로 설치함** (`winget install --id Git.Git -e --source winget`). **PowerShell 새 세션마다 PATH가 자동 반영 안 되므로, git/gh 명령 실행 전에 항상 아래를 먼저 실행해야 함**:
   ```powershell
   $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
   ```
   또한 최초 push 시 git이 GitHub 인증 정보가 없어서 실패했었음 → `gh auth setup-git`으로 gh의 인증을 git에 연결해서 해결함(1회만 하면 됨, 이미 완료됨).
5. **디자인 색상 팔레트**: 사용자가 지정한 뮤트 파스텔 톤을 기본으로 적용함(크림 배경 #f2f0ea, 소프트 블루/세이지그린 accent 등). 처음 적용한 accent/result 색이 팔레트 대비 너무 채도 높아 보인다는 피드백을 받아 한 번 더 톤다운함(`--accent: #74889a`, `--result-text: #6b7d62` 등, 다크모드도 대응 조정). 정확한 hex 값은 memory의 `feedback_color_palette.md` 참고 — 앞으로 다른 웹 작업에도 이 팔레트를 기본으로 쓰기로 함.
6. **색상 재조정 (2026-07-30 저녁)**: 사용자가 "모든 색상은 팔레트만 따르고, 글자는 검정(회색 80%, `#333333`), 바탕은 흰색"으로 확정 지정. 커스텀 톤다운 색(`--accent: #74889a`, `--result-text: #6b7d62` 등 팔레트에 없던 임의 배합 색)을 전부 제거하고, 팔레트 원본 8색 중 실제 쓰이는 것만 CSS 변수로 남김: 배경/카드 `#ffffff`, 텍스트 `#333333`, 보더 `#d9d8db`(라이트 그레이), 활성 탭/포커스 `#d3e2f0`(페일 블루), 결과 박스 `#c9d6bd`(세이지그린), 설명 카드 `#dbcee0`(라벤더). 다크모드 미디어쿼리는 "바탕은 흰색" 지시와 상충해 제거함(라이트 테마로 고정). `privacy.html`도 동일 톤으로 맞춤(링크는 컬러 텍스트 대신 검정 텍스트 + 팔레트색 밑줄로 처리해 "글은 검정" 규칙 유지). 이 팔레트 자체는 스크린샷으로 다시 확인함(값 동일, [[feedback_color_palette]] 참고).

## 남은 작업 / TODO
1. blog-automation 블로그 글에 이 계산기 링크 넣기 (아직 안 함)
2. 애드센스: blog-automation 쪽 심사 결과 나오면, 승인된 계정에 이 사이트를 추가 사이트로 등록 → 승인되면 `ads.txt` 실제 publisher ID로 추가
3. (낮은 우선순위) 사용자가 부동산계산기.net처럼 계산기를 여러 개 모은 허브 사이트에 관심을 보였음 — 나중에 계산기를 늘릴 경우 `index.html`을 허브 페이지 구조로 리팩터링하는 것 고려 (예: 취득세, 종합소득세 등 관련 세금 계산기 추가)
4. 정식 커스텀 도메인 연결은 트래픽이 실제로 붙는지 지켜본 뒤에 고려 (지금은 무료 서브도메인으로 충분)
