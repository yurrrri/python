# Data Wrangling: Clean, Transform, Merge, Reshape

## Combining and Merging Data Sets

### Database-style DataFrame Merges
- pandas.merge
```
import pandas as pd

df1 = pd.DataFrame({'key': ['b', 'b', 'a', 'c', 'a', 'a', 'b'], 'data1': range(7)})
df2 = pd.DataFrame({'key': ['a', 'b', 'd'],'data2': range(3)})
df1 df2
=>
	data1 key 	 	data2 key 
0 		0 	b  	0 		0 	a 
1 		1 	b 	1 		1 	b 
2	 	2 	a 	2 		2 	d 
3	 	3 	c 	4 		4 	a 
5 		5 	a 	6 		6 	b
pd.merge(df1, df2) = pd.merge(df1, df2, on='key') 
#key를 기준으로 행 병합
/ 기본값 : 두 데이터 모두 존재하는 key에 관해 병합하는 inner join
=>
  
	data1	  key	data2
0		0	    b		1
1		1		b		1
2		6		b		1
3		2		a		0
4		4		a		0
5		5		a		0

df3 = pd.DataFrame({'lkey': ['b', 'b', 'a', 'c', 'a', 'a', 'b'], 'data1': range(7)})
df4 = pd.DataFrame({'rkey': ['a', 'b', 'd'],'data2': range(3)})
df3
=>
	data1	lkey
0		0		b
1		1		b
2		2		a
3		3		c
4		4		a
5		5		a
6		6		b

df4
=>
	data2	rkey	
0		0		a		
1		1		b		
2		2		d

pd.merge(df3, df4, left_on='lkey', right_on='rkey')
#key가 되는 이름이 서로 다르다면, left_on과 right_on으로 명시할
필요성이 있다.
=>
	data1	lkey	data2	rkey
0		0		b		1		b
1		1		b		1		b
2		6		b		1		b
3		2		a		0		a
4		4		a		0		a
5		5		a		0		a

pd.merge(df1, df2, how='outer')
#outer join : 둘 중 하나에만 있는 데이터도 같이 병합함
=>
	data1		key		data2
0		0.0		b		  1.0
1		1.0		b		  1.0
2		6.0		b		  1.0
3		2.0		a		  0.0
4		4.0		a		  0.0
5		5.0		a		  0.0
6		3.0		c		  NaN
7		NaN		d		  2.0

pd.merge(df1, df2, on='key', how='left')
#left join : 왼쪽의 데이터에만 있는 key를 기준으로 병합하며, 만약에 왼쪽의 key의 데이터가 오른쪽에 없다면 missing value로 처리함
  
	data1		key		data2
0		0		b		1.0
1		0		b		3.0
2		1		b		1.0
3		1		b		3.0
4		2		a		0.0
5		2		a		2.0
6		3		c		NaN
7		4		a		0.0
8		4		a		2.0
9		5		b		1.0
10		5		b		3.0

left = pd.DataFrame({'key1': ['foo', 'foo', 'bar'],
 ....: 'key2': ['one', 'two', 'one'],
 ....: 'lval': [1, 2, 3]})
left
=>
	key1	key2	lval
0	foo		one			1
1	foo		two			2
2	bar		one			3

right = pd.DataFrame({'key1': ['foo', 'foo', 'bar', 'bar'],
 ....: 'key2': ['one', 'one', 'one', 'two'],
 ....: 'rval': [4, 5, 6, 7]})
right
=>
	key1	key2	rval
0	foo		one			4
1	foo		one			5
2	bar		one			6
3	bar		two			7

pd.merge(left, right, on=['key1', 'key2'], how='outer')
=>
	key1	key2	lval	rval
0	foo		one		1.0		4.0
1	foo		one		1.0		5.0
2	foo		two		2.0		NaN
3	bar		one		3.0		6.0
4	bar		two		NaN		7.0
```

