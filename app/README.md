# AI PEQ

자연어로 요청하면 AI가 헤드폰 FR(주파수 응답) 데이터를 근거로 파라메트릭 EQ를
만들어주고, Equalizer APO에 바로 적용해주는 개인용 도구.

## 준비물

1. **Python 3.11 이상** (3.12에서 개발/테스트함)
2. **Anthropic API 키** — https://console.anthropic.com 에서 발급
3. **Equalizer APO** — https://sourceforge.net/projects/equalizerapo/ 설치 필요
   (Peace는 선택. APO 위에 얹는 GUI라 없어도 이 프로그램은 동작함)

## 설치

```bash
pip install -r requirements.txt
python main.py
```

만약 "Unknown control: FilePicker" 같은 빨간 배너가 뜨면, `flet`과 `flet-desktop`
두 패키지 버전이 어긋난 것임 (`flet-desktop`이 실제 데스크톱 클라이언트 바이너리를
담당하는 별도 패키지라 둘이 정확히 같은 버전이어야 함). 아래로 강제 재설치:

```bash
pip install --upgrade --force-reinstall flet==0.86.5 flet-desktop==0.86.5
```

첫 실행 시 우측 상단 톱니바퀴(설정)에서 API 키를 입력하고 저장.
API 키는 `~/.ai_peq/config.json`에 로컬로만 저장되고 다른 곳으로 전송되지 않음.

## AI 제공자 선택 (Claude 외 다른 모델도 사용 가능)

설정 화면에서 "API 제공자"를 고를 수 있음:

- **Anthropic 직접**: Anthropic API 키 하나로 Claude 모델에 바로 연결.
- **OpenAI 호환 (OpenRouter, DeepSeek 등)**: base URL / API 키 / 모델명을
  직접 입력하는 범용 모드. 프리셋 드롭다운에서 OpenRouter나 DeepSeek을
  고르면 base URL이 자동으로 채워지고, 모델명은 자유롭게 입력하면 됨
  (예: `anthropic/claude-sonnet-4.6`, `deepseek-chat` 등). OpenAI 호환
  형식(`/chat/completions`)을 쓰는 곳이면 어디든 이 모드로 연결 가능함.

## AI 시스템 프롬프트 확인/수정

설정 화면 맨 아래에 AI한테 실제로 전달되는 시스템 프롬프트 전문이 그대로
노출돼 있고, 자유롭게 고칠 수 있음. `%%MAX_BANDS%%`와 `%%MAX_GAIN%%`은
저장된 안전 설정값으로 자동 치환되는 자리표시자니까 그대로 남겨두는 게
좋음 (지워도 동작은 하지만 그 경우 프롬프트에 실제 숫자 제한이 안내되지
않음). "기본값으로 초기화" 버튼으로 언제든 원래 프롬프트로 되돌릴 수 있음.

## 헤드폰 찾기 (내장 DB)

CSV 업로드 옆에 "헤드폰 찾기" 버튼이 있음. 누르면 oratory1990, Kuulokenurkka,
Rtings, Innerfidelity 실측 데이터 1,467개가 프로그램 안에 통째로 들어있어서,
**인터넷 연결 없이도** 검색하고 바로 불러올 수 있음. GitHub이 막히거나
장애가 나도 이 기능은 영향을 안 받음 (데이터가 이미 프로그램 폴더 안에
있으니까).

단, 신제품처럼 이 DB에 아직 없는 헤드폰(예: AR5000 MK2, Apollo Pro)은
여전히 "CSV 업로드"로 직접 구해서 넣어야 함 -- 이 두 기능은 서로 대체가
아니라 상호 보완 관계임.

## Equalizer APO 연동 (최초 1회 설정)

이 프로그램은 EQ를 바꿀 때마다 `AI-PEQ.txt`라는 파일을
Equalizer APO의 config 폴더(기본값: `C:\Program Files\EqualizerAPO\config`)에
자동으로 써줌. Equalizer APO는 이 폴더를 감시하고 있어서 파일이 바뀌면 자동으로
반영되지만, **최초 1회만** 아래 작업이 필요함:

1. `C:\Program Files\EqualizerAPO\config\config.txt` 를 메모장으로 열기
2. 아무 줄에나 다음을 추가:
   ```
   Include: AI-PEQ.txt
   ```
3. 저장

이후로는 프로그램 안에서 AI한테 요청할 때마다 자동으로 시스템 전체 오디오에
EQ가 적용됨. **주의**: Equalizer APO는 시스템 전체 사운드(게임, 유튜브, 음악
앱 전부)에 적용되는 방식이라, 특정 앱에만 걸리는 게 아님.

