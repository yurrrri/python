# Pandas

## Series 
> index와 value를 가지고 있는 1차원의 자료구조
```
import pandas as pd
obj = pd.Series([4, 7, -5, 3])
obj
=> 
0    4
1    7
2   -5
3    3
dtype: int64

# 인덱스를 지정해주지 않으면 인덱스가 0부터 (원소의개수-1)까지로 자동으로 할당됨

obj.values
=> array([ 4,  7, -5,  3], dtype=int64)

obj.index
=> RangeIndex(start=0, stop=4, step=1)

obj2 = pd.Series([4, 7, -5, 3], index=['d', 'b', 'a', 'c'])
obj2
=> 
d    4
b    7
a   -5
c    3
dtype: int64

obj2[['c', 'a', 'd']]]
=> 
c    3
a   -5
d    4
dtype: int64
# 인덱스로 자료 접근 가능

sdata = {'Ohio': 35000, 'Texas': 71000, 'Oregon': 16000, 'Utah': 5000}
obj3 = pd.Series(sdata)
obj3
Ohio 35000 
Oregon 16000 
Texas 71000 
Utah 5000

# 딕셔너리로 series 생성 가능

states = ['California', 'Ohio', 'Oregon', 'Texas']
obj4 = pd.Series(sdata, index=states)
obj4
=>
California        NaN
Ohio          35000.0
Oregon        16000.0
Texas         71000.0
dtype: float64

# 이미 존재하는 series의 인덱스를 리스트로 새로 지정 가능함. 
그러나, 기존의 series의 인덱스가 지정한 인덱스에 없는 겨우 value값으로 Nan을 반환함
```
- null값 판단 함수 : pd.isnull() / pd.notnull()

```
pd.isnull(obj4)
=> California     True
Ohio          False
Oregon        False
Texas         False
dtype: bool

pd.notnull(obj4)
=> California    False
Ohio           True
Oregon         True
Texas          True
dtype: bool
```

- series의 이름, index의 이름 지정 함수 : name / index.name
```
obj4.name = 'population'
obj4.index.name = 'state'
obj4
=>
state
California        NaN
Ohio          35000.0
Oregon        16000.0
Texas         71000.0
Name: population, dtype: float64
```
- Sorting and Ranking : series.order() / series.sort_index(axis=0, ascending=True)
										series.rank()

```
obj = Series([4, 7, -3, 2])
obj.order()
=> 
2 -3 
3 2 
0 4 
1 7

# series.order() : value값을 기본값 오름차순으로 정렬함. 이때 value가 NaN인 데이터는
가장 끝에 정렬

obj = Series(range(4), index=['d', 'a', 'b', 'c'])
obj.sort_index()
=>
a 1 
b 2 
c 3 
d 0

# series.sort_index() : index로 오름차순 정렬

obj = Series([7, -5, 7, 4, 2, 0, 4])
obj.rank()
=>
0 6.5 
1 1.0 
2 6.5 
3 4.5 #4가 2번 나왔는데 4위와 5위 이므로 4+5/2 = 4.5를 똑같이 할당
4 3.0 
5 2.0 
6 4.5

obj.rank(method='first')
=>
0 6 
1 1 
2 7 
3 4 
4 3 
5 2 
6 5

# series.rank() : value를 오름차순으로 정렬했을 때의 순위. 만약에 value가 똑같으면 순위들끼리 평균을 계산하여 평균으로 rank를 할당함. method='first' 옵션을 주면
원래의 순위로 할당, method='max' 옵션을 주면 순위가 될 수 있는 최댓값으로 rank 할당
method='min' 옵션을 주면 순위가 될 수 있는 최솟값으로 rank 할당	

obj.rank(ascending=False, method='max')
=>
0 2 #7이 value의 최댓값이면서 2개가 있으므로 순위 1,2위 중 2위가 최댓값이므로 2로 할당
1 7 
2 2 
3 4 
4 5 
5 6 
6 4
```

- Unique Values, Value Counts, and Membership :
 series.unique() : 유일값 반환 / series.value_counts(sort=True) : value의 개수 반환 (sort=True 옵션은 value의 개수를 기준으로 sorting하는 옵션임)
 
