# COMMAND CHEATSHEET — 셸·Git·실행·포트·브라우저

> **아래의 `bash` 명령은 실제 터미널(Bash shell)에 입력합니다.** 자연어 작업 지시는 Codex 채팅창에 입력합니다. 환경이 일반 Linux 셸이 아닐 수도 있으니 명령을 그대로 맹목 실행하지 말고 기존 README·사용 가능한 도구를 확인하세요.

## 1. 조사: 바꾸기 전에 읽기

```bash
pwd                               # 현재 폴더
ls -la                            # 숨김 파일 포함 목록
find . -maxdepth 2 -type f | head -80
cat README.md                     # 파일이 있을 때만
head -80 README.md                # 파일 첫 부분
git status --short                # Git 저장소인 경우
python --version                  # python3만 있을 수도 있음
node --version                    # Node 프로젝트인 경우
npm run                           # package.json 스크립트 목록
```

먼저 과제 폴더가 맞는지, 서버/DB/데이터 파일/기존 코드가 어디인지 확인합니다. `find` 또는 Git이 없을 수도 있습니다.

## 2. GitHub Public Toolkit clone/업데이트

```bash
# 실제로 반입·네트워크가 허용된 환경과 위치에서
# <...>는 문자열 그대로 실행하지 말고 실제 URL/경로로 교체
git clone <PUBLIC_REPOSITORY_URL> ai-hackathon-toolkit

# 기존 clone이 있을 때, 로컬 변경이 없음을 확인한 후
git -C ai-hackathon-toolkit status --short
git -C ai-hackathon-toolkit pull --ff-only
```

GitHub 웹에서 편집하고 **Commit changes** 한 내용은 **다음 clone에서는 최신 상태**로 내려받습니다. 기존 clone은 자동 갱신되지 않습니다. 로컬 수정/충돌이 있으면 `pull`이 실패할 수 있습니다. 공개 저장소에 과제 코드·실제 파일·대화·데이터를 커밋하지 않습니다. 커밋 전 `git status --short`와 `git diff --cached --name-only`를 확인하세요. `.gitignore`만으로 모든 누출을 막을 수는 없습니다.

**경로 주의:** 아래 예시의 `outputs/`가 공식 제출 전용 폴더이면, 내부 보고서는 허용된 비제출 폴더로 바꿉니다. 실제 제출 파일 이름·구조는 공식 지시를 우선합니다.

## 3. 선택적 EDA 스크립트와 자연어 EDA

- 자연어 EDA: `PROMPTS.md`의 **3단계**를 Codex에 입력 → Codex가 환경별 코드를 **실행**하고 `outputs/EDA_REPORT.md`에 결과를 작성. **스크립트 불필요**.
- CSV만 필요 시: 아래 스크립트를 과제 폴더에서 실행합니다. 입력 원본은 수정하지 않습니다. 결과 JSON은 **선택적**이고 내부 과제 작업 공간에만 저장합니다.

```bash
python /path/to/ai-hackathon-toolkit/scripts/profile_csv.py --self-test
python /path/to/ai-hackathon-toolkit/scripts/profile_csv.py ./data/input.csv --out ./outputs/data_profile.json
# 옵션: --encoding utf-8-sig --delimiter tab
```

`profile_csv.py`는 CSV 기본 구조 점검용이며 필수 ID의 유일성·업무 제약·이상치의 의미를 자동 검증하지 않습니다. 파일명/컬럼명/경로가 결과에 들어갈 수 있으므로 결과를 공개하지 않습니다. CSV가 아니면 이 스크립트를 사용하지 않습니다.

## 4. 실제 프로젝트 실행 (기존 설정 확인 후)

```bash
python path/to/app.py             # Python 스크립트 프로젝트인 경우
python -m pytest -q              # pytest가 설치·설정된 경우
npm run                          # npm scripts 조사
npm run dev                      # 실제 dev 스크립트가 존재할 때
curl -i http://localhost:PORT/   # 실제 PORT와 URL로 교체
ss -lntp                         # Linux 리스닝 포트, 지원 시
ps aux                           # 실행 프로세스
```