### Merging on Index
- pd.merge(left_index= / right_index= 옵션)
- join
```
 left1 = pd.DataFrame({'key': ['a', 'b', 'a', 'a', 'b', 'c'],
 ....: 'value': range(6)})
right1 = pd.DataFrame({'group_val': [3.5, 7]}, index=['a', 'b'])
left1
=>
	key	value
0	a		0
1	b		1
2	a		2
3	a		3
4	b		4
5	c		5

right1
=>
	group_val
a	3.5
b	7.0

pd.merge(left1, right1, left_on='key', right_index=True)
# 오른쪽 데이터의 행(인덱스)을 기준으로, 두 데이터 둘다 존재하는 key
의 데이터 만을 가져옴

	key		value	group_val
0	a		0		3.5
2	a		2		3.5
3	a		3		3.5
1	b		1		7.0
4	b		4		7.0

pd.merge(left1, right1, left_on='key', right_index=True, how='outer')
# 오른쪽 데이터의 행(인덱스)를 기준으로 하되, 두 데이터 둘다 존재하지 않는 데이터도 다 가져옴. (how='outer')
  
	key		value	group_val
0	a		0			3.5
2	a		2			3.5
3	a		3			3.5
1	b		1			7.0
4	b		4			7.0
5	c		5			NaN

import numpy as np
lefth = pd.DataFrame({'key1': ['Ohio', 'Ohio', 'Ohio', 'Nevada', 'Nevada'],
'key2': [2000, 2001, 2002, 2001, 2002],
'data': np.arange(5.)})

righth = pd.DataFrame(np.arange(12).reshape((6, 2)),
 index=[['Nevada', 'Nevada', 'Ohio', 'Ohio', 'Ohio', 'Ohio'],
 [2001, 2000, 2000, 2000, 2001, 2002]],
 columns=['event1', 'event2'])
pd.merge(lefth, righth, left_on=['key1', 'key2'], right_index=True)
=>
	data	key1	key2	event1	event2
0	0.0		Ohio	2000		4		5
0	0.0		Ohio	2000		6		7
1	1.0		Ohio	2001		8		9
2	2.0		Ohio	2002		10		11
3	3.0		Nevada	2001		0		1

left2 = pd.DataFrame([[1., 2.], [3., 4.], [5., 6.]], index=['a', 'c', 'e'],
 ....: columns=['Ohio', 'Nevada'])
right2 = pd.DataFrame([[7., 8.], [9., 10.], [11., 12.], [13, 14]],
 ....: index=['b', 'c', 'd', 'e'], columns=['Missouri', 'Alabama'])
left2
=>
	Ohio	Nevada
a	1.0		2.0
c	3.0		4.0
e	5.0		6.0

right2
=>
	Missouri	Alabama
b		7.0			8.0
c		9.0			10.0
d		11.0		12.0
e		13.0		14.0

pd.merge(left2, right2, how='outer', left_index=True, right_index=True) = left2.join(right2, how='outer')
# 왼쪽 데이터의 index와 오른쪽 데이터의 index를 모두 합치기

	Ohio	Nevada		Missouri	Alabama
a	1.0		2.0				NaN			NaN
b	NaN		NaN				7.0			8.0
c	3.0		4.0				9.0			10.0
d	NaN		NaN				11.0		12.0
e	5.0		6.0				13.0		14.0

another = pd.DataFrame([[7., 8.], [9., 10.], [11., 12.], [16., 17.]],
index=['a', 'c', 'e', 'f'], columns=['New York', 'Oregon'])
left2.join([right2, another])
  
	Ohio	Nevada	Missouri	Alabama		NewYork		Oregon
a	1.0		2.0		NaN			NaN			7.0			8.0
c	3.0		4.0		9.0			10.0		9.0			10.0
e	5.0		6.0		13.0		14.0		11.0		12.0
```

### Concatenating Along an Axis
- np.concatenate #사슬처럼 묶기
- pd.concat(how='outer', keys= : 사슬로 연결된 값들에 이름 붙이기,
 names= : key의 이름)
