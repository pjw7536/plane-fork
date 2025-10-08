# 🌱 Git Rebase & Backup Workflow Guide

이 문서는 로컬 브랜치(`custom`)를 **최신 upstream(`upstream-preview`) 위로 리베이스**하고,  
리베이스 전후로 **백업 브랜치를 생성 및 원격에 안전하게 보관하는 전체 절차**를 정리한 가이드입니다.

---

## 📌 개요

리베이스는 내 작업 브랜치를 최신 원격 코드 위로 다시 쌓아 올려  
"최신 코드 + 내 변경사항" 상태를 유지하기 위한 핵심 작업입니다.  
여기에 백업 브랜치 전략을 더하면 **언제든 되돌릴 수 있는 안전장치**를 확보할 수 있습니다.

---

## ✅ 전체 프로세스 요약

1. upstream 최신 상태 동기화 및 로컬 미러(`upstream-preview`) 업데이트  
2. 작업 브랜치(`custom`)에서 리베이스 전 백업 브랜치 생성  
3. `custom` 브랜치를 최신 기반으로 리베이스  
4. 원격 저장소(`origin`)에 업데이트 및 백업 브랜치 푸시  
5. (문제 발생 시) 백업 브랜치로 복구


# 0) upstream 최신 반영 + 로컬 미러 최신화
git fetch upstream
git switch upstream-preview
git reset --hard upstream/preview

# 1) 작업 브랜치로 이동
git switch custom

# 2) 백업 브랜치 생성
BACKUP="backup/custom-$(date +%Y%m%d-%H%M)"
git branch "$BACKUP"

# 3) 리베이스
git rebase upstream-preview

# 4) 원격 custom 업데이트
git push --force-with-lease origin custom

# 5) 백업 브랜치 원격 저장
git push origin "$BACKUP"



# 문제발생시 복구방법
# 1) 백업 브랜치로 바로 이동
git switch "$BACKUP"

# 2) 또는 custom 브랜치를 백업 시점으로 되돌리기
git switch custom
git reset --hard "$BACKUP"
git push --force-with-lease origin custom
