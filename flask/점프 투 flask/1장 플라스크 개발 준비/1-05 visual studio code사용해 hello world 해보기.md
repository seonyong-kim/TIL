### 1. visual studio에서 project\myproject폴더를 open한다.
### 2. 가상 환경 만들기
  - 새 터미널을 열고
  - 가상환경 생성
    ```
    python -m venv venv
    ```
  - 가상환경 활성화
    ```
    source venv/Scripts/activate
    ```
  - 수행이 잘 되었다면 (venv)가 떠야한다
### 3. Flaks 설치하기 
```
pip install flask
```
### 4. Hello World! 띄워보기
```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_pybo():
  return 'Hello, Pybo!'
```

```
flask --app hello_world.py run
```
로 실행