```
arr = np.arange(12).reshape((3, 4))
arr
=>
array([[ 0, 1, 2, 3], [ 4, 5, 6, 7], [ 8, 9, 10, 11]])

np.concatenate([arr, arr], axis=1)
=>
array( [[ 0, 1, 2, 3, 0, 1, 2, 3], 
		[ 4, 5, 6, 7, 4, 5, 6, 7], 
		[ 8, 9, 10, 11, 8, 9, 10, 11]])

s1 = Series([0, 1], index=['a', 'b'])
s2 = Series([2, 3, 4], index=['c', 'd', 'e'])
s3 = Series([5, 6], index=['f', 'g'])

pd.concat([s1, s2, s3])
=>
a 0 
b 1 
c 2 
d 3 
e 4 
f 5 
g 6
pd.concat([s1, s2, s3], axis=1)
=>
	0 	1 	2 
a 	0 NaN NaN 
b 	1 NaN NaN 
c 	NaN 2 NaN 
d 	NaN 3 NaN 
e 	NaN 4 NaN 
f 	NaN NaN 5 
g 	NaN NaN 6

s4 = pd.concat([s1 * 5, s3])
s4
=>
a    0
b    5
f    5
g    6
dtype: int64

pd.concat([s1, s4], axis=1)
=>
	0	1
a	0.0 0
b	1.0	5
f	NaN	5
g	NaN	6

pd.concat([s1, s4], axis=1, join='inner')
=>
	0	1
a	0	0
b	1	5

pd.concat([s1, s4], axis=1, join_axes=[['a', 'c', 'b', 'e']])
#join 하는데 필요한 축 지정
=>
	0 1 
a 	0 0 
c 	NaN NaN 
b 	1 5 
e 	NaN NaN

result = pd.concat([s1, s1, s3], keys=['one', 'two', 'three'])
# concat된 데이터에 key 첨부 (key=인덱스)
# 옵션을 axis=1로 주면 key가 열이 됨
result
=>
one 	a 0 
		b 1 
two 	a 0 
		b 1 
three 	f 5 
		g 6

pd.concat([s1, s2, s3], axis=1, keys=['one', 'two', 'three'])
=>
	one two three 
a 	0 NaN NaN 
b 	1 NaN NaN 
c 	NaN 2 NaN 
d 	NaN 3 NaN 
e 	NaN 4 NaN 
f 	NaN NaN 5 
g 	NaN NaN 6

pd.concat({'level1': df1, 'level2': df2}, axis=1)
# 딕셔너리로 데이터에 key 붙이기 가능
	level1 		level2 
	one two 	three four 
a 	0 	1 		5 		6 
b 	2 	3 		NaN 	NaN 
c 	4 	5		7 		8

df1 = DataFrame(np.arange(6).reshape(3, 2), index=['a', 'b', 'c'], ....: columns=['one', 'two']) 
df2 = DataFrame(5 + np.arange(4).reshape(2, 2), index=['a', 'c'], ....: columns=['three', 'four'])
pd.concat([df1, df2], axis=1, keys=['level1', 'level2']) 
=>
	level1 		level2 
		one two three four 
a 		0 	1 	5 	6 
b 		2 	3 NaN NaN 
c 		4 	5 	7 	8

pd.concat([df1, df2], axis=1, keys=['level1', 'level2'], ....: names=['upper', 'lower'])
# names= 옵션 : key에 이름 붙이기
upper level1 		level2 
lower 	one two 	three four 
a 		0 	1 		5 		6 
b 		2 	3 		NaN 	NaN 
c 		4 	5 		7 		8
```
### Combining Data with Overlap
- (데이터1).combine_first(데이터) => join이나 merge 개념이 아닌 overlap
- series와 dataframe 둘다 사용 가능
```
a = Series([np.nan, 2.5, np.nan, 3.5, 4.5, np.nan], ....: index=['f', 'e', 'd', 'c', 'b', 'a'])
b = Series(np.arange(len(a), dtype=np.float64), ....: index=['f', 'e', 'd', 'c', 'b', 'a'])
b[-1] = np.nan

a
=>
f    NaN
e    2.5
d    NaN
c    3.5
b    4.5
a    NaN
dtype: float64

b
=>
f    0.0
e    1.0
d    2.0
c    3.0
b    4.0
a    NaN
dtype: float64

b[:-2]
=>
f    0.0
e    1.0
d    2.0
c    3.0
dtype: float64
b[:-2].combine_first(a[2:]) 
#둘이 결합하는데 인덱스가 겹치는 데이터는 왼쪽 데이터로 overlap함
missing value는 둘 중 하나 있는 데이터로 채움
=>
a NaN 
b 4.5 
c 3.0 
d 2.0 
e 1.0 
f 0.0


df1 = pd.DataFrame({'a': [1., np.nan, 5., np.nan],
....: 'b': [np.nan, 2., np.nan, 6.],
....: 'c': range(2, 18, 4)})
df1
=>
	a	b	c
0	1.0 NaN	2
1	NaN	2.0	6
2	5.0	NaN	10	
3	NaN	6.0	14
df2 = pd.DataFrame({'a': [5., 4., np.nan, 3., 7.],
 ....: 'b': [np.nan, 3., 4., 6., 8.]})
df2
=>
	a	b
0	5.0	NaN
1	4.0	3.0
2	NaN	4.0
3	3.0	6.0
4	7.0	8.0

df1.combine_first(df2)
=>
	a	b	c
0	1.0	NaN	2.0
1	4.0	2.0	6.0
2	5.0	4.0	10.0
3	3.0	6.0	14.0
4	7.0	8.0	NaN

```