```
obj = pd.Series(['c', 'a', 'd', 'a', 'a', 'b', 'b', 'c', 'c'])
uniques = obj.unique()
uniques
=>
array(['c', 'a', 'd', 'b'], dtype=object)

obj.value_counts()
=>
c 3 
a 3 
b 2 
d 1

mask = obj.isin(['b', 'c'])
mask
=>
0     True
1    False
2    False
3    False
4    False
5     True
6     True
7     True
8     True
dtype: bool

obj[mask]
=>
0    c
5    b
6    b
7    c
8    c
dtype: object

```

## DataFrame
> 여러 개의 column으로 구성된 2차원의 자료구조
```
data = {'state': ['Ohio', 'Ohio', 'Ohio', 'Nevada', 'Nevada'],
 'year': [2000, 2001, 2002, 2001, 2002],
 'pop': [1.5, 1.7, 3.6, 2.4, 2.9]}
frame = pd.DataFrame(data)
frame
=>
  pop state year
0 1.5 Ohio 2000 
1 1.7 Ohio 2001 
2 3.6 Ohio 2002 
3 2.4 Nevada 2001 
4 2.9 Nevada 2002

#데이터 프레임을 생성하는 가장 쉬운 방법은 딕셔너리 생성 후 pd.DataFrame(딕셔너리)

pd.DataFrame(data, columns=['year', 'state', 'pop'])
=>
  year state pop 
0 2000 Ohio 1.5 
1 2001 Ohio 1.7 
2 2002 Ohio 3.6 
3 2001 Nevada 2.4 
4 2002 Nevada 2.9

# 열의 이름 지정 : columns=[ ] 옵션

frame2 = DataFrame(data, columns=['year', 'state', 'pop', 'debt'],
						 index=['one', 'two', 'three', 'four', 'five'])
frame2
=>
    year state pop debt 
one 2000 Ohio 1.5 NaN 
two 2001 Ohio 1.7 NaN 
three 2002 Ohio 3.6 NaN 
four 2001 Nevada 2.4 NaN 
five 2002 Nevada 2.9 NaN

# 인덱스 지정: index=[] 옵션
```
- DataFrame 접근

열: dataframe[열] / dataframe.열
행: dataframe.ix[인덱스] (이때 인덱스는 끝의 인덱스 포함함), 인덱스 라벨 둘다 가능
    dataframe[인덱스] ( 이때 인덱스는 끝의 인덱스 포함 X)
    dataframe.iloc[인덱스] (끝의 인덱스 포함 X) , 라벨로 찾을 수 없음
    dataframe.loc[라벨], 인덱스로 찾을 수 없음
행, 열 : dataframe.ix[인덱스, 열]
  
```
frame2['state']
=>
one        Ohio
two        Ohio
three      Ohio
four     Nevada
five     Nevada
Name: state, dtype: object

frame2.state
=>
one        Ohio
two        Ohio
three      Ohio
four     Nevada
five     Nevada
Name: state, dtype: object

frame2.ix['three']
=>
year 2002
state Ohio 
pop 3.6 
debt NaN 
Name: three

frame2['debt'] = 16.5
=>
    year state pop debt 
one 2000 Ohio 1.5 16.5
two 2001 Ohio 1.7 16.5 
three 2002 Ohio 3.6 16.5 
four 2001 Nevada 2.4 16.5 
five 2002 Nevada 2.9 16.5

# 기존에 없던 새로운 인덱스 추가 가능

del frame2['debt']
frame2.columns
=>
Index(['year', 'state', 'pop'], dtype='object')

# 기존의 인덱스 삭제 : del dataframe[인덱스]

frame2.values
=>
array([[2000, 'Ohio', 1.5],
       [2001, 'Ohio', 1.7],
       [2002, 'Ohio', 3.6],
       [2001, 'Nevada', 2.4],
       [2002, 'Nevada', 2.9]], dtype=object)

# 데이타 프레임의 value값 반환 : dataframe.values => array 자료형
```

- 인덱스 이름 지정 : dataframe.index.name / 열 이름 지정 : dataframe.columns.name
```
frame3.index.name = 'year'; frame3.columns.name = 'state'
frame3
=>
state Nevada Ohio 
year 
2000 NaN 1.5 
2001 2.4 1.7 
2002 2.9 3.6
```

- Value Counts

```
 data = pd.DataFrame({'Qu1': [1, 3, 4, 3, 4],
 .....: 'Qu2': [2, 3, 1, 2, 3],
 .....: 'Qu3': [1, 5, 2, 4, 4]})
 result = data.apply(pd.value_counts).fillna(0)
 
 # data에 pd.value_counts 함수를 적용하고, NA값은 0으로 채워서 데이터프레임으로 반환

 result
   Qu1 Qu2 Qu3 
 1 1 1 1 
 2 0 2 1 
 3 2 2 0 
 4 2 0 2 
 5 0 0 1

```