FastAPI라면 `uvicorn 모듈:app` 형태를 쓸 수 있지만 **모듈명과 포트는 실제 설정**을 봐야 합니다. 예시 `python -m uvicorn main:app --host 0.0.0.0 --port 8000`은 **공식 실행 명령이 아닙니다**. 프론트엔드가 별도로 필요하지 않을 수도 있고, 서버 없는 CLI/CSV 결과 과제도 가능합니다.

## 5. Port 3000/8000, localhost, 0.0.0.0 차이

| 용어 | 실제 뜻 | 주의 |
|---|---|---|
| `:3000`, `:8000` | 프로세스가 통신을 받는 **포트 번호** 예시 | 해당 환경의 고정 정답 포트 아님 |
| `localhost` / `127.0.0.1` | 요청을 보내는 **그 환경 내부 자신** | 클라우드 IDE와 사용자 PC는 서로 다른 localhost일 수 있음 |
| `0.0.0.0` (서버 바인딩) | 서버가 사용 가능한 네트워크 인터페이스에서 연결을 받도록 설정 | 외부 접근 주소 자체가 아님; 정책/포워딩 확인 필요 |
| Frontend | 화면/상호작용 담당 | Node/React 등, 아예 없을 수도 있음 |
| Backend/API | 데이터·업무 계산과 응답 담당 | 별도 프로세스가 아닐 수도 있음 |
| Browser Preview | IDE가 제공하는 미리보기/포트 접속 방식 | 플랫폼별 URL·포워딩·버튼 확인 필요 |

### 서버·브라우저 확인 순서

1. **Codex**에게 `README`, `package.json`, Python/Docker 설정을 읽고 실행 명령·실제 포트를 확인하도록 요청.
2. **셸**에서 실제 명령으로 서버 실행. 터미널이 실행 중인 서버 프로세스에 점유될 수 있으므로 필요하면 별도 터미널 사용.
3. 터미널에 출력된 **실제 로그와 URL/포트** 확인. 서버 실행 실패라면 로그부터 점검.
4. CLI에서 가능한 경우 `curl -i http://localhost:<실제 포트>/<실제 경로>`로 실제 응답을 확인.
5. **Browser Preview**에서 포트/미리보기 URL을 연결하는 플랫폼 방식을 확인하고 페이지를 직접 열어 입력·버튼·데이터/다운로드 흐름을 확인.
6. 같은 화면이 보이지 않으면 주소가 틀린 것인지, 포트 포워딩이 없는 것인지, 서버가 종료됐는지, Frontend가 API에 접근 못 하는지 나눠 확인.

**Codex가 '정상 실행'이라고 말하는 것과 사용자가 Browser Preview에서 확인한 것은 별도 증거입니다.** UI 검증을 실제 수행하지 못했다면 `NOT TESTED`.

## 6. Docker — 공식 구성에 있을 때만

```bash
docker --version
docker ps -a
docker logs <CONTAINER_ID_OR_NAME>
docker compose config          # compose 파일이 있는 경우
```

Docker가 설치됐다고 항상 사용해야 하는 것은 아닙니다. `Dockerfile`/Compose/README 설정 및 포트 매핑을 확인합니다. 데이터나 볼륨을 삭제하는 명령을 승인 없이 실행하지 않습니다.

## 7. 세션 공유 점검과 위험한 명령

```bash
# Bash 1: 과제 폴더에서 새 파일임을 확인한 뒤
if [ ! -e .shared_check.tmp ]; then printf 'shared-check\n' > .shared_check.tmp; fi
# Bash 2: 동일한 과제 폴더에서
cat .shared_check.tmp
# Bash 1: 직접 만든 파일임을 확인하고 정리
rm .shared_check.tmp
```

별도 세션의 대화는 자동 공유되지 않습니다. 파일 공유를 확인한 후 원본/결과 경로와 실제 실행 명령을 전달합니다. 무작정 `rm -rf`, `git reset --hard`, `git clean -fd`, DB 삭제·볼륨 삭제를 하지 않습니다.