## Reshaping and Pivoting
### Reshaping with Hierarchical Indexing 
- stack(dropna=True)
```
data = pd.DataFrame(np.arange(6).reshape((2, 3)), ....: index=pd.Index(['Ohio', 'Colorado'], name='state'), ....: columns=pd.Index(['one', 'two', 'three'], name='number'))
data
=>
number one two three
state 
Ohio 	0 	1 	2 
Colorado 3 	4 	5

result = data.stack() <-> data.unstack()
result
=>
state number
Ohio 	one 	0 
		two 	1 
		three 	2 
Colorado one 	3 
		 two 	4 
		 three 	5

result.unstack(0) #axis=0을 기준으로 펼치기 = real.unstack('state') #인덱스(Ohio, Colorado) 기준으로 펼치기
=>
state	Ohio	Colorado
number
one		  0			 3
two		  1			 4
three	  2		     5

s1 = pd.Series([0, 1, 2, 3], index=['a', 'b', 'c', 'd'])
s2 = pd.Series([4, 5, 6], index=['c', 'd', 'e'])
s1
=>
a    0
b    1
c    2
d    3
dtype: int64

s2
=>
c    4
d    5
e    6
dtype: int64

data2 = pd.concat([s1, s2], keys=['one', 'two'])
data2
=>
one  a    0
     b    1
     c    2
     d    3
two  c    4
     d    5
     e    6
dtype: int64

data2.unstack()
=>
	a b c d e 
one 0 1 2 3 NaN 
two NaN NaN 4 5 6
data2.unstack().stack()
=>
one  a    0.0
     b    1.0
     c    2.0
     d    3.0
two  c    4.0
     d    5.0
     e    6.0
dtype: float64

df = DataFrame({'left': result, 'right': result + 5}, .....: columns=pd.Index(['left', 'right'], name='side'))
df
=>
side 				left right 
state 	number    
Ohio  	one 		0 		5 
		two 		1 		6 
		three 		2 		7 
Colorado one 		3 		8 
		 two 		4 		9 
		 three 		5 		10
df.unstack('state').stack('side') #state를 기준으로 펼치고, side를 기준으로 쌓아라
=>  
state			Ohio	Colorado
number	side
one		left	0			3
		right	5			8
two		left	1			4
		right	6			9
three	left	2			5
		right	7			10
```
### Pivoting “long” to “wide” Format
> 1. 대화형 **테이블**의 일종으로, 데이터의 나열 형태에 따라서 집계나 카운트 등의 계산을 하는 테이블
> 2. 데이터 열 중에서 두 개를 키(key)로 사용하여 데이터를 선택하는 방법

```
ldata[:10] 
		date 	item 	value
0 1959-03-31 00:00:00 realgdp 2710.349 
1 1959-03-31 00:00:00 infl 0.000 
2 1959-03-31 00:00:00 unemp 5.800 
3 1959-06-30 00:00:00 realgdp 2778.801 
4 1959-06-30 00:00:00 infl 2.340 
5 1959-06-30 00:00:00 unemp 5.100 
6 1959-09-30 00:00:00 realgdp 2775.488 
7 1959-09-30 00:00:00 infl 2.740 
8 1959-09-30 00:00:00 unemp 5.300 
9 1959-12-31 00:00:00 realgdp 2785.204


pivoted = ldata.pivot('date', 'item', 'value')
pivoted.head()
item 		infl 	realgdp unemp
date 
1959-03-31 0.00 	2710.349 5.8 
1959-06-30 2.34 	2778.801 5.1 
1959-09-30 2.74 	2775.488 5.3 
1959-12-31 0.27 	2785.204 5.6 
1960-03-31 2.31 	2847.699 5.2

ldata['value2'] = np.random.randn(len(ldata)) #value2라는 열을 추가로 만들고, 데이터 추가
				date 	 item 	 value 	 value2 
0 1959-03-31 00:00:00 realgdp 2710.349 1.669025 
1 1959-03-31 00:00:00 infl 0.000 -0.438570 
2 1959-03-31 00:00:00 unemp 5.800 -0.539741 
3 1959-06-30 00:00:00 realgdp 2778.801 0.476985 
4 1959-06-30 00:00:00 infl 2.340 3.248944 
5 1959-06-30 00:00:00 unemp 5.100 -1.021228 
6 1959-09-30 00:00:00 realgdp 2775.488 -0.577087 
7 1959-09-30 00:00:00 infl 2.740 0.124121 
8 1959-09-30 00:00:00 unemp 5.300 0.302614 
9 1959-12-31 00:00:00 realgdp 2785.204 0.523772

pivoted = ldata.pivot('date', 'item') 
<-> unstacked = ldata.set_index(['date','item']).unstack('item')
pivoted[:5] # 
# set_index:기존의 행 인덱스를 제거하고 데이터 열 중 하나를 인덱스로 설정
=>
			value 				value2 
item 		infl realgdp unemp 	infl 	realgdp 	unemp 
date
1959-03-31 	0.00 2710.349 5.8 -0.438570 1.669025 -0.539741 1959-06-30 	2.34 2778.801 5.1 3.248944 0.476985 -1.021228 
1959-09-30 	2.74 2775.488 5.3 0.124121 -0.577087 0.302614 
1959-12-31 	0.27 2785.204 5.6 0.000940 0.523772 1.343810 
1960-03-31 	2.31 2847.699 5.2 -0.831154 -0.713544 -2.370232

```