## DataFrame & Series 공통

-  Index Objects
```
obj = pd.Series(range(3), index=['a', 'b', 'c'])
index = obj.index
index
=>
Index([a, b, c], dtype=object)

#이 때 index 객체는 자료값을 수정할 수 없는 객체임
index[1] = 'd' => 오류 발생!
```

- Reindexing : dataframe.reindex
```
obj = pd.Series([4.5, 7.2, -5.3, 3.6], index=['d', 'b', 'a', 'c'])
obj
=>
d    4.5
b    7.2
a   -5.3
c    3.6
dtype: float64

obj2 = obj.reindex(['a', 'b', 'c', 'd', 'e'])
obj2
a   -5.3
b    7.2
c    3.6
d    4.5
e    NaN
dtype: float64

# Index 순서로 정렬 & 기존에 없는 인덱스가 추가된 경우에 value값으로 NaN 할당

obj.reindex(['a', 'b', 'c', 'd', 'e'], fill_value=0)
obj
a   -5.3
b    7.2
c    3.6
d    4.5
e    0.0
dtype: float64

# fill_value 옵션: NaN 값이 있다면 그 값을 fill_value에서 지정한 값으로 채움

obj3 = pd.Series(['blue', 'purple', 'yellow'], index=[0, 2, 4])
obj3.reindex(range(6), method='ffill')
=>
0 blue 
1 blue 
2 purple 
3 purple 
4 yellow 
5 yellow

# method='ffill' 옵션 : NaN 값이 있으면 직전 값으로 채움 <-> method = 'bfill'
```

- Dropping entries from an axis

```
obj = pd.Series(np.arange(5), index=['a', 'b', 'c', 'd', 'e'])
new_obj = obj.drop('c')
new_obj
=>
a 0 
b 1 
d 3 
e 4

# dataframe.drop(x, axis=) axis=0이면 지정한 행 데이터 전체를 제외한 
dataframe을 반환하고, axis=1이면 지정한 열 데이터 전체를 제외한 dataframe 반환

data = pd.DataFrame(np.arange(16).reshape((4, 4)),
....: index=['Ohio', 'Colorado', 'Utah', 'New York'], 
....: columns=['one', 'two', 'three', 'four'])

data.drop('two', axis=1)
=>
		one three four
Ohio  		0     2    3
Colorado 	4     6	   7
Utah		8     10   11
New York	12    14   15
```

- Arithmetic and data alignment : dataframe.add / sub / div / mul

```
df1 = DataFrame(np.arange(9.).reshape((3, 3)), columns=list('bcd'), 
index=['Ohio', 'Texas', 'Colorado'])
df2 = DataFrame(np.arange(12.).reshape((4, 3)), columns=list('bde'), 
index=['Utah', 'Ohio', 'Texas', 'Oregon'])
df1 + df2 / df1.add(df2)
=>
			b c d e 
Colorado NaN NaN NaN NaN 
Ohio 		3 NaN 6 NaN 
Oregon 	 NaN NaN NaN NaN 
Texas 		9 NaN 12 NaN 
Utah 	 NaN NaN NaN NaN

df1 = pd.DataFrame(np.arange(12.).reshape((3, 4)), columns=list('abcd'))
df2 = pd.DataFrame(np.arange(20.).reshape((4, 5)), columns=list('abcde'))
df1.add(df2, fill_value = 0)
=> 
  a b c d e 
0 0 2 4 6 4 
1 9 11 13 15 9 
2 18 20 22 24 14
3 15 16 17 18 19

# 0으로 채운 다음 더하기 (fill_value = 0)
```

- Function application and mapping : apply / applymap / map

```
frame = pd.DataFrame(np.random.randn(4, 3), columns=list('bde'), 
index=['Utah', 'Ohio', 'Texas', 'Oregon'])

f = lambda x: x.max() - x.min() # lambda 변수: 조건식
frame.apply(f)
=>
b 1.802165 
d 1.684034 
e 2.689627

# dataframe.apply(함수) : 함수를 적용할 columns가 여러개일 때 dataframe에 사용 (열마다 함수 적용)

format = lambda x: '%.2f' % x
frame.applymap(format)
=>
		b d e 
Utah -0.20 0.48 -0.52 
Ohio -0.56 1.97 1.39 
Texas 0.09 0.28 0.77 
Oregon 1.25 1.01 -1.30

# dataframe.applymap(함수) : 함수를 적용한 데이터프레임을 반환하되, 열마다가 아니라 요소마다 적용함

frame['e'].map(format)
=>
Utah -0.52 
Ohio 1.39 
Texas 0.77 
Oregon -1.30 
Name: e

# series.map(함수) : 함수를 적용한 series를 반환함. 반드시 series에만 사용
```

