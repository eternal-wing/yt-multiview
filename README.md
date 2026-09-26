# yt-multiview

유튜브 동시시청(리액션) 방송과 원본 방송 등 2~4개의 다시보기를 한 화면에서 싱크를 맞춰 보는 서비스의 타당성 조사와 PoC.

- 조사 문서: [`docs/feasibility.md`](docs/feasibility.md)
- PoC: [`poc/index.html`](poc/index.html) — 빌드 없는 단일 HTML 파일

## 요약

| | 데스크탑 | Android | iPhone |
|---|---|---|---|
| 여러 방송 동시 재생 | ✅ | ✅ | ✅ (실기기 확인 필요) |
| 방송별 볼륨 | ✅ | ✅ | ❌ 음소거/솔로만 가능 |
| 전체 재생/정지 | ✅ | ✅ | ✅ 첫 재생은 탭 필요 |
| 시작시각 자동 싱크 + 수동 보정 | ✅ | ✅ | ✅ |

## PoC 실행

### GitHub Pages (아이폰 테스트용)
`poc/` 가 바뀌어 push되면 `.github/workflows/pages.yml` 이 자동으로 배포한다. 배포 주소: **https://eternal-wing.github.io/yt-multiview/**

처음 한 번은 저장소에서 설정이 필요하다:
1. 저장소가 private이면 GitHub Pages는 유료 플랜(Pro 등)에서만 쓸 수 있다. 무료 계정이면 저장소를 public으로 바꾼다.
2. **Settings → Pages → Build and deployment → Source** 를 **GitHub Actions** 로 선택한다.
3. **Actions → Deploy PoC to GitHub Pages → Run workflow** 로 한 번 실행한다(설정 전에 실행돼 실패한 경우 포함).

### 로컬
IFrame API는 `file://` 에서 제대로 동작하지 않으므로 http로 서빙해야 한다.

```bash
python3 -m http.server 8000
# 데스크탑: http://localhost:8000/poc/
```

### 화면 구성
- 영상은 화면 크기와 방송 수에 맞춰 자동 배치된다: 폰 세로에서 2~3개는 위아래, 4개는 2×2, 가로·데스크탑에서는 옆으로.
- 영상 아래 라벨(번호·★기준·음소거·보정값·오차)을 탭하면 그 방송이 선택되고(파란 테두리), 하단 도크의 둘째 줄이 선택한 방송을 제어한다: ★ 기준 지정, 🔊 음소거, 솔로, 보정 −1/−.1/+.1/+1. iOS가 아니면 볼륨 슬라이더 줄이 추가로 보인다.
- 도크 첫 줄은 전체 제어다: ▶/⏸, −10/+10초, ⟲ 재정렬, 🔗 링크 복사, ⚙ 설정. 맨 위 시크바는 기준 방송 위치다.
- iPhone 13/14 세로(Safari 가시영역 390×664)와 SE(375×548)에서 방송 2개와 컨트롤이 스크롤 없이 한 화면에 들어간다.

### 사용법
1. 방송 URL을 한 줄에 하나씩 입력한다 (`watch?v=`, `youtu.be/`, `/live/`, 영상 ID 모두 가능).
2. (선택) YouTube Data API 키를 입력하면 방송 시작시각으로 자동 싱크한다. 키는 [Google Cloud Console](https://console.cloud.google.com/apis/library/youtube.googleapis.com)에서 YouTube Data API v3를 활성화한 뒤 발급한다. 키는 이 브라우저의 localStorage에만 저장된다.
3. **불러오기** 후 하단의 **▶** 를 탭한다.
4. 어긋나 있으면 방송 라벨을 탭해 선택한 뒤 도크의 보정 버튼(±0.1s / ±1s)으로 맞춘다. 양수는 그 방송을 앞(나중 장면)으로 당긴다.
5. **🔗** 로 영상 목록·보정값·기준 방송이 담긴 URL을 공유할 수 있다.

### 실기기 테스트 체크리스트
- [ ] 데스크탑: 방송 2~4개가 동시에 재생되고, 볼륨 슬라이더가 방송별로 동작하는가
- [ ] 데스크탑: 한 방송의 자체 컨트롤로 앞으로 넘겨도 다른 방송이 따라오는가(기준 방송) / 되돌아오는가(나머지)
- [ ] iPhone: 하단 ▶ 한 번 탭으로 모든 방송이 시작되는가
- [ ] iPhone: 소리 켠 방송 2개 이상이 동시에 재생되는가, 아니면 하나가 멈추는가
- [ ] iPhone: 음소거/솔로 버튼이 동작하는가
- [ ] 광고가 나온 뒤 몇 초 안에 다시 맞춰지는가
- [ ] 자동 싱크 오차는 대략 몇 초인가 (보정값 기록)
