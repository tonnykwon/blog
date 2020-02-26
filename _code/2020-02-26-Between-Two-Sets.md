---

title: "Between Two Sets"
date: 2020-02-26
categories: Code
mathjax: true
---

You will be given two arrays of integers and asked to determine all integers that satisfy the following two conditions:

1. The elements of the first array are all factors of the integer being considered
2. The integer being considered is a factor of all elements of the second array



**Function Description**

Complete the *getTotalX* function in the editor below. It should return the number of integers that are betwen the sets.

getTotalX has the following parameter(s):

- *a*: an array of integers
- *b*: an array of integers



처음 해석이 헷갈려서 문제를 이해하지 못했다. 해당 조건에 모두 만족하는 정수를 찾으려고 한다. 첫번째 조건은 첫 배열이 해당 수의 factor(인자)여야하며, 다음 조건은 해당 수가 두번째 배열의 인자여야한다.

결국 첫번째 배열의 최소공배수이면서 두번째 배열의 공약수인 수를 찾으면 되는 것이다.

최소공배수(Least Common Multiple)과 최대공약수(Greatest Common Divisor)에 대해 간단히 설명하자면, 최대공약수는 두 정수에서 공통되는 약수 중 가장 큰수를 말한다. 즉 두 수를 해당 수로 나누었을 때 딱 맞아떨어지는 수 중 가장 작은 수이다. Brute Force로도 구할 수 있지만 유클리드 호제법을 사용해서 구할 수 있다. gcd(a,b)에서 a mod b = 0이라면 gcd(a,b) = b가 된다. 여기서 gcd(a,b) = gcd(b, a mod b)가 성립하는 데 이를 유클리드 호제법이라 한다. 즉,  21 mod 18 = 3이고 18 mod 3 = 0이고 이 때 21과 18의 최대공약수는 3이 된다. 즉 (21 mod (18 mod 3))와 같이 나타냄으로써 최대공약수를 빠르게 구할 수 있다.

따라서 다음과 같은 방법으로 최대공약수를 구할 수 있다.

```python
def get_gcd(a,b):
    if a< b:
        a, b = b, a
    mod = a % b
    while mod >0:
        a = b
        b = mod
        mod = a % b
    return mod
```



최소공배수는 두 정수가 공통적으로 가지는 가장 작은 배수를 말한다. 만약 a와 b의 최소공배수를 구한다고 했을 때, 두 수의 최대공약수를 X라고 한다면 

$$a = X*x$$

$$ b= X*y $$ 

로 나타낼 수 있다. 이때 두 수의 최소공배수는 $$ X*x*y$$ 이므로 lcm(a,b) = a*b/gcd(a,b)로 구할 수 있다.

또 최대공약수는 다음과 같은 특징을 가지고 있다.

$$ gcd(a,b,c ) = gcd(gcd(a,b), c) = gcd(a, gcd(b,c)) = gcd(b, gcd(a,c))$$

최소공배수 또한 같은 특징을 가지고 있으므로 이 문제에서 사용할 수 있다.



코드는 다음과 같다.

```python
# a,b의 최소공배수
def get_lcm(a,b):
    return a*b/get_gcd(a,b)

# a 배열의 최소공배수
def lcm(a):
    if len(a)==1:
        return a[0]
    
    lcm_a = get_lcm(a[0],a[1])
    if len(a)==2:
        return lcm_a
    
    for i in range(2, len(a)):
        lcm_a = get_lcm(lcm_a, a[i])
	return lcm_a
    
def getTotalX(a, b):
    
    count = 0
    
	# a의 최소공배수
    lcm_a = lcm(a)
    
    # 공약수 찾기
    each_a = lcm_a
    while True:
        all_divide = sum([1 for each_b in b if each_b % lcm_a==0])
        # 최소공배수가 b의 공약수일때
        if all_divide == len(b):
            count +=1
            
        each_a += lcm_a
        # b보다 더 크면 중단
        if each_a > b[0]:
            break
            
    return count
```

