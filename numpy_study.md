
Numpy
-------------

**ndim** : array의 차원
```
data1 = [6, 7.5, 8, 0, 1]
array1 = np.array(data1)
data2 = [[1, 2, 3, 4], [5, 6, 7 ,8]]
array2 = np.array(data2)

array2.ndim
=> 2
array1.ndim
=> 1
```

**shape** : array의 차원의 tuple
```
array2.shape
=> (2,4)
array1.shape
=> (5,)
```

**dtype** : array의 원소의 자료형
```
array2.dtype
=> dtype('float64')
```

**zeros** : 원소의 값이 모두 0인 array
```
np.zeros(10)
=> array([ 0., 0., 0., 0., 0., 0., 0., 0., 0., 0.])
np.zeros((3,5))
=> array([[ 0., 0., 0., 0., 0., 0.], 
		[ 0., 0., 0., 0., 0., 0.], 	
		[ 0., 0., 0., 0., 0., 0.]])
```

**ones** : 원소의 값이 모두 1인 array
```
np.ones(9)
=> [1. 1. 1. 1. 1. 1. 1. 1. 1.]
```

**eye** : 대각 원소의 값이 모두 1이고 나머지 원소의 값이 모두 0인 nxn 행렬
```
np.eye(2)
=> [[1. 0.]
 [0. 1.]]
```

**astype** :

 - array의 자료형을 바꾸어 보여줌. 
 - 단, 원래의 array의 자료형이 바뀌는 것이 아니기 때문에 자료형이 바뀐 array를 사용하려면 또 다른 변수에 저장해서 사용해야함. 
 - astype(int) 일 때 array의 자료형이 float이면 내림한 정수 값을 반환함
```
arr = np.array([1,2,3,4,5])
arr.astype(float)
arr.dtype
=> [1. 2. 3. 4. 5.]
int32
```
 - int_array의 정수형 자료의 값을 float_array의 실수형 자료의 값으로 모두 바꿀 수 있음!
```
int_array = np.arange(5)
int_array
=> [0 1 2 3 4]

float_array = np.array([1.2,3.6])
int_array.astype(float_array.dtype)
=> [0. 1. 2. 3. 4.]
```

**array의 연산은 각 위치에 맞는 원소끼리 연산한다!** 
```
arr = np.array([[1., 2., 3.], [4., 5., 6.]])
arr * arr
=> [[ 1.  4.  9.]
 [16. 25. 36.]]
```


**★★ slicing -> array의 slice는 view이다. slicing된 view 자료를 변경하면 원래 array의 자료까지 변경된다**
```
arr = np.arange(10)
arr[5:8]=10 

# arr의 인덱스 5번부터 7번까지에 해당하는 자료를 10으로 바꾸어준다.

arr 
=> [ 0  1  2  3  4 10 10 10  8  9]
arr_slice = arr[0:2]
arr_slice
=> [10 10 10]
arr_slice[0:2]=64
arr
=> [ 0  1  2  3  4 64 64 10  8  9]
```

```
arr3d = np.array([[[1, 2, 3], [4, 5, 6]], [[7, 8, 9], [10, 11, 12]]])
old_values = arr3d[0].copy() 
# array 복사하여 old_value 변수에 지정

arr3d[0]=42
arr3d
=> [[[42 42 42]
  [42 42 42]]

 [[ 7  8  9]
  [10 11 12]]]

arr3d[0]=old_values
old_values
=>
[[1 2 3]
 [4 5 6]]
 
# array를 잘라도 array!

arr3d
=> [[[ 1  2  3]
  [ 4  5  6]]

 [[ 7  8  9]
  [10 11 12]]]

# array 안의 array를 인덱스를 통하여 자료값을 통째로 바꿀수도 있음!
```

**boolean** :

 - &는 and, |는 or
 - !=, ~(slicing 할 때) 는 not equal, ==는 equal
