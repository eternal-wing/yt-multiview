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

IFrame API는 `file://` 에서 제대로 동작하지 않으므로 http로 서빙해야 한다.

```bash
python3 -m http.server 8000
# 데스크탑: http://localhost:8000/poc/
```

iPhone에서 테스트하려면 같은 Wi-Fi에서 `http://<PC의 IP>:8000/poc/` 로 접속하거나, GitHub Pages 등에 올려 https로 접속한다.

### 사용법
1. 방송 URL을 한 줄에 하나씩 입력한다 (`watch?v=`, `youtu.be/`, `/live/`, 영상 ID 모두 가능).
2. (선택) YouTube Data API 키를 입력하면 방송 시작시각으로 자동 싱크한다. 키는 [Google Cloud Console](https://console.cloud.google.com/apis/library/youtube.googleapis.com)에서 YouTube Data API v3를 활성화한 뒤 발급한다. 키는 이 브라우저의 localStorage에만 저장된다.
3. **불러오기 → 전체 재생**.
4. 어긋나 있으면 방송별 **보정** 버튼(±0.1s / ±1s)으로 맞춘다. 양수는 그 방송을 앞(나중 장면)으로 당긴다.
5. **링크 복사**로 영상 목록·보정값·기준 방송이 담긴 URL을 공유할 수 있다.

### 실기기 테스트 체크리스트
- [ ] 데스크탑: 방송 2~4개가 동시에 재생되고, 볼륨 슬라이더가 방송별로 동작하는가
- [ ] 데스크탑: 한 방송의 자체 컨트롤로 앞으로 넘겨도 다른 방송이 따라오는가(기준 방송) / 되돌아오는가(나머지)
- [ ] iPhone: "전체 재생" 한 번 탭으로 모든 방송이 시작되는가
- [ ] iPhone: 소리 켠 방송 2개 이상이 동시에 재생되는가, 아니면 하나가 멈추는가
- [ ] iPhone: 음소거/솔로 버튼이 동작하는가
- [ ] 광고가 나온 뒤 몇 초 안에 다시 맞춰지는가
- [ ] 자동 싱크 오차는 대략 몇 초인가 (보정값 기록)
