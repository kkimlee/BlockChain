# 2 타원곡선
유한체와 타원곡선을 이용해 다음장에서 타원곡선 암호 생성

## 2.1 정의
타원 곡선 식은 다음과 같음
<pre>
y<sup>2</sup> = x<sup>3</sup> + ax + b
</pre>

1차 방정식은 다음과 같음
<pre>
y = mx + b
</pre>

2차 방정식은 다음과 같음
<pre>
y = ax<sup>2</sup> + bx + c
</pre>

3차 방정식은 다음과 같음
<pre>
y = ax<sup>3</sup> + bx<sup>2</sup> + cx + d
</pre>

타원곡선은 다음과 같음
<pre>
y<sup>2</sup> = x<sup>3</sup> + ax + b
</pre>
3차방정식의 곡선과 차이는 y<sup>2</sup> 이며, y<sup>2</sup>로 인해 그래프가 x축 대칭이 됨    
3차곡선보다 기울기가 완만한데 이 또한 y<sup>2</sup> 때문임   
계숫값에 따라 곡선이 하나로 이어지지 않고 분리되기도 함    

타원곡선은 3차방정식 그래프에서 y > 0인 부분을 완만하게 만들고, y > 0인 부분을 x축 대칭이 되도록 만든 그래프로 볼 수 있음    
비트코인에 사용되는 타원곡선은 secp256k1이라고 하며 다음과 같음
<pre>
y<sup>2</sup> = x<sup>3</sup> + 7
</pre>

일반 정규식은 y<sup>2</sup> = x<sup>3</sup> + ax + b이며, 계숫값 a = 0, b = 7인 곡선으로 정의됨

## 2.2 파이썬으로 타원곡선 코딩
곡선 자체보다 곡선 위 개별 점들에 의미가 있음    
예를 들어, y<sup>2</sup> = x<sup>3</sup> + 5x + 7곡선에서 (x, y) = (-1, 1)인 점이 중요함    
따라서, 특정 곡선의 한 점<sup>point</sup>으로 Point 클래스를 정의함    
곡선의 일반식은 y<sup>2</sup> = x<sup>3</sup> + ax + b로 a와 b 두 계숫값으로 곡선을 특정할 수 있음    

```
class Point:

    def __inint__(self, x, y, a, b):
        self.a = a
        self.b = b
        self.x = x
        self.y = y
        if self.y**2 != self.x**3 + a * x + b:
            raise ValueError('({}, {}) is not on the curve'.format(x, y))
    
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y \
            and self.a == other.a. and self.b == other.b
