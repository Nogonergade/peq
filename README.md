
# AI PEQ

헤드폰의 실측 주파수 응답(FR) 데이터를 기반으로, 자연어 요청만으로 파라메트릭 EQ를 생성하고 Equalizer APO에 실시간으로 적용하는 프로그램입니다.

## 주요 기능

### FR 그래프 표시
헤드폰의 raw FR 커브와 Harman 타겟 커브를 화면에 함께 표시합니다. CSV 파일을 업로드하면 해당 헤드폰의 실측 데이터를 그대로 사용할 수 있습니다.

### 내장 헤드폰 데이터베이스
oratory1990, Kuulokenurkka, Rtings, Innerfidelity의 실측 데이터 약 1,467개를 프로그램 내부에 포함하고 있습니다. 인터넷 연결 없이도 검색과 데이터 로드가 가능하며, 외부 서비스 상태에 영향을 받지 않습니다. 데이터베이스에 없는 헤드폰은 CSV 파일을 직접 업로드하여 추가할 수 있습니다.

### 자연어 기반 EQ 생성
채팅창에 원하는 소리 변화를 자연어로 입력하면, AI가 현재 헤드폰의 실측 FR 수치를 근거로 구체적인 EQ 밴드(주파수, 게인, Q값)를 계산합니다. 이미 강조된 대역은 적게, 부족한 대역은 크게 조정하는 방식으로 상대적인 판단을 반영합니다. 각 조정에 대한 설명이 함께 제공됩니다.

### Harman 타겟 1차 보정
버튼 한 번으로 raw FR을 Harman OE2018 타겟 방향으로 부분 보정할 수 있습니다. 완전히 타겟에 맞추는 것이 아니라 일정 비율만 조정하는 방식입니다.

### 실시간 미리보기 및 적용
EQ 결과는 실제 파라메트릭 필터 공식(RBJ biquad)으로 계산되어 그래프에 표시되므로, 화면에 보이는 커브와 실제 적용되는 결과가 일치합니다. 변경 사항은 애니메이션으로 표시되며, Before/After 비교와 원본으로 즉시 되돌리기를 지원합니다.

### Equalizer APO 연동
생성된 EQ는 Equalizer APO 설정 파일에 자동으로 기록되어 시스템 전체 오디오에 실시간으로 반영됩니다. Peace EQ와 호환되는 프리셋 파일로도 내보낼 수 있습니다.

### 프리셋 관리
생성된 EQ는 자동으로 저장되며, 별도 화면에서 이름 변경, 삭제, 재적용이 가능합니다.

### 다양한 AI 제공자 지원
Anthropic API를 직접 사용하거나, OpenRouter 및 DeepSeek을 포함한 OpenAI 호환 API 엔드포인트를 자유롭게 설정하여 사용할 수 있습니다. AI에게 전달되는 시스템 프롬프트는 설정 화면에서 확인 및 수정이 가능합니다.

### 안전장치
EQ 게인에 상한선을 설정할 수 있으며, 상한을 초과하는 조정은 자동으로 제한되고 그 사실이 안내됩니다.

## Equalizer APO 연동 설정

프로그램이 EQ를 생성할 때마다 Equalizer APO의 config 폴더(기본값: `C:\Program Files\EqualizerAPO\config`)에 `AI-PEQ.txt` 파일을 자동으로 기록합니다. Equalizer APO가 이 파일을 실제로 읽어 적용하려면, 최초 1회 `Include: AI-PEQ.txt` 설정이 필요합니다.

### 방법 1: Equalizer APO Configuration Editor에서 추가 (권장)

1. 작업표시줄 트레이 아이콘 또는 시작 메뉴에서 **Equalizer APO Configuration Editor**를 실행합니다.
2. 필터 목록의 `+` 버튼을 눌러 새 항목을 추가합니다.
3. 항목 종류를 **Include**로 선택합니다.
4. 파일명 칸에 `AI-PEQ.txt`를 입력하거나, 폴더 아이콘을 눌러 config 폴더 안의 `AI-PEQ.txt` 파일을 직접 선택합니다.
5. 저장합니다 (Ctrl+S). "Instant mode"가 켜져 있으면 즉시 적용됩니다.

### 방법 2: config.txt 직접 수정

1. `C:\Program Files\EqualizerAPO\config\config.txt`를 메모장으로 엽니다.
2. 아무 줄에나 아래 내용을 추가합니다.
   ```
   Include: AI-PEQ.txt
   ```
3. 저장합니다.

### AI-PEQ.txt 파일이 아예 생성되지 않는 경우

`C:\Program Files\...` 경로는 관리자 권한이 필요할 수 있습니다. 프로그램을 관리자 권한으로 다시 실행하거나, 프로그램 설정 화면에서 Equalizer APO config 폴더 경로를 관리자 권한이 필요 없는 위치로 변경한 뒤, 그 경로를 위 방법으로 Include 해주세요.

## 요구 사항

- Windows
- [Equalizer APO](https://sourceforge.net/projects/equalizerapo/)
- Anthropic, OpenRouter, DeepSeek 등 지원되는 AI 제공자의 API 키
