# flask와 MySQL연동하기
## 1. MySQL 설치 및 데이터 베이스 생성
## 2. Flaks와 연동하기

### 아래의 라이브러리 다운받기
```
pip install mysql-connector-python
```
mysql-connector-python 패키지는 MySQL DBMS 서버에 연결가능하도록 여러 메소드들을 제공해줍니다. <br>
이 패키지를 이용해 SQL query문을 활용해 CRUD가 가능합니다.

### 다음으로 아래의 코드를 작성합니다.
```
from flask import Flask, jsonify, request
import mysql.connector

app = Flask(__name__)

mysql.connector.connect(
    host="127.0.0.1",
    user="root",
    password="본인의 password",
    database="사용하려는 DB이름"
    )
```
추가적으로 몇가지 더 설명을 하면
- jsonify는 return을 json형식으로 되도록합니다
- request는 POST 및 PUT시, body의 내용을 읽기 위해 사용됩니다. <br>

### 아래의 코드를 추가를 해줍니다.
```
from flask import Flask, jsonify, request
import mysql.connector

app = Flask(__name__)

def connection(): # SQL 연결을 위한 코드
    return mysql.connector.connect(
    host=""127.0.0.1"",
    user="root",
    password="자신의 pw",
    database="FLASK_BASIC"
    )

def close(cursor, db_connection): # 사용 끝나면 DB 종료
    return cursor.close() ; db_connection.close()
```
## REST API 활용하기
### GET 활용해보기 
```
from flask import Flask, jsonify, request
import mysql.connector

app = Flask(__name__)

def connection(): # SQL 연결을 위한 코드
    return mysql.connector.connect(
    host="127.0.0.1",
    user="root",
    password="자신의 pw",
    database="FLASK_BASIC"
    )

def close(cursor, db_connection): # 사용 끝나면 DB 종료
    cursor.close()
    db_connection.close()

@app.route('/api 주소', methods=['GET'])
def getFavoritesList():
    # db와 연동하기
    db_connection = connection()
    cursor = db_connection.cursor(dictionary=True)
    # GET
    query = 'SELECT * FROM table 이름'
    cursor.execute(query)
    # return
    data = cursor.fetchall()
    # close
    close(cursor, db_connection)

    return jsonify(data)

if __name__ == '__main__':
    app.run(host="127.0.0.1", port=5000, debug=True)
```
이후 Postman에서 GET + http://127.0.0.1:5000/api주소로 테스트 해보면 <br>
![image](https://github.com/user-attachments/assets/1fc9ac16-eb11-4588-9395-2cf6293ce532) <br>
이런식으로 결과가 리턴되는것을 알 수 있다. <br>
다만 데이터는 sql에 미리 넣어나야 데이터가 리턴된다.

### POST