## Data Transformation
### Removing Duplicates

- duplicated (): 데이터의 중복 여부를 요소마다 boolean 값으로 반환
- drop_duplicates() : duplicated값이 True인 값 제거한 data 반환
(중복된 값을 반환하고, 유일한 data만 반환함!
기본값은 전체 열을 다 따져서 중복된 요소가 하나라도 있으면
그 중복된 요소를 제거하지만, 특정 열을 지정해서 기준을
삼을 수도 있음.)

```
data = DataFrame({'k1': ['one'] * 3 + ['two'] * 4, .....: 'k2': [1, 1, 2, 3, 3, 4, 4]})
data
=>
	k1 k2 
0 	one 1 
1 	one 1 
2	one 2 
3 	two 3 
4 	two 3 
5 	two 4 
6 	two 4

data.duplicated()
=>
0    False
1     True
2    False
3    False
4     True
5    False
6     True
dtype: bool

data.drop_duplicates()
	k1 k2 
0 	one 1 
2 	one 2 
3 	two 3 
5 	two 4

data['v1']=range(7)
data
=>
	k1	k2	v1	
0	one	1	0
1	one	1	1
2	one	2	2
3	two	3	3
4	two	3	4
5	two	4	5
6	two	4	6

data.drop_duplicates(['k1', 'k2'], keep='last')
# 중복되는 값들 중 맨 마지막 데이터를 반환함
=>
	k1 k2 v1 
1 	one 1 1 
2 	one 2 2 
4 	two 3 4 
6 	two 4 6
```

### Transforming Data Using a Function or Mapping
- map(함수, itereable 자료형)

```
 data = pd.DataFrame({'food': ['bacon', 'pulled pork', 'bacon', 'Pastrami',
 .....: 'corned beef', 'Bacon', 'pastrami', 'honey ham',
 .....: 'nova lox'],
 .....: 'ounces': [4, 3, 12, 6, 7.5, 8, 3, 5, 6]})
data
=>
  
	food		ounces
0	bacon		4.0
1	pulled pork	3.0
2	bacon		12.0
3	Pastrami	6.0
4	corned beef	7.5
5	Bacon		8.0
6	pastrami	3.0
7	honey ham	5.0
8	nova lox	6.0

meat_to_animal = { 'bacon': 'pig', 'pulled pork': 'pig', 'pastrami': 'cow', 'corned beef': 'cow', 'honey ham': 'pig', 'nova lox': 'salmon' }
data['animal'] = data['food'].map(str.lower).map(meat_to_animal)
# meat_to_animal의 자룟값이 다 소문자이기 때문에 matching 시켜주려면
데이터를 그 전에 소문자로 바꿔줘야할 필요가 있음.
# 소문자로 바뀐 데이터에 meat_to_animal 자료 적용

data
=>
	food 		ounces 	animal 
0 	bacon 		4.0 	pig 
1 	pulled pork 3.0 	pig 
2 	bacon 		12.0 	pig
3 	Pastrami 	6.0 	cow 
4 	corned beef 7.5 	cow 
5 	Bacon 		8.0 	pig
6 	pastrami 	3.0 	cow 
7 	honey ham 	5.0 	pig 
8 	nova lox 	6.0 	salmon

data['food'].map(lambda x: meat_to_animal[x.lower()])
# lambda함수 => lambda 변수: 조건문

0 	pig 
1 	pig 
2 	pig 
3 	cow 
4 	cow 
5 	pig 
6 	cow 
7 	pig 
8 	salmon 
Name: food
```

### Replacing Values
- replace(원래값, 바꾸고 싶은 값)
```
data = pd.Series([1., -999., 2., -999., -1000., 3.])
data
=>
0       1.0
1    -999.0
2       2.0
3    -999.0
4   -1000.0
5       3.0
dtype: float64

data.replace([-999, -1000], [np.nan, 0])
=>
0 1 
1 NaN 
2 2 
3 NaN 
4 0 
5 3
```

### Renaming Axis Indexes
rename : 원래 자료를 변경하지 않고 반환하고 싶을 때
```
data = DataFrame(np.arange(12).reshape((3, 4)), .....: index=['Ohio', 'Colorado', 'New York'], .....: columns=['one', 'two', 'three', 'four'])

data.index = data.index.map(str.upper) #원 자료가 변경
data
=>
		one two three four 
OHIO 	 0 	 1 		2	 3 
COLORADO 4 	 5 		6 	 7 
NEW YORK 8 	 9 		10 	 11

data.rename(index=str.title, columns=str.upper)
# str.title : 첫 글자를 대문자, 나머지를 소문자로
=>
		ONE TWO THREE FOUR 
Ohio 	0 	1 	2 		3 
Colorado 4 	5 	6 		7 
New York 8 	9 	10 		11
```

