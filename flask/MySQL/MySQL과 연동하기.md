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
    password="본인의 password",
    database="사용하려는 DB이름"
    )

def close(cursor, db_connection): # 사용 끝나면 DB 종료
    return cursor.close() ; db_connection.close()
```
## REST API 활용하기
### GET 활용해보기 
```
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
다만 데이터는 sql에 미리 넣어야 데이터가 return된다.

### POST
```
@app.route('/setting/favorites', methods=['POST'])
def createFavorite():
    # db와 연동하기
    db_connection = connection()
    cursor = db_connection.cursor() # 튜플로 결과가 아나온다.
    request_data = request.get_json() # request : 클라이언트의 요청 내용이 담김 -> json으로 파싱해 python 딕셔너리로 바꾼다.
    # POST
    insert_query = "INSERT INTO favorite(address,name) VALUES(%s,%s)" # %s는 파라미터 바인딩에 쓰이며, SQL 인젝션 방지에 도움을 준다.                               
    data = (request_data['address'],request_data['name']) # data는 실제 입력할 값의 튜플.
    cursor.execute(insert_query,data)
    db_connection.commit() # 이걸 해야 적용된다. 안하면 rollback상태로 남아서 DB에 반영 안된다.
    # return
    cursor = db_connection.cursor(dictionary=True) # 딕셔너리 형태로 반환
    output_query = 'SELECT * FROM favorite'
    cursor.execute(output_query)
    output = cursor.fetchall()
    # close
    close(cursor, db_connection)

    return jsonify(output)
```
postman에서 test해보면 POST + 주소로 설정 후 <br>
Body에서 raw선택 후
```
{
  "address": "부산광역시 어딘가에 위치한 곳",
  "name": "부모님 집"
}
```
이런식으로 넣고자 하는 데이터를 json형식으로 넣는다. <br> 결과를 보면 <br>
![image](https://github.com/user-attachments/assets/d7341db3-b6d9-40f2-9a36-44d5236a053b) <br>
잘 반영된것을 알 수 있다. 

### PUT
```
@app.route('/setting/favorites/<int:id>', methods = ['PUT'])
def changeFavorite(id):
    db_connection = connection()
    cursor = db_connection.cursor()
    request_data = request.get_json()
    # PUT
    put_query = 'UPDATE favorite SET address=%s, name=%s WHERE id=%s'
    data = (request_data['address'], request_data['name'], str(id))
    cursor.execute(put_query, data)
    db_connection.commit() 
    # return
    cursor = db_connection.cursor(dictionary=True)
    output_query = 'SELECT * FROM favorite'
    cursor.execute(output_query)
    output = cursor.fetchall()
    # close
    close(cursor, db_connection)
    return jsonify(output)
```
postman에서 test해보면 PUT + 주소로 설정 후 <br>
Body에서 raw선택 후
```
{
  "address": "서울특별시 00구 00동 어딘가",
  "name": "부모님 집"
}
```
변경할 데이터를 json형식으로 넣는다. <br> 결과를 보면 <br>
![image](https://github.com/user-attachments/assets/66b00a0a-9ec1-4f6a-9e1c-4973b102a96e) <br>
잘 반영된것을 알 수 있다. 

### DELETE
```
@app.route('/setting/favorites/<int:id>', methods=['DELETE'])
def deleteFavorite(id):
    db_connection = connection()
    cursor = db_connection.cursor()
    # return
    query = 'SELECT * FROM favorite WHERE id = %s'
    cursor.execute(query, (id,))
    result = cursor.fetchall()
    # DELETE
    deleteQuery = 'DELETE FROM favorite WHERE id = %s'
    cursor.execute(deleteQuery,(id,))
    db_connection.commit()
    #close
    close(cursor, db_connection)
    return jsonify(result)
```

postman에서 test해보면 DELETE + 주소로 설정 후 <br>
Body에서 none선택 후 결과를 보면 <br>
![image](https://github.com/user-attachments/assets/3dbef8a1-0e1f-4bbc-93e4-677fa4c5a4de) <br>
잘 반영된것을 알 수 있다. 
이제 서버에서의 DB연결 및 동작이 끝났다. <br>
다음으로 할것은 프론트 <-> 서버 + DB 연결을 해보겠다.