- Sorting and ranking : dataframe.sort_index() / dataframe.rank(axis=)
```
frame = pd.DataFrame({'b': [4, 7, -3, 2], 'a': [0, 1, 0, 1]})
frame.sort_index(by='b')
=>
	a       b
2       0      -3
3	1	2
0	0	4
1	1	7
frame.sort_index(by=['a', 'b'])
=>
	a  b 
2 	0  -3 
0	0  4 
3 	1  2 
1 	1  7

# 'a'를 기준으로 먼저 정렬하고 난 후, 'b'를 기준으로 정렬

frame.rank(axis=1)
	a       b
0`	2	3
1	1	3
2	2	1
3	2	3
```

- Summarizing and Computing Descriptive Statistics : 
dataframe.mean() / sum() / cumsum()/ min() / max() / cumprod() / argmin() / argmax() / idxmax() : 지정한 축마다 최댓값이 포함된 데이터 반환/ idxmin() / describe() : 기본통계량 ...


-  Handling Missing Data 
dropna(axis=, how=) : missing data가 있는 자료값 자체를 삭제. 기본값 : axis=0
how='all' 일 경우 축을 기준으로 그 축에 해당되는 자료값이 모두 missing data 인것만 drop
(thresh = n 옵션 : n개 이상의 missing data가 있는 자료만 삭제)
isnull(), notnull(): 각 요소마다 missing data인지 아닌지를 bool값으로 반환
fillna(x= 숫자 or 여러 missing data를 각각 지정할 경우 딕셔너리 or 함수, inplace=False <fillna는 기본값이 nan을 채운  새로운 객체를 반환하지만, inplace=True로 하면 원래의 dataframe을 변경함>, method='ffill' or 'bfill' , limit=n < n개만큼 앞의 자료 혹은 뒤의 자료로 채움>)


```
data['Qu1'] = np.nan

data.isnull()
=>
	Qu1 Qu2 Qu3
0  True False  False
1  True False  False
2  True False  False
3  True False  False
4  True False  False

data.dropna()
=>
Qu1 Qu2 Qu3

df = DataFrame(np.random.randn(7, 3))
df.ix[:4,1] = np.NaN; df.ix[:2, 2]=np.NaN;
df
=>
          0         1         2
0 -1.197912       NaN       NaN
1  0.476382       NaN       NaN
2 -0.019053       NaN       NaN
3 -0.480427       NaN -0.089518
4 -0.148159       NaN  0.931992
5  0.039886  0.627198  0.099928
6 -0.961399  0.097585  1.104502

df.dropna(thresh=3)
=>
          0         1         2
5  0.039886  0.627198  0.099928
6 -0.961399  0.097585  1.104502

df.fillna({1: 0.5, 2:-1})
=>
          0         1         2
0 -1.197912  0.500000 -1.000000
1  0.476382  0.500000 -1.000000
2 -0.019053  0.500000 -1.000000
3 -0.480427  0.500000 -0.089518
4 -0.148159  0.500000  0.931992
5  0.039886  0.627198  0.099928
6 -0.961399  0.097585  1.104502
```

- Hierarchical Indexing (Multi Index)

```
data = Series(np.random.randn(10), 
index=[['a', 'a', 'a', 'b', 'b', 'b', 'c', 'c', 'd', 'd'], 
[1, 2, 3, 1, 2, 3, 1, 2, 2, 3]])
data
=>
a 1 0.670216 
  2 0.852965 
  3 -0.955869 
b 1 -0.023493 
  2 -2.304234 
  3 -0.652469 
c 1 -1.218302 
  2 -1.332610 
d 2 1.074623 
  3 0.723642

data.index
=>
MultiIndex [('a', 1) ('a', 2) ('a', 3) ('b', 1) ('b', 2) ('b', 3) ('c', 1) ('c', 2) ('d', 2) ('d', 3)]

data['b']
=>
1 -0.023493 
2 -2.304234 
3 -0.652469