### Discretization and Binning
> 연속형 자료의 이산화
> 연속형 자료를 범주화
- cut(자료, 기준, right=True # 범위의 끝을 포함시킴, labels= #label 지정, precision = 3)
- qcut(자료, 기준) : 범위 지정이 아닌, 기준에 맞추어 데이터의 개수를 똑같이 해서 나눔
```
ages = [20, 22, 25, 27, 21, 23, 37, 31, 61, 45, 41, 32]
bins = [18, 25, 35, 60, 100]
cats = pd.cut(ages, bins)
cats
=>
Categorical: #범주형 데이터
array([(18, 25], (18, 25], (18, 25], (25, 35], (18, 25], (18, 25], (35, 60], (25, 35], (60, 100], (35, 60], (35, 60], (25, 35]], dtype=object) 
Levels (4): Index([(18, 25], (25, 35], (35, 60], (60, 100]], dtype=object)

cats.labels
=>
array([0, 0, 0, 1, 0, 0, 2, 1, 3, 2, 2, 1])

cats.levels
=>
Index([(18, 25], (25, 35], (35, 60], (60, 100]], dtype=object)

pd.value_counts(cats)
=>
(18, 25] 5 
(35, 60] 3 
(25, 35] 3 
(60, 100] 1

pd.cut(ages, [18, 26, 36, 61, 100], right=False)
=>
Categorical: array([[18, 26), [18, 26), [18, 26), [26, 36), [18, 26), [18, 26), [36, 61), [26, 36), [61, 100), [36, 61), [36, 61), [26, 36)], dtype=object) Levels (4): Index([[18, 26), [26, 36), [36, 61), [61, 100)], dtype=object)

data = np.random.rand(20) #[0,1) 범위의 20개의 난수 반환
data
=>
array([0.80102539, 0.39528776, 0.64419638, 0.0908898 , 0.82251329,
       0.16337372, 0.01199713, 0.04979789, 0.09616964, 0.54762865,
       0.32177502, 0.35599801, 0.29859701, 0.74459639, 0.15758546,
       0.21404251, 0.89120453, 0.7167986 , 0.58680595, 0.98716981])

pd.cut(data, 4, precision=2) #4분위로 자르고, 소수점은 둘째자리까지
[(0.74, 0.99], (0.26, 0.5], (0.5, 0.74], (0.011, 0.26], (0.74, 0.99], ..., (0.011, 0.26], (0.74, 0.99], (0.5, 0.74], (0.5, 0.74], (0.74, 0.99]]
Length: 20
Categories (4, object): [(0.011, 0.26] < (0.26, 0.5] < (0.5, 0.74] < (0.74, 0.99]]

data = np.random.randn(1000)
cats = pd.qcut(data, 4)
cats
=>
[(-0.653, 0.0448], (-0.653, 0.0448], (0.0448, 0.723], [-3.386, -0.653], [-3.386, -0.653], ..., [-3.386, -0.653], (0.0448, 0.723], (0.723, 3.38], (0.0448, 0.723], [-3.386, -0.653]]
Length: 1000
Categories (4, object): [[-3.386, -0.653] < (-0.653, 0.0448] < (0.0448, 0.723] < (0.723, 3.38]]
```

### Detecting and Filtering Outliers

```
np.random.seed(12345) # 난수의 생성자. 똑같은 난수를 계속 생성하고 싶으면 seed의 인자를 계속 똑같이 주면 됨

data = DataFrame(np.random.randn(1000, 4))
data.describe()
				0 			1 			2 			3 
count 1000.000000 1000.000000 1000.000000 1000.000000 
mean -0.067684 0.067924 0.025598 -0.002298 
std 0.998035 0.992106 1.006835 0.996794 
min -3.428254 -3.548824 -3.184377 -3.745356 
25% -0.774890 -0.591841 -0.641675 -0.644144 
50% -0.116401 0.101143 0.002073 -0.013611 
75% 0.616366 0.780282 0.680391 0.654328 
max 3.366626 2.653656 3.260383 3.927528

data[(np.abs(data) > 3).any(1)] #절댓값이 3보다 큰 데이터가 단 하나라도 있는 데이터 반환
			0 		1 		2 		3 	
5 -0.539741 0.476985 3.248944 -1.021228 
97 -0.774363 0.552936 0.106061 3.927528 
102 -0.655054 -0.565230 3.176873 0.959533 
305 -2.315555 0.457246 -0.025907 -3.399312 
324 0.050188 1.951312 3.260383 0.963301 
400 0.146326 0.508391 -0.196713 -3.745356 
499 -0.293333 -0.242459 -3.056990 1.918403 
523 -3.428254 -0.296336 -0.439938 -0.867165 
586 0.275144 1.179227 -3.184377 1.369891 
808 -0.362528 -3.548824 1.553205 -2.186301 
900 3.366626 -2.372214 0.851010 1.332846
```

### Permutation (순열) and Random Sampling
- numpy.random.permutation(length of the axis)
```
sampler = np.random.permutation(5)
sampler
=>
array([3, 2, 4, 0, 1])

df = pd.DataFrame(np.arange(5 * 4).reshape(5, 4))
df
=>
	0	1	2	3
0	0	1	2	3
1	4	5	6	7
2	8	9	10	11
3	12	13	14	15
4	16	17	18	19

df.take(sampler)
# 인덱스가 3, 2, 4, 0, 1인 데이터 차례로 뽑아오기!

	0	1	2	3
3	12	13	14	15	
2	8	9	10	11
4	16	17	18	19
0	0	1	2	3
1	4	5	6	7

bag = np.array([5, 7, -1, 6, 4])
sampler = np.random.randint(0, len(bag), size=10)
sampler
=>
array([4, 4, 2, 2, 2, 0, 3, 0, 4, 1])

draws = bag.take(sampler)
draws
=>
array([ 4, 4, -1, -1, -1, 5, 6, 5, 4, 7])
```

### Computing Indicator/Dummy Variables
- get_dummies(자료) # 자료의 유무를 0, 1로 반환

```
df = pd.DataFrame({'key': ['b', 'b', 'a', 'c', 'a', 'b'], .....: 'data1': range(6)})
pd.get_dummies(df['key'])

	a b c 
0 	0 1 0 
1 	0 1 0 
2 	1 0 0 
3 	0 0 1 
4 	1 0 0 
5 	0 1 0

dummies = pd.get_dummies(df['key'], prefix='key')
df_with_dummy = df[['data1']].join(dummies)
df_with_dummy
=>
	data1 	key_a 	key_b 	key_c 
0 	0 		0 		1 		0
1 	1 		0 		1 		0
2 	2 		1 		0 		0 
3	3 		0 		0 		1 
4 	4 		1 		0 		0 
5 	5 		0 		1 		0

mnames = ['movie_id', 'title', 'genres']
movies = pd.read_table('ch02\movielens\movies.dat', sep='::', header=None,
 .....: names=mnames)
movies[:10]
=>
	movie_id 	title 			genres
0 			1 Toy Story (1995) Animation|Children's|Comedy
1 			2 Jumanji (1995) Adventure|Children's|Fantasy 
2 			3 Grumpier Old Men (1995) Comedy|Romance 
3 			4 Waiting to Exhale (1995) Comedy|Drama 
4 			5 Father of the Bride Part II (1995) Comedy 
5 			6 Heat (1995) Action|Crime|Thriller 
6 			7 Sabrina (1995) Comedy|Romance 
7 			8 Tom and Huck (1995) Adventure|Children's 
8 			9 Sudden Death (1995) Action 
9 			10 GoldenEye (1995) Action|Adventure|Thriller

dummies = pd.DataFrame(np.zeros((len(movies), len(movies.genres))), columns=movies.genres)
for i, gen in enumerate(movies.genres):
    dummies.ix[i, gen.split('|')] = 1

movies_windic = movies.join(dummies.add_prefix('Genre_'))
movies_windic.ix[0]
movie_id 						   1 
title 				Toy Story (1995) 
genres 		Animation|Children's|Comedy 
Genre_Action 						0
Genre_Adventure 					0 
Genre_Animation 					1 
Genre_Children's 					1 
Genre_Comedy 						1 
Genre_Crime 						0 
Genre_Documentary 					0 
Genre_Drama 						0 
Genre_Fantasy 						0 
Genre_Film-Noir 					0 
Genre_Horror 						0 
Genre_Musical 						0 
Genre_Mystery 						0 
Genre_Romance 						0 
Genre_Sci-Fi 						0 
Genre_Thriller 						0 
Genre_War 							0 
Genre_Western 						0
```

## String Manipulation
### String Object Methods
- strip() : 문자열의 공백 제거
- join() : 리스트를 문자열로
- index('찾고자 하는 문자') : 찾고자 하는 문자가 맨 첫번째로 등장하는 인덱스 return 찾고자하는 문자가 없으면 오류 발생!
- find() : 찾고자 하는 문자가 맨 첫번째로 등장하는 인덱스 return. 이 때 없을때 -1을 반환함
- count('찾고자하는 문자') : 찾고자 하는 문자의 갯수 반환

```
val = 'a,b, guido'
val.split(',')
=>
['a', 'b', ' guido']

pieces = [x.strip() for x in val.split(',')]
pieces
=>
['a', 'b', 'guido']

first, second, third = pieces
first + '::' + second + '::' + third
'a::b::guido'

'::'.join(pieces)
'a::b::guido'

val.index(',') # 맨 처음 ','가 나오는 인덱스를 return함.
=> 
1
val.index(':')
=>
--------------------------------------------------------------------------- ValueError Traceback (most recent call last) in () ----> 1 val.index(':') ValueError: substring not found

val.find(',')
=>
1

val.find(':')
=>
-1

val.count(',')
=>
2

val.replace(',', '::')
=>
'a::b:: guido'

```

### Regular expressions
- import re
- \t : tab, \s : 공백, \s+ : 연속적인 공백
- 컴파일객체.findall() : 지정한 정규표현식에 맞는 모든 문자열을 리스트로 반환
- 컴파일객체.search() : 문자열 전체를 검사하여 정규식과 매치되는지 검사
- 컴파일객체.match() : 문자열 처음부터 정규식과 매치되는지 검사
- 컴파일객체.sub(수정 뒤, 수정 전 텍스트 문자열 객체)
- re.compile(정규표현식, flags=) : 정규표현식을 매칭할 컴파일러 만들기
```
import re
text = "foo bar\t baz \tqux"
re.split('\s+', text)
=>
['foo', 'bar', 'baz', 'qux']

regex = re.compile('\s+') #정규 표현식을 매칭할 컴파일러 만들기
regex.split(text)
=>
['foo', 'bar', 'baz', 'qux']

regex.findall(text)
=>
[' ', '\t ', ' \t']

text = """Dave dave@google.com Steve steve@gmail.com Rob rob@gmail.com Ryan ryan@yahoo.com """ 
pattern = r'[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}'

regex = re.compile(pattern, flags=re.IGNORECASE) 
flags=re.IGNORECASE : #대소문자 구분없이 매칭하기

regex.findall(text)
=>
['dave@google.com', 'steve@gmail.com', 'rob@gmail.com', 'ryan@yahoo.com']

m = regex.search(text)
m
=>
<_sre.SRE_Match object; span=(5, 20), match='dave@google.com'>

m.start() => 5
m.end() => 20 (정규표현식에 매칭되는 인덱스 반환)

text[m.start():m.end()]
=>
'dave@google.com'

print(regex.sub('REDACTED', text))
Dave REDACTED
Steve REDACTED
Rob REDACTED
Ryan REDACTED

pattern = r'([A-Z0-9._%+-]+)@([A-Z0-9.-]+)\.([A-Z]{2,4})'
regex = re.compile(pattern, flags=re.IGNORECASE)
m = regex.match('wesm@bright.net')
m
=>
<_sre.SRE_Match object; span=(0, 15), match='wesm@bright.net'>

m.groups()
=>
('wesm', 'bright', 'net')

regex.findall(text)
=>
[('dave', 'google', 'com'), ('steve', 'gmail', 'com'), ('rob', 'gmail', 'com'), ('ryan', 'yahoo', 'com')]

print regex.sub(r'Username: \1, Domain: \2, Suffix: \3', text)
=>
Dave Username: dave, Domain: google, Suffix: com
Steve Username: steve, Domain: gmail, Suffix: com
Rob Username: rob, Domain: gmail, Suffix: com
Ryan Username: ryan, Domain: yahoo, Suffix: com

regex = re.compile(r""" (?P[A-Z0-9._%+-]+) @ (?P[A-Z0-9.-]+) \. (?P[A-Z]{2,4})""", flags=re.IGNORECASE|re.VERBOSE)
#re.VERBOSE : 문자열에 사용된 공백은 컴파일시 제거하라

m = regex.match('wesm@bright.net')
m.groupdict()
=>
{'domain': 'bright', 'suffix': 'net', 'username': 'wesm'}
```

### Vectorized string functions in pandas
- pandas.str
```
data = {'Dave': 'dave@google.com', 'Steve': 'steve@gmail.com', .....: 'Rob': 'rob@gmail.com', 'Wes': np.nan}
data = pd.Series(data)
data.str.contains('gmail')
=>
Dave     False
Rob       True
Steve     True
Wes        NaN
dtype: object

pattern
=>
'([A-Z0-9._%+-]+)@([A-Z0-9.-]+)\\.([A-Z]{2,4})'

data.str.findall(pattern, flags=re.IGNORECASE)
=>
Dave [('dave', 'google', 'com')] Rob [('rob', 'gmail', 'com')] Steve [('steve', 'gmail', 'com')] Wes NaN

matches = data.str.match(pattern, flags=re.IGNORECASE)
matches
=>
Dave ('dave', 'google', 'com') Rob ('rob', 'gmail', 'com') Steve ('steve', 'gmail', 'com') Wes NaN

matches.str.get(1) = matches.str[1]
=>
Dave     google
Rob       gmail
Steve     gmail
Wes         NaN
dtype: object
```