만약 "권한 오류"가 뜨면, `C:\Program Files\...` 는 관리자 권한이 필요한 폴더라서
그럼. 프로그램을 관리자 권한으로 실행하거나, 설정에서 APO config 폴더 경로를
관리자 권한이 필요 없는 곳으로 바꿔서 (그리고 APO 쪽 Include 경로도 맞춰서) 써도 됨.

## Peace EQ 프리셋으로 저장

채팅으로 EQ를 만든 다음 "Peace 프리셋으로 저장" 버튼을 누르면, APO config
폴더 옆에 `AI_PEQ_Presets` 폴더가 생기고 거기에 이름 붙인 `.txt` 파일이 저장됨.
Peace를 쓴다면 이 파일을 Peace의 프리셋 불러오기(가져오기)로 열면 됨 -- 포맷이
Equalizer APO 필터 문법 그대로라 별도 변환 없이 바로 읽힘.

## 화면 구성

- **헤드폰 선택 / CSV 업로드**: 지금은 AR5000 MK2, Apollo Pro가 내장돼 있고,
  다른 헤드폰 CSV(주파수, L, R 컬럼)를 업로드하면 그것도 추가됨.
- **하만 타겟으로 1차 보정**: 누르면 raw FR을 하만 커브 쪽으로 50% 정도만
  살짝 당겨줌 (완전히 하만에 맞추는 게 아니라 "살짝 보정"). 다시 누르면 해제.
- **AI 적용 후 보기 스위치**: 방금 만든 EQ 적용 전/후를 즉시 전환해서 비교.
- **그래프**: 청록색 = 현재 헤드폰 raw, 회색 점선 = 하만 타겟, 주황색 = AI가
  만든 최종 결과. AI가 EQ를 새로 만들면 주황 커브가 부드럽게 애니메이션으로
  바뀜.
- **히스토리 칩**: 지금까지 AI가 만들어준 EQ들이 작은 태그로 쌓임. 클릭하면
  그 버전으로 즉시 되돌아가고 APO에도 그 버전이 다시 적용됨.
- **채팅창**: 여기에 원하는 걸 자연어로 입력. AI가 왜 그렇게 조정했는지
  설명 먼저 오고, 그래프가 그에 맞춰 바뀜.

## 안전장치

설정에서 "안전 게인 한계"를 조절할 수 있음 (기본 ±6dB). AI가 이 한계를
넘는 값을 제안하면 자동으로 한계값까지만 잘라서 적용하고, 채팅창에 어떤
밴드가 잘렸는지 알려줌.

## 파일 구조

```
main.py            앱 진입점, 전체 UI
config.py          API 키 등 로컬 설정 저장/로드
headphone_data.py  헤드폰 FR 데이터 (내장 + CSV 업로드 파싱), 표준 31포인트 그리드
eq_filters.py       파라메트릭 EQ 필터 수식 (RBJ biquad, 실제 APO 동작과 동일한 계산)
ai_client.py        Claude API 호출, 자연어 -> EQ 밴드 변환, 대화 히스토리 관리
apo_export.py        Equalizer APO / Peace용 텍스트 파일 생성 및 저장
fr_chart.py           FR 그래프 캔버스 위젯 (애니메이션 전환 포함)
presets_store.py       저장된 프리셋 영구 관리 (이름변경/삭제/적용)
local_headphone_db.py  번들된 로컬 헤드폰 DB 검색/로드 (완전 오프라인)
headphone_db/           실제 CSV 데이터 1,467개 + 검색 인덱스 (약 18MB)
```

## 알아두면 좋은 것들

- **AR5000 MK2** 데이터는 ChatGPT/Gemini/Claude/Grok 4개 AI가 각각 그래프를
  디지타이징한 값의 평균이라 상대적으로 신뢰도가 높음. **Apollo Pro**는
  아직 단일 소스 근사치라 정확도가 더 낮음 (추후 다른 소스로 교차검증 예정).
- 타겟 커브 없이 "raw 그대로" 모드일 때 AI는 하만 대비 편차가 아니라 현재
  raw FR 수치 자체를 기준으로 판단함.
- EQ 미리보기 그래프는 실제 RBJ biquad 필터 공식으로 계산되기 때문에,
  Equalizer APO가 실제로 적용했을 때의 커브와 거의 일치함 (근사 벨 커브가
  아님).
