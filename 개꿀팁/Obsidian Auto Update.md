퇴근 시간에 자동 업데이트 하기 세팅

1. bash 파일 생성
```bash
#!/bin/bash

if [ -z "$SSH_AUTH_SOCK" ]; then
    eval "$(ssh-agent -s)"
fi

HERE=<OBSIDIAN 저장소, 절대 경로>
echo $HERE
cd $HERE

ssh-add <ssh 키 경로>
git stash
git pull origin main
git stash pop
git add .
git commit -m "update"
git push origin main 

# ssh-agent 종료
if [ -n "$SSH_AGENT_PID" ]; then
    eval "$(ssh-agent -k)"
fi
```

2. 윈도우 작업 스케줄러 설정에 추가
	- 이름: 기억하기 쉽게
	- 설명: 설명
	- 트리거: 원하는 주기 (예: 주중 오후 6시)
	- 작업: 프로그램 시작
		- 프로그램/스크립트: "C:\Program Files\Git\bin\bash.exe" -> git bash 위치
		- 인수 추가: "/c/Users/hjseo/vaults/update.sh" -> 1번 배쉬파일 위치
	
3. 끝