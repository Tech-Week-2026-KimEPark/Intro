# 실습 노트북 로컬 변경 추적 제외

`TECH-WEEK-26_Physical-AI.ipynb`를 실행하거나 환경에 맞게 수정한 내용을 커밋 대상에서 제외하는 방법입니다. 저장소에는 원본 노트북을 유지합니다.

## 적용 조건

노트북은 Git이 추적하는 파일입니다. 추적 중인 파일은 `.gitignore`에 추가해도 변경이 계속 표시됩니다. `.gitignore`는 `.ipynb_checkpoints/`와 `.venv/`만 제외합니다.

## 작업 순서

저장소를 clone한 PC마다 저장소 루트에서 1회 실행하십시오.

```bash
git update-index --skip-worktree TECH-WEEK-26_Physical-AI.ipynb
```

## 결과 확인

노트북을 실행한 뒤 `git status`를 실행하십시오. 노트북이 변경 목록에 없으면 적용된 상태입니다.

```bash
git ls-files -v | grep '^S'
```

위 명령 결과에 `S TECH-WEEK-26_Physical-AI.ipynb`가 표시되면 설정이 유지된 상태입니다.

## 원본 노트북 갱신 시 복구

원격 저장소의 노트북이 갱신되면 `git pull`이 로컬 변경과 충돌해 중단될 수 있습니다. 설정을 해제하고 로컬 변경을 되돌린 뒤 다시 적용하십시오.

```bash
git update-index --no-skip-worktree TECH-WEEK-26_Physical-AI.ipynb
git restore TECH-WEEK-26_Physical-AI.ipynb
git pull
git update-index --skip-worktree TECH-WEEK-26_Physical-AI.ipynb
```

`git restore`는 로컬 실행 결과와 수정 내용을 삭제합니다. 보관할 내용이 있으면 먼저 다른 파일 이름으로 복사하십시오.
