---
layout: post
published: false
title: flutter doctor - cocoapods 설치 오류
---
## cocoapods 설치 시 권한 문제

cocoapods 설치 시 디렉토리 권한 문제로 아래와 같은 ERROR가 발생하며 설치가 진행되지 않을 경우

ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /usr/bin directory.
    
    

sudo gem install cocoapods -n /usr/local/bin

위 명령어를 통해 설치를 진행한다.