```
names = np.array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'])
names=='Bob'
=> [ True False False  True False False False]

# 원소끼리 비교하여 각각의 boolean 값을 반환함.

data = np.random.randn(7,4) #randn 사용하려면 np.random 사용!
data
=> [[ 0.03289026 -0.4853732   0.25022397 -0.84822805]
 [-1.44518729  0.34961776 -1.47306958  0.25102132]
 [-0.32162326  0.02240361 -0.23955512  1.16939586]
 [ 0.56130526  2.34553077  0.67105453 -1.7198809 ]
 [-0.17739653  0.26447075  0.36146401 -0.35182507]
 [ 0.7975443   0.11408052  1.20096968  0.21351796]
 [-0.96690576 -1.58633855  0.47188322  0.40314803]]

data[names=='Bob']
=> [[ 0.03289026 -0.4853732   0.25022397 -0.84822805]
 [ 0.56130526  2.34553077  0.67105453 -1.7198809 ]]

# data의 True 값에 해당하는 array만 가져옴

data[names=='Bob', 1]
=> [-0.4853732   2.34553077]

# boolean 값과 인덱스를 같이 사용해서 slicing할 수 있음!

names != 'Bob'
=> [False  True  True False  True  True  True]

data[~(names == 'Bob')] 
=> [[ 0.28403134  0.10692137  0.37935912 -0.91101568]
 [ 0.39395616  1.6191782   1.96753834  2.48583871]
 [ 1.74502053  0.60616381  0.3029351   0.1228419 ]
 [ 0.43423202  1.28874432 -0.10897491 -0.24118103]
 [ 0.10630512 -0.96176364 -0.13281918 -1.00116666]]

# False 값에 해당하는 array만 반환

mask = (names =='Bob') | (names =='Will')
mask
=> [ True False  True  True  True False False]

data
=> [[ 0.83715149 -0.05317648 -1.30349129  0.33725659]
 [ 0.80186846  1.1154714   0.07015922  0.00285414]
 [ 1.1252638  -0.05436221 -0.91957479 -1.37652739]
 [-0.27416624  0.78096591 -0.02808424 -0.48482693]
 [ 0.98468465  1.2047045  -0.8618517   0.97764115]
 [-0.43683057  0.92131134 -1.41010602 -0.37874089]
 [-1.2891628  -0.54546766  1.90896262  0.59248671]]
 
 
data[data<0]=0
data
=> [[0.83715149 0.         0.         0.33725659]
 [0.80186846 1.1154714  0.07015922 0.00285414]
 [1.1252638  0.         0.         0.        ]
 [0.         0.78096591 0.         0.        ]
 [0.98468465 1.2047045  0.         0.97764115]
 [0.         0.92131134 0.         0.        ]
 [0.         0.         1.90896262 0.59248671]]
```


**Fancy Indexing**
```
arr = np.empty((8,4))
for i in range(8):
    arr[i] = i
arr
=> 
[[0. 0. 0. 0.]
 [1. 1. 1. 1.]
 [2. 2. 2. 2.]
 [3. 3. 3. 3.]
 [4. 4. 4. 4.]
 [5. 5. 5. 5.]
 [6. 6. 6. 6.]
 [7. 7. 7. 7.]]
 
arr[[3,2,6]]
=> 
[[3. 3. 3. 3.]
 [2. 2. 2. 2.]
 [6. 6. 6. 6.]]
 
arr = np.arange(32).reshape((8,4))
arr
=> [[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]
 [12 13 14 15]
 [16 17 18 19]
 [20 21 22 23]
 [24 25 26 27]
 [28 29 30 31]]


arr[[1,5,7,2],[0,3,1,2]]
=> [ 4 23 29 10]


# array의 [1,0], [5,3], [7,1], [2,2] 인덱스의 원소를 뽑아 array로 만들어줌

arr[[1, 5, 7, 2]][:, [0, 3, 1, 2]]
=> [[ 4  7  5  6]
 [20 23 21 22]
 [28 31 29 30]
 [ 8 11  9 10]]
 
# 인덱스 1, 5, 7, 2 자리의 모든 array 안에서 인덱스 0, 3, 1, 2 자리의 값을 차례대로 가져와 array로 반환

# arr[np.ix_([1, 5, 7, 2], [0, 3, 1, 2])] 
= arr[[1, 5, 7, 2]][:, [0, 3, 1, 2]]
=> [[ 4  7  5  6]
 [20 23 21 22]
 [28 31 29 30]
 [ 8 11  9 10]]
```

**transposing arrays & swaping axes**
```
arr = np.arange(15).reshape((3, 5))
arr
=> [[ 0  1  2  3  4]
 [ 5  6  7  8  9]
 [10 11 12 13 14]]
arr.T
=> [[ 0  5 10]
 [ 1  6 11]
 [ 2  7 12]
 [ 3  8 13]
 [ 4  9 14]]


arr = np.arange(16).reshape((2, 2, 4))
arr => 
[[[ 0  1  2  3]
  [ 4  5  6  7]]

 [[ 8  9 10 11]
  [12 13 14 15]]]
  
arr.transpose((1, 0, 2))
=> [[[ 0  1  2  3]
  [ 8  9 10 11]]

 [[ 4  5  6  7]
  [12 13 14 15]]]

arr[0][1]
=> [4 5 6 7]
arr[1][0]
=> [ 8  9 10 11]


# x, y, z로 보고 y, x가 바뀌었다고 생각하자!

● np.swapaxes : 축을 통해 array의 배열을 바꾸어줌.

arr.swapaxes(1,2)
=> [[[ 0  4]
  [ 1  5]
  [ 2  6]
  [ 3  7]]

 [[ 8 12]
  [ 9 13]
  [10 14]
  [11 15]]]
```
 
 **universal functions**

 - np.modf(array) : 정수 부분과 소수 부분을 분리하여 첫 번째로 소수 부분의 자료값의 array, 두 번째로 정수 부분의 자료값의 array를 반환한다.
