# 3 타원곡선 암호
메시지 서명과 검증에 필요한 기본 알고리즘을 설명하기 위해 유한체 연산과 타원곡선의 개념을 이용

## 3.1 실수체에서 정의된 타원곡선
실수는 체<sup>field</sup>의 요건을 만족하여 실수체를 이루기 때문에 타원곡선 함수의 변숫값으로 사용이 가능함  
유한체와 다르게 실수체에는 무한의 원소들이 존재. 유한체와 실수체는 다음과 같이 동일한 성질을 가짐  

<pre>
    1. a와 b가 집합의 원소이면 a + b와 a · b도 그 집합의 원소임
    2. 0이라는 원소가 존재하면 그 원소는 a + 0 = a 성질을 만족함
    3. 1이라는 원소가 존재하면 그 원소는 a · 1 = a 성질을 만족함
    4. a가 어떤 집합의 원소이면 -a라는 원소 또한 그 집합에 존재하며 a + (-a) = 0 성질을 만족함
    5. 0이 아닌 a가 집합의 원소이면 a<sup>-1</sup>이라는 원소 또한 그 집합에 존재하며 그 원소는 a · a<sup>-1</sup> = 1 성질을 만족함
</pre>

1번 성질은 일반 덧셈과 곱셉의 정의에 해당하며, 2번과 3번은 덧셈과 곱셈에 대한 항등원이 존재한다는 것을 의미함. 4번과 5번은 -x, 1/x의 존재를 보여줌  
  
실수는 좌표축 위의 점과 빠짐없이 일대일 대응이 가능하여 그래프에 그리기가 쉬움. y<sup>2</sup> = x<sup>3</sup> = 7은 그림[3-1]과 같이 그릴 수 있음
  
![그림 3-1](./그림/그림3-1.png)  
###### [그림 3-1] 실수에서 정의된 secp256k1(y<sup>2</sup> = x<sup>3</sup> + 7 그래프)  
  
타원곡선 함수의 변수와 계수들이 어떤 체에서 정의되었는지와 상관없이 그 타원곡선에서 점 덧셈의 성질이 성립함  
즉, 점 덧셈 방정식의 근이 유한체 소속인지 실수체 소속인지와 관계없이 점 덧셈이 성림함  
이때, 각 체에 맞는 사칙연산 방법을 사용해야 함  
  
## 3.2 유한체에서 정의된 타원곡선
F<sub>103</sub>에서 정의된 타원곡선 방정식 y<sup>2</sup> = x<sup>3</sup> + 7을 예로 타원곡선을 그림  
점 (17, 64)가 곡선 위에 있는지 보기 위해 양변에 좌푯값을 대입한 결과는 다음과 같음  
  
<pre>
    y<sup>2</sup> = 64<sup>2</sup> % 103 = 79
    x<sup>3</sup> + 7 = (17<sup>3</sup> + 7) % 103 = 79
</pre>
  
위 결과를 통해 점 (17, 64)가 타원곡선 y<sup>2</sup> = x<sup>3</sup> + 7 위에 있다는 것을 확인 할 수 있음  
그러나 유한체에서 정의된 타원곡선의 모양은 실수체에서 정의된 것과 매우 다른 모습을 보임(그림 3-2)  
  
![그림 3-2](./그림/그림3-2.png)  
###### [그림 3-2] 유한체에서 정의된 타원곡선
  
실수체와 달리 유한체의 원소들은 연속적이지 않기 때문에 이와 같은 결과가 나타남  
유한체에는 음수가 없기 때문에 x축 대칭이 나타나지 않으며, 타원곡선의 y<sup>2</sup>으로 인해 y축의 중간값(100)을 지나는 수평축을 기준으로 대칭이 나타남  
그러나 유한체에서 정의된 각종 연산을 점 덧셈 방정식에 사용할 수는 있음  

## 3.3 유한체에서 정의된 타원곡선 코딩하기
타원곡선의 점을 정의하고 유한체에서의 +,-,＊,/, 연산자를 정의했기 때문에 점의 좌표가 유한체 원소인 타원곡선의 점을 표현하기 위해서 아래와 같이 2개의 클래스를 활용해야 함  
  
```
>>> from ecc import FieldElement, Point
>>> a = FieldElement(num=0, prime=223)
>>> b = FieldElement(num=7, prime=223)
>>> x = FieldElement(num=192, prime=223)
>>> y = FieldElement(num=105, prime=223)
>>> p1 = Point(x, y, a, b)
>>> print(p1)
Point(192,105)_0_7 FieldElement(223)
```
  
Point 클래스의 인스턴스를 초기화할 때 아래 코드가 실행됨  

```
class Point:
    def __init__(self, x, y, a, b):
        self.a = a
        self.b = b
        self.x = x
        self.y = y
        if self.x is None and self.y is None:
            return
        if self.y**2 != self.x**3 + a * x + b:
            raise ValueError('({}, {}) is not on the curve'.format(x, y))
```
  
덧셈(+), 곱셈(＊), 거듭제곱(＊＊), 같지 않음을 나타내는 비교 연산자(!=)는 내장 연산자가 아닌 FieldElement 클래스에서 정의된 __add__, __mul__, __pow__, __ne__ 메서드를 사용함
  
타원곡선 위 점의 좌표를 유한체로 표현하기 위해 2개의 클래스(FieldElement, Point)를 작성하였으며, 다음 코드를 이용해 테스트 가능함  
  
```
class ECCTest(TestCase):
    
    def test_on_curve(self):
        prime = 223
        a = FieldElement(0, prime)
        b = FieldElement(7, prime)
        valid_points = ((192, 105), (17, 56), (1, 193))
        invalid_points = ((200, 119), (42, 99))
        for x_raw, y_raw in valid_points:
            x = FieldElement(x_raw, prime)
            y = FieldElement(y_raw, prime)
            Point(x, y, a, b) ①
        for x_raw, y_raw in invalid_points:
            x = FieldElement(x_raw, prime)
            y = FieldElement(y_raw, prime)
            with self.assertRaises(ValueError):
                Point(x, y, a, b) ①
```
  
> ① FieldElement 객체를 Point 클래스 생성자의 매개변수로 넘김. 이후 생성자 안에서 코드 실행시 FieldElement 에서 정의된 수학 연산자가 적용됨
  
정의가 완료된 후 다음과 같이 테스트 코드 실행이 가능해짐  
  
```
>>> import ecc
>>> from helper import run ①
>>> run(ecc.ECCTest('test_on_curve'))
.
________________________________________________________
Ran 1 test in 0.001s

OK
```
  
> ① helper 모듈은 단위 테스트를 실행시키는 함수(run)를 포함해서 매우 유용한 유틸리티 함수들을 제공함  

## 3.4 유한체에서 정의된 타원곡선 위 두 점의 덧셈
아래 직선의 방정식을 포함하여 모든 방정식을 유한체에서 사용할 수 있음  
  
<pre>
    y = mx + b
</pre>

직선의 방정식은 [그림 3-3]과 같이 직선이 아닌 점들로 나타남  
  
![그림 3-3](./그림/그림3-3.png)  
###### [그림 3-3] 유한체에서의 직선  
  