data.unstack()
=>
	1 2 3 
a 0.670216 0.852965 -0.955869 
b -0.023493 -2.304234 -0.652469 
c -1.218302 -1.332610 NaN 
d NaN 1.074623 0.723642

# unstack() : 쌓은 자료를 옆으로 확장하기
# stack() : 옆으로 확장된 자료를 쌓기

frame = pd.DataFrame(np.arange(12).reshape((4, 3)), .....: index=[['a', 'a', 'b', 'b'], [1, 2, 1, 2]], .....: columns=[['Ohio', 'Ohio', 'Colorado'], .....: ['Green', 'Red', 'Green']])
frame
=>
	Ohio 	Colorado 
	Green Red Green 
a  1 	0 	1    2 
   2 	3 	4 	 5 
b  1 	6 	7 	 8 
   2 	9 	10 	 11
   
frame.index.names = ['key1', 'key2']
frame.columns.names = ['state', 'color']
frame
=>
state	Ohio 	Colorado 
color	Green Red Green 
key1 key2
a   1 	0 	1    2 
    2 	3 	4    5 
b   1 	6 	7    8 
    2 	9 	10   11
```

- Reordering and Sorting Levels 
swaplevel(int[인덱스]) : level 바꾸기
sortlevel(int[인덱스]) : level을 기준으로 자료 sorting 
```
frame.swaplevel('key1', 'key2')
=>
state	Ohio 	Colorado 
color	Green Red Green 
key2 key1
a  	1 	0 	1    2 
	2 	3 	4 	 5 
b   1 	6 	7 	 8 
    2 	9 	10 	 11

frame.sortlevel(1)
state	Ohio 	Colorado 
color	Green Red Green 
key1 key2
a  	1 	0 	1    2 
	1 	6 	7 	 8 
b   2 	3 	4 	 5 
    2 	9 	10 	 11
```

- Summary Statistics by Level
```
frame.sum(level='key2')
state Ohio Colorado 
color Green Red Green 
key2 
1 	6 	8 	10 
2 	12 	14 	16

frame.sum(level='color', axis=1)
color Green Red 
key1 key2 
a 	1 	2 1 
	2 	8 4 
b 	1 	14 7 
	2 	20 10
```

- Using a DataFrame’s Columns
set_index( n ) : n을 dataframe의 인덱스로 지정 옵션으로 drop=False를 주면 인덱스를 붙이되 자료를 그대로 둠
reset_index() : 원래의 인덱스 상태로 돌려놓기

```
frame = DataFrame({'a': range(7), 'b': range(7, 0, -1), .....: 'c': ['one', 'one', 'one', 'two', 'two', 'two', 'two'], .....: 'd': [0, 1, 2, 0, 1, 2, 3]})
frame
=>
  a b c d 
0 0 7 one 0 
1 1 6 one 1 
2 2 5 one 2 
3 3 4 two 0 
4 4 3 two 1 
5 5 2 two 2 
6 6 1 two 3

frame2 = frame.set_index(['c', 'd'])
frame2
=>
	a b 
c   d 
one 0 	0 7 
    1 	1 6 
    2 	2 5 
two 0 	3 4 
    1 	4 3 
    2 	5 2 
    3 	6 1

frame.set_index(['c', 'd'], drop=False)
=>
	  a b c  d
c   d 
one 0 0 7 one 0
	1 1 6 one 1 
	2 2 5 one 2
two 0 3 4 two 0
	1 4 3 two 1
	2 5 2 two 2
	3 6 1 two 3
frame2.reset_index()
=>
  c d a b 
0 one 0 0 7 
1 one 1 1 6 
2 one 2 2 5 
3 two 0 3 4 
4 two 1 4 3 
5 two 2 5 2 
6 two 3 6 1
```

- Panel Data
> 여러 객체의 데이터를 복수의 시간에 따라 나열한 데이터

pdata.ix[행, 날짜, 열]
pdata.to_frame() / dataframe.to_panel()

```
pdata.ix['Adj Close', '5/22/2012':, :]
		AAPL DELL GOOG MSFT 
Date 
2012-05-22 556.97 15.08 600.80 29.76 
2012-05-23 570.56 12.49 609.46 29.11 
2012-05-24 565.32 12.45 603.66 29.07 
2012-05-25 562.29 12.46 591.53 29.06 
2012-05-29 572.27 12.66 594.34 29.56 
2012-05-30 579.17 12.56 588.23 29.34
```