```
arr=np.random.randn(7)*5
arr
=> [ 5.11800395  3.10809692 -5.86378264  2.90823198  2.81345301  1.36455867
  2.80073916]
np.modf(arr)
=> (array([ 0.11800395,  0.10809692, -0.86378264,  0.90823198,  0.81345301, 0.36455867,  0.80073916]), array([ 5.,  3., -5.,  2.,  2.,  1.,  2.]))
```
 - np.abs(x) : x의 절댓값
 - np.sqrt(x) : x의 제곱근
 - np.square(x) : x의 제곱
 - np.ceil(x) : x의 내림한 값
 - np.floor(x) : x의 반올림한 값

**Conditional Logic as Array Operations**

- zip 함수 : 여러가지 list를 한꺼번에 slicing 할때 사용함
```
xarr = np.array([1.1, 1.2, 1.3, 1.4, 1.5])
yarr = np.array([2.1, 2.2, 2.3, 2.4, 2.5])
cond = np.array([True, False, True, True, False])
result = [(x if c else y) for x, y, c in zip(xarr, yarr, cond)]
= np.where(cond, xarr, yarr)
result
=> [1.1, 2.2, 1.3, 1.4, 2.5]


# xarr, yarr, cond의 리스트 3개를 차례대로 접근하여 True이면 그 인덱스에 해당하는 xarr의 값을 가져오고, False이면 그 인덱스에 해당하는 yarr의 값을 가져온다.

# 저 코드와 같은 기능을 하는 메쏘드 => np.where(조건문, true일 때 지정할 값, false일 때 지정할 값)


arr = np.random.randn(4, 4)
arr
=> [[ 0.09719634 -0.00915221  0.14236223  2.07891208]
 [ 1.91995174 -0.53762946 -0.55671224  0.28635465]
 [ 0.85223063 -0.28343224  2.0178921   0.0767574 ]
 [ 0.46115336  0.3030742  -0.34372426  1.5367316 ]]
np.where(arr >0, 2, -2)
=> [[ 2 -2  2  2]
 [ 2 -2 -2  2]
 [ 2 -2  2  2]
 [ 2  2 -2  2]]


# np.where 메쏘드는 조건문에 true에 해당하는지, false에 해당하는지를 모든 원소에 대해 판단하여 값을 return 한다.
```

**Mathematical and Statistical Methods**

 - np.sum(x) : x의 합계
 - np.mean(x) : x의 평균
 - np.std(x) , np.var(x) : x의 표준편차, x의 분산 
 - np.min(x), np.max(x) : x의 최솟값, 최댓값
 - np.argmin(x), np.argmax(x) : x의 최솟값, 최댓값인 array의 index
 - np.cumsum(x) : x의 누적합


arr = np.array([[0, 1, 2], [3, 4, 5], [6, 7, 8]])

**Methods for Boolean Arrays**

 - boolean값은 True는 1, False는 0으로 할당
 - numpy.any() : 하나라도 True값이 있으면 True 반환
 - numpy.all() : 모든 값이 True이면 True 반환

```
bools = np.array([False, False, True, False])
bools.any()
=> True
bools.all()
=> False
```

**Unique and Other Set Logic**
 
 - np.unique(x) : x의 유일값 반환
 - np. in1d(x, y) : x에 y가 포함되어 있는가를 원소끼리 비교하여 boolean값 반환
```
names = np.array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'])
np.unique(names)
=> ['Bob' 'Joe' 'Will']

values = np.array([6, 0, 0, 3, 2, 5, 6])
np.in1d(values, [2, 3, 6])
=> [ True False False  True  True False  True]
```
**대표적인 numpy.linalg 함수들**

 - dot : 행렬 곱
 -  trace : 대각원소의 합을 계산
 -  det : 행렬식 반환
 -  qr : qr곱 반환
 -  lsqtsq : 최소 제곱해 반환
 -  solve : 선형시스템 Ax=b 계산
 -  inv : 역행렬 반환

**random number generation**

 - rand : 균등분포로부터 샘플을 뽑아 반환
 - randint : 랜덤하게 정수 추출
 -  randn : 평균이 0이고 표준편차가 1인 정규 분포로부터의 표본 추출
 -  bionomial : 이항분포에서 표본 추출
 - normal : 정규분포에서 표본 추출
