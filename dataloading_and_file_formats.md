# Data Loading and File Formats
## Reading & Writing Data in Text Format
- read_csv(header=None, names=[열이름], indexl_col=[인덱스로 사용할 이미 있는 열 이름], sep(=delimeter)=' 쪼갤 기준 ', skiprows=[csv로부터 읽어들이지 않을 행], na_values = 결측값으로 지정하고 싶은 데이터 목록,
parse_dates = True : 연월일형식의 데이터를 인덱스로 지정하면서 그 데이터를 날짜 타입의 데이터로 바꿈, nrows= 가져올 열의 개수 )  
- read_table(sep=' , ')
```
df = pd.read_csv('ex1.csv')
df
=>
a b c d message 
0 1 2 3 4 hello 
1 5 6 7 8 world 
2 9 10 11 12 foo

df = pd.read_table('ex1.csv')
df

	a,b,c,d,message
0	1,2,3,4,hello
1	5,6,7,8,world
2	9,10,11,12,foo

#read_table은 sep= 옵션을 주지 않으면 줄마다 읽어서 표로 만들어 반환

pd.read_csv('ex2.csv')
  
  1 2 3 4 hello 
0 5 6 7 8 world
1 9 10 11 12 foo

pd.read_csv('ex2.csv', head=None)

	0 1 2 3 4
0   1 2 3 4 hello
1 	5 6 7 8 world 
2 	9 10 11 12 foo

#header=None 옵션: csv 파일의 맨 윗줄을 header로 쓰지 않기

pd.read_csv('ex2.csv', names=['a', 'b', 'c', 'd', 'message'])
  a b c d message
0 1 2 3 4 hello 
1 5 6 7 8 world 
2 9 10 11 12 foo

pd.read_csv('ch06/ex2.csv', names=['a', 'b', 'c', 'd', 'message'], index_col='message')
	  a b c d 
message 
hello 1 2 3 4 
world 5 6 7 8 
foo 9 10 11 12

pd.read_csv('ex2.csv', names=['a', 'b', 'c', 'd'], indexl_col='message')
---------------------------------------------------------------------------
TypeError                Traceback (most recent call last)
<ipython-input-14-bdc5f5405620> in <module>()
----> 1  pd.read_csv('ex2.csv', names=['a', 'b', 'c', 'd'], indexl_col='message')

TypeError: parser_f() got an unexpected keyword argument 'indexl_col'

# 'message'가 names에 없기때문에 발생하는 오류

csv_mindex.csv
key1,key2,value1,value2 
one,a,1,2 
one,b,3,4 
one,c,5,6 
one,d,7,8 
two,a,9,10 
two,b,11,12 
two,c,13,14 
two,d,15,16

parsed = pd.read_csv('ch06/csv_mindex.csv', index_col=['key1', 'key2'])
parsed
=>
		  value1 value2
key1 key2
one  a 		1 		2 
	 b 		3 		4 
	 c 		5 		6 
	 d 		7 		8 
two  a 		9 		10 
	 b 		11 		12 
	 c 		13 		14 
	 d 		15 		16

list(open('ch06/ex3.txt'))
[' 		A 			B 			C	\n', 
'aaa -0.264438 -1.026059 -0.619500\n', 
'bbb  0.927272 0.302904 -0.032399\n', 
'ccc -0.264273 -0.386314 -0.217601\n', 
'ddd -0.871858 -0.348382 1.100491\n']

result = pd.read_table('ch06/ex3.txt', sep='\s+')
result
=>
			A 		  B 		C 
aaa -0.264438 -1.026059 -0.619500 
bbb  0.927272 0.302904 -0.032399 
ccc -0.264273 -0.386314 -0.217601 
ddd -0.871858 -0.348382 1.100491

# sep='\s+' : 공백이 1번 이상 있는 지점을 기준으로 table을 만들어라
(\s : white space, + : 1번 이상 반복)

ch06/ex4.csv 
# hey! 
a,b,c,d,message 
# just wanted to make things more difficult for you 
# who reads CSV files with computers, anyway? 
1,2,3,4,hello 
5,6,7,8,world 
9,10,11,12,foo

pd.read_csv('ch06/ex4.csv', skiprows=[0, 2, 3])
=>
  a b c d message
0 1 2 3 4 hello 
1 5 6 7 8 world 
2 9 10 11 12 foo

ch06/ex5.csv 
something,a,b,c,d,message 
one,1,2,3,4,NA 
two,5,6,,8,world 
three,9,10,11,12,foo

result = pd.read_csv('ch06/ex5.csv')
result
=>
something a b c d message 
0 one 1 2 3 4 NaN
1 two 5 6 NaN 8 world 
2 three 9 10 11 12 foo

sentinels = {'message': ['foo', 'NA'], 'something': ['two']}
pd.read_csv('ch06/ex5.csv', na_values=sentinels)
=>
something a b c d message 
0 one 1 2 3 4 NaN 
1 NaN 5 6 NaN 8 world 
2 three 9 10 11 12 NaN

#na_values = 딕셔너리로 열과 데이터를 지정해서 그 데이터를 NA값으로 
지정
```
## Writing Data Out to Text Format
- dataframe or series.to_csv('파일이름', index=True, header=True, cols=[]) : data를 csv 파일로 만듦
- dataframe or series.from_csv('파일이름', parse_dates= (연월일 형식의 데이터를 날짜 데이터로 읽을것인가?)
```
data.to_csv(sys.stdout, sep='|')
=>
|something|a|b|c|d|message 0|one|1|2|3.0|4| 1|two|5|6||8|world 2|three|9|10|11.0|12|foo

# sys.stout 옵션 : 실제 파일을 내보내는 것이 아니라 형식에 맞게
'보여주기' 만 함

dates = pd.date_range('1/1/2000', periods=7)
ts = Series(np.arange(7), index=dates)
ts.to_csv('ch06/tseries.csv')
ch06/tseries.csv 
=>
2000-01-01,0
2000-01-02,1 
2000-01-03,2 
2000-01-04,3 
2000-01-05,4 
2000-01-06,5 
2000-01-07,6

#pd.date_range(start, end, periods=) : 연월일 형식의 일련의 데이터

Series.from_csv('ch06/tseries.csv', parse_dates=True)
2000-01-01 0 
2000-01-02 1 
2000-01-03 2 
2000-01-04 3 
2000-01-05 4 
2000-01-06 5 
2000-01-07 6
```

## Manually Working with Delimited Formats
- 문자가 하나인 구분자로 구분된 파일이 있으면, csv 모듈 사용
- csv.reader(file, dialect='') <dialect : parameter의 집합. class나 list 형태, delimeter의 기본값은 ' , '>
- csv.writer(file, dialect="")

```
ch06/ex7.csv 
"a","b","c" 
"1","2","3" 
"1","2","3","4"

import csv 
f = open('ch06/ex7.csv') 
reader = csv.reader(f)
reader
=>
<_csv.reader at 0x29f0d97ca70>
# csv reader 객체주소. 실제 데이터를 읽으려면 자료형을 변환하거나 줄마다 접근

for line in reader: 
	print line 
['a', 'b', 'c'] 
['1', '2', '3'] 
['1', '2', '3', '4']

lines = list(csv.reader(open('ch06/ex7.csv')))
header, values = lines[0], lines[1:]
data_dict = {h: v for h, v in zip(header, zip(*values))}
data_dict
=>
{'a': ('1', '1'), 'b': ('2', '2'), 'c': ('3', '3')}

class my_dialect(csv.Dialect): 
	lineterminator = '\n' # 줄 단위 구분
	delimiter = ';' # 구분자 단위 구분
	quotechar = '"' # 인용부호
reader = csv.reader(f, dialect=my_dialect)

with open('mydata.csv', 'w') as f: 
	writer = csv.writer(f, dialect=my_dialect)
	writer.writerow(('one', 'two', 'three')) 
	writer.writerow(('1', '2', '3')) 
	writer.writerow(('4', '5', '6')) 
	writer.writerow(('7', '8', '9'))
```
## JSON Data
> name/value 형태의 쌍으로 collection 타입
> 값들의 순서화된 리스트.
- json module : json.loads(json data 불러오기), json.dumps(python data를 json data로 바꾸기)
```
obj = """ {"name": "Wes", "places_lived": ["United States",
		 "Spain", "Germany"], 
		 "pet": null, 
		 "siblings": [{"name": "Scott", "age": 25, "pet":
		  "Zuko"}, {"name": "Katie", "age": 33, "pet":
		  "Cisco"}] } """
import json
result = json.loads(obj)
result
=>
{'name': 'Wes',
 'pet': None,
 'places_lived': ['United States', 'Spain', 'Germany'],
 'siblings': [{'age': 25, 'name': 'Scott', 'pet': 'Zuko'},
  {'age': 33, 'name': 'Katie', 'pet': 'Cisco'}]}
asjson = json.dumps(result)
asjson
=>
'{"name": "Wes", "places_lived": ["United States", "Spain", "Germany"], "pet": null, "siblings": [{"name": "Scott", "age": 25, "pet": "Zuko"}, {"name": "Katie", "age": 33, "pet": "Cisco"}]}'
```

## XML and HTML: Web Scraping
> xml : 
> 1. XML은 HTML과 매우 비슷한 문자 기반의 마크업 언어(text-based markup language)
>2. HTML처럼 데이터를 보여주는 목적이 아닌, 데이터를 저장하고 전달할 목적
>3. 새로운 태그를 만들어 추가해도 계속해서 동작하므로, 확장성이 우수
>4. XML 문서 내의 모든 것은 노드(node)라고 불리는 계층적 단위에 정보를 담고있음

lxml.html / lxml.objectify
```
# xml 문서 파싱
from lxml.html import parse
from urllib.request import urlopen
parsed = parse(urlopen('https://kin.naver.com/index.nhn'))
doc = parsed.getroot()

links = doc.findall('.//a')
# .// : 현재 노드의 하위 노드를 모두 선택함. 
# findall : './/a'에 매칭되는 노드를 리스트로 반환

links[15:20]
=>
[<Element a at 0x29f04cefa98>,
 <Element a at 0x29f04cefae8>,
 <Element a at 0x29f04cefb38>,
 <Element a at 0x29f04cefb88>,
 <Element a at 0x29f04cefbd8>]
 
lnk = links[28]
lnk.get('href') # Ink의 'href'의 속성에 해당되는 값을 return 
=>
'/qna/list.nhn?dirId=10'
lnk.text_content() # 해당 노드의 text값 return
=>
'스포츠, 레저'
```
- table 로부터 data 뽑아오기
```
from lxml.html import parse
from urllib.request import urlopen
parsed = parse(urlopen('https://finance.yahoo.com/quote/AAPL/options?ltr=1'))
doc = parsed.getroot()

tables = doc.findall('.//table') # 하위노드 table의 노드 리스트
calls = tables[0] #첫번째 table
puts = tables[1] #두번째 table

rows = calls.findall('.//tr') # 첫번째 table의 행들

def _unpack(row, kind='td'): # td : 한 행의 데이터 하나하나
	elts = row.findall('.//%s' % kind)  # 원하는 종류의 노드
	return [val.text_content() for val in elts] # 그 노드의 text 리스트

_unpack(rows[0], kind='th') #첫번째 table의 첫 번째 행의 header
=> ['Contract Name',
 'Last Trade Date',
 'Strike',
 'Last Price',
 'Bid',
 'Ask',
 'Change',
 '% Change',
 'Volume',
 'Open Interest',
 'Implied Volatility']

_unpack(rows[1], kind='td') # 첫번째 table의 두 번째 행의 data
['AAPL180803C00162500',
 '2018-07-20 11:45PM EDT',
 '162.50',
 '30.09',
 '27.15',
 '31.00',
 '0.00',
 '-',
 '2',
 '2',
 '78.96%']

def parse_options_data(table):
 rows = table.findall('.//tr')
 header = _unpack(rows[0], kind='th')
 data = [_unpack(r) for r in rows[1:]]
 return TextParser(data, names=header).get_chunk()
 # 이름이 header고 data는 앞에서 뽑은 data로 이루어진 dataframe 반환

call_data = parse_options_data(calls)
call_data[:5]

	Contract Name	Last Trade Date	Strike	Last Price	Bid	Ask	Change %Change	Volume	Open Interest	Implied  
	Volatility
0	AAPL180803C00162500	2018-07-20 11:45PM EDT162.530.0927.1531.000.0-2278.96%
1	AAPL180803C001675002018-07-27 6:00PM EDT167.523.8023.5523.900.0-972055.37%
2	AAPL180803C002250002018-07-27 6:00PM EDT225.00.010.000.030.0-12412548.05%
3	AAPL180803C002275002018-07-27 6:00PM EDT227.50.020.000.020.0-2048.44%
4	AAPL180803C002300002018-07-27 6:00PM EDT230.00.010.000.020.0-81051.17%
```

- Parsing XML with lxml.objectify
```
from lxml import objectify
path = 'Performance_MNR.xml'
parsed = objectify.parse(open(path))
root = parsed.getroot()

data = []
skip_fields = ['PARENT_SEQ', 'INDICATOR_SEQ',
 'DESIRED_CHANGE', 'DECIMAL_PLACES']
for elt in root.INDICATOR:
     el_data = {}
     for child in elt.getchildren():
         if child.tag in skip_fields:
             continue # 건너뛰고 반복문 다음 단계 실행
         el_data[child.tag] = child.pyval
     data.append(el_data)
data
=>
[{'AGENCY_NAME': 'Metro-North Railroad',
  'CATEGORY': 'Service Indicators',
  'DESCRIPTION': 'Percent of commuter trains that arrive at their destinations within 5 minutes and 59 seconds of the scheduled time. West of Hudson services include the Pascack Valley and Port Jervis lines. Metro-North Railroad contracts with New Jersey Transit to operate service on these lines.\n',
  ......

import io
tag = '<a href="http://www.google.com">Google</a>'
root = objectify.parse(io.StringIO(tag)).getroot()
root.get('href')
=>
'http://www.google.com'
root.text
=> 'Google' 
```
## Binary Data Formats
- pickle : 텍스트 이외의 자료형을 파일로 저장하기 위하여 `pickle`이라는 포맷으로 저장
- pickle 모듈 / 입력 : pickle.dump(data, file) , 출력 : pickle.load(file)
```
import pickle

frame = pd.read_csv('ch06/ex1.csv')
frame
=>
  a b c d message 
0 1 2 3 4 hello 
1 5 6 7 8 world 
2 9 10 11 12 foo

with open('frame.txt', 'wb') as f:
   pickle.dump(frame, f)
#wb : binary data 쓰기

with open('frame.txt', 'rb') as f:
    data = pickle.load(f) # 단 한줄씩 읽어옴
#rb : binary data 읽기

data
=>
  a b c d message 
0 1 2 3 4 hello 
1 5 6 7 8 world 
2 9 10 11 12 foo
```

-  HDF5 Format
> 1. 대용량 데이터를 저장하기 위한 파일 포맷
>  2.  플랫폼 독립적으로 모든 시스템에 사용 가능
    -   플랫 파일(flat file) 형태
	-   대용량의 복합 데이터 사용 가능
    -   계층적 트리와 내장 스키마(schema)
	-   유연하고 빠른 입출력
    -   서브셋 접근, out-of-core 연산, 노드별 압축

## Reading Excel Files
 - pd.ExcelFile('a.xls')
 - excel_file.parse('시트이름') 
excel file을 먼저 불러오고, 시트 데이터를 읽을 때 parsing을 추가적으로 함

## Interacting with Databases
- import sqlite3
```
import sqlite3

query = """
CREATE TABLE test
(a VARCHAR(20), b VARCHAR(20),
 c REAL, d INTEGER
);"""
con = sqlite3.connect(':memory:') # SQLite DB 연결
con.execute(query) #query 실행
con.commit()

data = [('Atlanta', 'Georgia', 1.25, 6),
 ('Tallahassee', 'Florida', 2.6, 3),
 ('Sacramento', 'California', 1.7, 5)]
stmt = "INSERT INTO test VALUES(?, ?, ?, ?)"
con.executemany(stmt, data) # 복수의 data 넣기
con.commit()

cursor = con.execute('select * from test')
rows = cursor.fetchall() # 모든 데이터를 한꺼번에 가져올때 
<-> fechone() : rows마다 가져올 때
rows
=>

[('Atlanta', 'Georgia', 1.25, 6),
 ('Tallahassee', 'Florida', 2.6, 3),
 ('Sacramento', 'California', 1.7, 5),
 ('Atlanta', 'Georgia', 1.25, 6),
 ('Tallahassee', 'Florida', 2.6, 3),
 ('Sacramento', 'California', 1.7, 5)]

cursor.description
=>
(('a', None, None, None, None, None, None),
 ('b', None, None, None, None, None, None),
 ('c', None, None, None, None, None, None),
 ('d', None, None, None, None, None, None))
pd.DataFrame(rows, columns=list(zip(*cursor.description))[0])
=>
	a b c d 
0 Atlanta Georgia 1.25 6 
1 Tallahassee Florida 2.60 3 
2 Sacramento California 1.70 5
```

## Storing and Loading Data in MongoDB
- import pymongo
```
import pymongo

username = 'davelee'
password = 'korea123'
connection = pymongo.MongoClient('mongodb://%s:%s@www.funcoding.xyz' % (username, password)) # db서버에 연결

db = connection.test #test database 사용
db
=>
Database(MongoClient(host=['www.funcoding.xyz:27017'], document_class=dict, tz_aware=False, connect=True), 'test')

test_collection = db.test_collection # test database에 test_collection 이라는 collection 생성
test_collection
=>
Collection(Database(MongoClient(host=['www.funcoding.xyz:27017'], document_class=dict, tz_aware=False, connect=True), 'test'), 'test_collection')

post =  {"author":  "Mike",  "text":  "My first blog post!",  "tags":  ["mongodb",  "python",  "pymongo"]  }
post_id = test_collection.insert_one(post).inserted_id
post_id
=>
ObjectId('5a0ac8dd81f6402f8ff0569f')

test_insert_collection = db.test_insert #test database에 test_insert collection 만들기

test_insert_collection.insert_one({'title'  :  '암살',  'castings'  :  ['이정재',  '전지현',  '하정우']})
test_insert_collection.insert_one({'title'  :  '실미도',  'castings'  :  ['설경구',  '안성기'],
'datetime'  :  {'year'  :  '2003',  'month'  :  3,
'val'  :  {'a'  :{'b'  :  1}}}})
# 데이터 하나하나씩 넣기. 딕셔너리 형태

for result in test_insert_collection.find(): #데이터 모두 가져오기
# 데이터 하나씩 가져오기 : collection.find_one(조건문)

	print(result)
=>
{'_id': ObjectId('5a01b540c607715c40efc080'), 'title': '암살', 'castings': ['이정재', '전지현', '하정우']}
{'_id': ObjectId('5a01b540c607715c40efc081'), 'title': '실미도', 'castings': ['설경구', '안성기'], 'datetime': {'year': '2003', 'month': 3, 'val': {'a': {'b': 1}}}}
```
