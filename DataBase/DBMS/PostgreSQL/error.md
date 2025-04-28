# 발생하는 error에 대해 찾은 해결법을 작성

## 1. PostgreSQL을 키면 server에 접속할때 비밀번호를 입력하는데 
**connection failed: could not receive data from server: Socket is not connected (0x00002749/10057) <br>
could not send SSL negotiation packet: Socket is not connected (0x00002749/10057)**
라는 error가 발생 <br>
- 해결 방안 :
1. window + R 키로 서비스 관리자를 열고 services.msc를 입력한다
2. PostgreSQL 13 비슷한게 running중인지 확인하고 아니면 실행시켜주면 잘 해결된다.

## 123
