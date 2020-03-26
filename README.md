[TOC]
# CS61a Notes       
*By Zian W.   01/15/2019 - 03/03/2019* (Discontinued)



## Lecture 2
>1. Python first looks for variables in the current environ, if not found, then search them in the outer environ.

***Ex. 1***  
```python
num = 1
def f():
    return num
f()
```
The result would be 1. No errors would occur.  

***Ex. 2*** 
```python
def square(square):
	return square*square
square(2)
```
The result would be 4. No errors would occur, since the variable square in the the local environment of square function, meaning no conflict would occur.  


##Lecture 3
###1. Print
The return value of print() is None. However, in the interactive interpreter, None will not be displayed unless another print() is called. In other words, the variable passed to the print() function would be leaked, while the return value is always a None. The print() function itself is not a closed environment.  

***Ex. 1***
```py
print(None)
```
None  

***Ex. 2***
```py
print(print(1), print(2))
```
1  
2  
None None  

***Ex. 3***
```py
def f():
	pass
a = f()
print(a)
```
None  

***Ex. 4***
```py
None
```
*(Nothing would appear.)*  

###2. Operators

> / or operator.truediv( )  

2013 / 10 = 201.3

> // or operator.floordiv( )  

2013 // 10 = 201

> % or operator.mod( )  

2013 % 10 = 3
### 3. Muitl-assgin
```py
def divide(n,d):
	return n // d, n % d
quotient, remainder = divide(2013,10)
```
Valid √

### 4. Bool
False value in python: **0, False, empty string, None** ...  


##Lecture 4
>**High-order Function**: functions that take another function as an argument value or return a function as a return value
### 1. Assert
```python
def area_square(r):
	assert r > 0, "A length must be positive."
	return r*r
area_square(-2)
```
The assert statement is used to make sure all variables passed into the function meet the requirements. The above call will throw an error "A length must be postive".

### 2. Don't repeat yourself(Dry)
A single function should only be designed for a single purpose. Do not make mutiple functions have overlapped utilities.  

**Wrong Case**
```py
def area_square(r):
	assert r > 0, "A length must be postive."
	return r * r

def area_circle(r):
	assert r > 0, "A length must be postive."
	return r *r * pi

def area_hexagon(r):
	assert r > 0, "A length must be positive."
	return r *r * 3 * sqrt(3) / 2
```
In this case the assert statement is repeated **3 times** which does not fit the Dry rule.  
Therefore, we must spilit the assert statement and design another function for this purpose.  

**Right Case**
```py
def area(r,shape_constant):
	assert r > 0, "A length must be postive."
	return r * r * shape_constant

def area_square(r):
	return area(r,4)

def area_circle(r):
	return area(r,pi)

def area_hexagon(r):
	return area(r, 3 * sqrt(3) / 2)
``` 
In this way, each utility is separated. By maximally spliting each unit from a function, we could maximally increase reusability.

### 3. Generalize computational process
Consider the implementation below:  
```py
def identity(x):
	return x

def cube(x):
	return pow(x,3)

def sigmoid(x):
	return 1 / (1 + pow(math.e, - x))

def summation(n, term):
	total, k = 0, 1
	while k <= n:
		total, k = total + term(k), k + 1
	return total
```
In this way, ***term*** is passed to ***summation*** as a varibale function(*function passed as an argument value*). When the system figures it could not find the specific function like (e.g. if the ***cube*** is passed into ***summation***) in the inner environment, it will look for it in the outer environ and find it there. Therefore, the specific ***term*** and the ***summation*** function are separated.  
### 4. Functions that return a function
```python
def make_adder(n):
	'''Return a function that will add a passed integer to n and return it.
	>>> add_three = make_adder(3)
	>>> add_three(4)
	7
	'''
	def adder(k):
		return k + n
	return adder
```
The base n is passed to the local function adder and when we assign make_adder() to a varibale the parameter n is fixed as it is retrieved from the parent environment. So the only formal parameter for the new assigned function would be k.  
Or, we could do the following call:  
```py
make_adder(3)(4) '''equals to add_three(4)'''
``` 
The result is 7. No error would occur.  
### 5. Lambda Expressions
```py
square = lambda x: x * x
square(10)  '''100'''
(lambda x: x * x)(10) '''100'''
```
Both the ways above are valid.  

Note: **lambda cannot contain conditional statements**   
The def function has a intrinsic name. If we define square() by DEF, and then we type square, we will get **function square at 0x---------.**  
While lambda doesn't. If we define square by lambda, and then we type square it the interactive interpreter, we will get **function lambda at 0x---------.**  

## Lecture 5:   Environments 
### 1. Another interpretation of nested functions
```py
def make_adder(n):
	'''Return a function that will add a passed integer to n and return it.
	>>> add_three = make_adder(3)
	>>> add_three(4)
	7
	'''
	def adder(k):
		return k + n
	return adder
```
In **5.4** my understanding of the above definition is a parent function's order that fixes the child function adder()'s n to be 3. And this is like an inheritance process and by this the created add_three() function no longer has a relation to make_adder().  

The lecturer's interpretation is that the creation process is not an inheritance or separation. The add_three() function still stays inside make_adder(3). And the n within adder() is not defined. When add_three(4) is called. The system will notice that n is not defined, meaning that it cannot find it in the current frame or environment. As a result, it will start looking for n in the outer/parent environment and find n there, i.e. 3. This is because when make_adder(3) is called in the environment of make_adder() n is equal to 3. And when adder() looks for n in the outer environment, it will find it there. This interpretation is consistent with **2.1**.  

### 2. Local Names
```py
def f(x,y):
	return g(x)

def g(a):
	return a + y

result = f(1, 2)
``` 
The above call will cause an error "global name 'y' is not defined" because g and f are in the same environment. In other words, when g() is called the system creates another frame in the global environment parallel to f(), and when g() tries to find y in the outer environment it will fail.

### 3. global and nonlocal (To be continued)
When 'global x' is called it will bind current x to the global x in the outest environment and then we could change the value of global variables by change it in the child environment. Without calling global, changing the value of varibles not defined in the current frame will cause an error.
```py
x = 1

def f():
	x += 1
	return x

f() ''' Error: local variable 'x' referenced before assignment '''
----------------------------------------------------------------------
x = 1

def g():
	global x
	x += 1
	return x

g()  ''' Result: 2'''

print(x) '''Result: 2'''
```
nonlocal is used in nested functions which could change the value of the parent variable(not global) without affecting the value in the global environment. **x must be defined in the parent environment without using global.**
```py
x = 1

def f():
	x = 2
	print(x)
	def g():
		nonlocal x
		x += 1
		print(x)
	g()
	print(x)

f()
print(x)
'''Result:
2
3
3
1
'''
```

## Lecture 6: Iteration
### 1. Naive inverse function
If we have a square function, how could we create a sqrt function applicable to perfect squares ? 
```py
def square(x):
	return x*x
------------------------------
def search(f):
	x = 0 
	while not f(x):
		x += 1
	return x

def inverse(f):
	return lambda y: search(lambda x: f(x) == y)    
'''
y is the number waiting to be sqrted.

My personal understanding is the equivalent as follows:
def inverse(f):
	def h(y):
		def is_y(x):
			return f(x) == y
		return search(is_y)
	return h
'''

sqrt = inverse(square)
```
The nested lambda is a bit tricky, more time needed to fully digest it.

### 2. Self-referencing

***Ex. 1***
```py
def print_all(x):
	print(x)
	return print_all

print_all(1)(3)(5)

'''Result:
1
3
5
'''
```  

***Ex. 2***
```py
def print_sum(x):
	print(x)
	def next_sum(y):
		return print_sum(x + y)
	return next_sum

print_sum(1)(3)(5)

'''Result:
1
4
9
'''
```
When print_sum(1)(3)(5) is called, first 1 is passed to it and 1 is printed. The function then returns next_sum(y) which returns print_sum(1+y). When 3 is passed to next_sum(1+y), print_sum(4) is called and 4 is printed. Next, because x is 4 now, the new returned next_sum(y) will return print_sum(4+y). When 5 is passed to it, print_sum(9) is called and 9 is thus printed.

## Lecture 7: Recursion  

### 1. Recursive Function
> Definition: A function is called recursive if the body of that function calls itself, either directly or indirectly.

***Ex. 1***  Sum digits without a while statement   
```py
def split(n):
	return n // 10, n % 10

def sum_digits(n):
	if n < 10:
		return n
	else:
		all_but_last, last = split(n)
		return sum_digits(all_but_last) + last

sum_digits(2013) 
'''
6
'''
```
Usually, a recursive function is composed of a conditional statement, a base case, and a recursive case. First, the parameter is passed to the conditional statement and if it is true, the base case is returned and the recursion ends. Otherwise, the recursive case is called and the recursion keeps going. An recursion always terminates at the base case and the function then operates on all the results staying in the current environment waiting to be used from the first recursive result to the base case.

### 2. Mutual Recursion
**Luhn Algorithm**: using in credit card number verification  

Steps:   

1. From the rightmost digit, which is the check digit, moving left, double the value of every second digit; and the doubled value is        greater than nine, sum the digits of the doubled value.
2. Take the sum of all resulted digits.
If the original number is 138743. First starts at 3. Move left. 4 is doubled to 8. Move left. 7 doesn't need any transformation. Then 8. Doubled to 16 and sum 1 and 6 to 7. Then 3. Then 1, doubled to 2. And the sum would be 3+8+7+7+3+2 equals to 30.  

A Luhn sum of a valid credit number is always a multiple of ten. Any replacement or transposition would cause a non-10x result.   

**Implemention using recursion**:
```py
def split(n):
	return n // 10, n % 10

def sum_digits(n):
	if n < 10:
		return n
	else:
		all_but_last, last = split(n)
		return sum_digits(all_but_last) + last

------------------------------------------------------
def luhn_sum(n):
	if n < 10:
		return n
	else:
		all_but_last, last = split(n)
		return luhn_sum_double(all_but_last) + last

def luhn_sum_double(n):
	all_but_last, last = split(n)
	luhn_digit = sum_digits(2 * last)
	if n < 10:
		return luhn_digit
	else:
		return luhn_sum(all_but_last) + luhn_digit
```  
For a number passed to luhn_sum(), taking 2476 as an example, it is firstly split into 247 and 6. 6 is last which stays in the current environment and 247 is all_but_last which is then passed to luhn_sum_double(). In luhn_sum_double(), 247 is split into 24 and 7. luhn_digit therefore is 1 + 4 = 5. Cause 24 is greater than 10, 24 is then passed to luhn_sum() and here the we already get luhn_sum(2476) = luhn_sum(24) + 5 + 6. When luhn_sum() operates on 24, it is clear that we will get 4 + 2 * 2 and the final value is then 11 + 8 = 19. Using this mutual recursion method would naturally solve the second digit doubling problem without using any conditional statement due to the alternating struture.

### 3. Recursion and Iteration
Iterative version of sum_digits:
```py
def split(n):
	return n // 10, n % 10

def sum_digits(n):
	if n < 10:
		return n
	else:
		all_but_last, last = split(n)
		return sum_digits(all_but_last) + last

------------------------------------------------------
def sum_digits_iter(n):
	sum = 0
	while n > 10:
		n, last = split(n)
		return sum + last
	return n + sum
```

#### Exercises
```py
y = "y"
h = y
def y(y):
	h = "h"
	if y == h:
		return y + "i"
	y = lambda y: y(h)
	return lambda h: y(h)

y = y(y)(y)  # the value of y is 'hi'
```
Here we have a tricky one. Let's go through all this from up to bottom. First **y** is assigned to 'y' and so is **h** then we define a new function y and the previous value is erased. When **y(y)** is called, first **h** is and assigned to 'h' and cause **y** is now a function(**waiting to be verified**) and if statement turns out to be a False so we could just ignore this. Then **in this sub environemt** **y** is assigned to a new lambda expression where it takes in a random function f and opeartes on a given variable **h**(*the value of which is 'h'*). Now we come to the return line and we finally get a function that takes a random variable h in and is then operated on by the given function **y**. The **y** here is the lambda function above and it is the equavalent of f('h') where f is still unknown. Then we get to the outside environment and **y(y)(y)** the last **(y)** here points to the def function body and so this entire function is passed to f('h') as the f. Therefore we finally get y('h') and cause the variable passed in, 'h', is equal to the **h** in the sub environment, the result is obviously a "hi".

## Lecture 8: Tree Recursion  
### 1. Order of Recursive Calls  
Consider the function below:
```py
def cascade(n):
	if n < 10:
		print(n)
	else:
		print(n)
		cascade(n // 10)
		print(n)

cascade(12345)
''' Result:
12345
1234
123
12
1
12
123
1234
12345
'''
```
Here what makes the function call tricky is the order of printing behavior during the running process. When 12345 first reaches the inside cascade which takes in 1234, the next print line is still waiting its varibale. Therefore, print(12345) stays at the outest loop, only when the inside prints finish will the outest n be printed. So it follows such a logic: a new call interrupts the flow of (outest -> outest) and it becomes (outest -> inner -> outest), when the inner part keeps being interrupted and inserted with new chunk, the flow becomes (outest -> 2rd outest -> ... -> innermost(base con met, recursion ends, starting quitting loops) -> ... -> 2rd outest -> outest).  
The cascade function could also be defined as the shorter version below:
```py
def cascade(n):
	print(n)
	if n >= 10:
		cascade(n // 10)
		print(n)		
```

### 2. Inverse Cascade
One way to implement the inverse cascade function:
```py
def inverse_cascade(n):
	grow(n)
	print(n)
	shrink(n)

def f_then_g(f, g, n):
	if n:
		f(n)
		g(n)

grow = lambda n: f_then_g(grow, print, n // 10)
shrink = lambda n : f_then_g(print, shrink, n // 10)

inverse_cascade(1234)
'''Result:
1
12
123
1234
123
12
1
'''
```
First, when grow(1234) is called, it first calls another grow(123) and 123 is waiting to be printed as the g(n) in f_then_g(). When it keeps moving on, it wll finally reaches the state where n == 0 and recursion stops. Now, it will starts jumping from innermost to outermost, print 1 first, then 12, then 123, then the job of grow is done. Then comes to print(1234). And when shrink(1234) is called, first
it print(123), then shrinks to 12, print(12), then shrinks to 1 and print(1) then n is now 0 and the recursion ends. Since there is no nother job waiting to be fininshed by shrink unlike grow, the entire call of inverse_cascade is done.

### 3. Tree Recursion
> Tree-shaped processes arise whenever executing the body of a recursive function makes more than one call to that function.
The most classical exmaple of tree recursion is Fibonacci series:
```py
def fib(n):
	assert n > 0 'n must be a natural number'
	if n == 0:
		return 0
	elif n == 1:
		return 1
	else:
		return fib(n-1) + fib(n-2) 
```  

***Ex.1*** Counting Partitions  

This function takes in 2 positive-interger parameter n and m, and what it does is to count how many possible ways there exist to express the value of n by spliting it up into the summation of a series of positive integers each of which could not exceed m.

*Hint:* Take count_partitions(5, 3) as an instance, the answer to which is actually 5, we could break it down into simpler problems and add it up to get the final answer. When we use 3s, the number of all possible ways becomes count_partitions(2, 3). When we do not use any 4s, the number of all possible ways would be count_partitions(5, 2). And we could keep dividing it into smaller units.  
```py
def count_partitions(n, m):
	if n == 0:
		return 1 
	elif n < 0:
		return 0
	elif m == 0:
		return 0
	else:
		with_m = count_partitions(n-m, m)
		without_m = count_partitions(n, m-1)
	    return with_m + without_m
```
To clarify, I would use () to denote the count_partitions function in the following part.    

**1.** (5,3) -> (2,3) + (5,2) 

**2.** (2,3) -> (-1,3) + (2,2) = (2,2) -> (0,2) + (2,1) -> 1 + (1,1) + (2,0) = 1 + (0,1) + (1,0) = 2  

**3.** (5,2) -> (2,2) + (5,1) = 2 + (4,1) + (5,0) = 2 + (3,1) + (4,0) = 2 + (0,1) + (1,0) = 3  

**4.** (5,3) = 2 + 3 = 5  

***Ex.2***  Make Repetitor
Implement a one-line function using what has been provided such that it takes one f and one n and returns a function that takes one x and this function would apply f to m for n times and return the result.  
```py
def accumulate(combiner, base, n, term):
    if n == 0:
    	return base
    else:
    	return combiner(term(n), accumulate(combiner, base, n-1, term))

def compose1(f, g):
	def h(x):
		return f(g(x))
	return h

---------------------------------------------------------------------------
def make_repeater(f, n):
    """Return the function that computes the nth application of f.
    >>> add_three = make_repeater(increment, 3)
    >>> add_three(5)
    8
    >>> make_repeater(triple, 5)(1) # 3 * 3 * 3 * 3 * 3 * 1
    243
    >>> make_repeater(square, 2)(5) # square(square(5))
    625
    >>> make_repeater(square, 4)(5) # square(square(square(square(5))))
    152587890625
    >>> make_repeater(square, 0)(5)
    5
    """
    return accumulate(compose1, lambda x: x, n, lambda x: f)
```
First, when we pass compose1 as a parameter, we note that the required arguments for this in the definition of accumulate doesn't meet what has been specified, cause term(n) is a number not a function f. Therefore, we use a trick lambda x:f for the fourth parameter and by this way whatever n is passed to term it will just return term itself. For instance, if we call make_repeater(square, 2)(5) we will get square(accumulate(compose1, 5, 1, lambda x: square))(5) and then get square(square(accumulate(compose1, 5, 0, lambda x: square)))(5) and cause n is 0 now then entire accumulate expression becomes the identity function, so we get 625. In a nutshell, we tranform the two seemingly incorret arguments into a standard f and g format, term(n) becomes term and accumulate(......) turns out to be the identity function at last.

***Ex. 3***  Anonymous Factorial (HW04 Extra)
```py
anonymous_factorial = (lambda f: lambda n: f(f, n))(lambda f, n: 1 if n == 1 else n * f(f, n-1)) 
---------------------------------------------------------------------------------------------------------
# Translation:
def phase1(f):
	def h(n):
		return f(f, n)

def phase2(f, n):
	if n == 1:
		return 1
	else:
		return n * f(f, n-1)

anonymous_factorial = phase1(phase2)

''' Take anonymous_factorial(5) as an example 
h(5) -> phase2(phase2, 5) -> 5 * phase2(phase2, 4) -> .... -> 5 * 4 * 3 * 2 * 1 = 120
'''
```

## Lecture 9 Function Examples
### 1. Currying
> Currying is transforming a multi-argument function into a single-argument, higher-order function.  

```py
def original(f, x, y):
	return f(x, y)

original(add, 3, 2010)
-------------------------------------
def curry2(f):
	def g(x):
		def h(y):
			return f(x, y)
		return h
	return g

make_adder = curry2(add)
add_three = make_adder(3)
add_three(2010)
'''
Works same as:
curry2 = lambda f: lambda x: lambda y: f(x, y)
'''
```

### 2. Decorators
Note the differences by the following comparison
```py
def trace1(f):
	def traced(x):
		print('Calling', f, 'on', x)
		return f(x)
	return traced
-------------------------------------
@trace1
def square(x):
	return x * x

@trace1
def q(x):
	return square(square(x))
'''
In this way, by calling 
square(1) you will get 1 print line and 1 return value, by calling
q(1) you will get 3 print line(1 q + 2 square in order) and 1 return value.
'''
-------------------------------------
def square(x):
	return x * x
square = trace1(square)

def q(x):
	return square(square(x))
'''
In this way, by calling
square(1) you will get 1 print and 1 return, by calling
q(1) you will get 2 print(cause q is not decorated) and 1 return
'''
-------------------------------------
def square(x):
	return x * x

def q(x):
	return square(square(x))
q = trace1(q)
'''
In this way, by calling
square(1) you will not get any print, by calling
q(1) you will get 1 print(cause q is not decorated).
'''
```
Also, note that do not decorate a function twice by @ and direct assigning, otherwise the print will be called twice and in the first print the decorated function will lose its name and become anonymous.  

## Lecture 10 Data Abstraction
### 1. Data Abstraction
Create add, mul, and equals methods for manipulating rational numbers:
```py
rational(n, d) # Constructor: compose a rational number 
numer(x)       # Selector: decompose a rational number
denom(x)
--------------------------------------------
def add_rational(x, y):
	nx, dx = numer(x), denom(x)
	ny, dy = numer(y), denom(y)
	return (nx * dy + ny * dx) / (dx * dy)

def mul_rational(x, y):
	return numer(x) *　numer(y) / (denom(x) * denom(y))

def equal_rational(x, y):
	return numer(x) * denom(y) == numer(y) * denom(x)
``` 

### 2. Pairs
```py
pair = [1, 2] 	# Unpack a list
x, y = pair

from operator import getitem
x, y = getitem(pair, 1), getitem(pair, 2)
```  
Representing rational numbers:
```py
def rational(n, d):
	return [n, d]

def numer(x):
	return x[0]

def denom(y):
	return x[1]
----------------------------------
# Optimize the constructor
from fractions import gcd

def rational(n, d):
	g = gcd(n, d)
	return [n // g, d // g]
```

### 3. Abstraction Barriers
Programmers in each layer should only use and care about the abstraction and manipulating methods within that layer:
1. [Use] treats rational numbers as whole data values. / add_rational(), mul_rational(), equal_rational()...
2. [Create] treats them as numerators and denominators. / rational(), numer(), denom()
3. [Implement] treats them as two-element lists. / list literals and element selection
4. Bottom layer implementation

Violating abstration barriers:
```py
add_rational([1, 2], [3, 4])  # Should not use constructors
def divide_rational(x, y):
	return [x[0] * y[1], x[1] * y[0]] # Should insteat use numer() and denom() not selectors, and should not use constructors[]
```

### 4. Data Representation
A high-order function version for the constructor:
```py
def rational(n, d):
	def select(name):
		if name == "n":
			return n
		elif name == "d":
			return d
	return select

def numer(x):
	return x("n")

def denom(x):
	return x("d")
```

## Lecture 11 Containers
### 1. Lists
```py
digits = [1, 8, 2, 8]

add([2, 7], mul(digits, 2)) # [2, 7, 1, 8, 2, 8, 1, 8, 2, 8]

1 in digits # True
5 not in digits # True
[1, 8] in digits # False
[1, 2] in [1, [[1, 2]]] # False
```

### 2. For Statement
Sequence unpacking:
```py
pairs = [[1, 2], [2, 2], [4, 4]]
same_count = 0

for x, y in pairs:
	if x == y:
		same_count += 1

print(same_count) # 2
```

### 3. Range
```py
list(range(-2, 2)) # [-2, -1, 0, 1]

range(5, 8) # range(5, 8)
range(4) # range(0, 4)
```
It is also recommended that if you are writing a n-time for loop, use underscore( _ ) instead of letters. Ex. for _ in range(10): do(). 

### 4. List Comprehensions
```py
index = [1, 3, 4]
pairs = [0, 1, 1, 2, 3, 5, 8]

[pairs[i] for i in pairs] #[1, 2, 3]

[i for i in pairs if i > 7] # [8]

def divisors(n):
	return [1] + [i for i in range(2, n) if n % i == 0]

divisors(6) # [1, 2, 3]
``` 

### 5. Strings
```py
exec('curry = lambda f: lambda x: lambda y: f(x, y)')
# similar to eval()

city = "Berkeley"

len(city) # 8
city[3] # 'k'

'here' in "Where's Waldo?" # True. in matches substrings unlike list of numbers
```

### 6. Dictionaries
```py
numerals = {'I': 1, 'V': 5, 'X': 10}

numerals['X'] # 10
numerals[10] # Error

numerals.keys()
numerals.values()
numerals.items()

'X' in numerals # True

# The get() method will return the corresponding value,
# if not found it will return the set value or 0 by default.

numerals.get('X', 0) # 10
numerals.get('a', 0) # 0
```

Dictionary Comprehensions:
```py
{x: x * x for x in range(3)}
# {0: 0, 1: 1, 2: 4}
```

You cannot have two same key, if you do so, it will throw out one, but you can have same values.
```py
{1: 2, 1: 3} # Not allowed, it will throw away {1: 2}
{1: [2, 3]} # One way for the above situation
```

Lists and dictionaries could not be keys.
```py
{[1]: 2} # Error
```

### 7. Sequence Processing
```py
sum([2, 3, 4], 5) # 14
sum(['2', '3', '4']) # Error
sum([[2, 3], [4]], []) # [2, 3, 4]
sum([[2, 3], [4]]) # Error
sum([[[1]], [2]], []) # [[1], [2]]
```
The optional second argument for sum is called start, if not specified, 0, and in the third case above, we specify it as an empty list, thus making the operation valid.

```py
max(range(4), key = lambda x: -2 * x) # 0
``` 
You have to tell the max function that this is a key, otherwise it will be viewed as a value and throw an error out. By adding this additional argument, it will return the **x** that makes the biggest f(x).

```py
all(range(5)) # False
all(range(1,5)) # True
all([]) # True
all([[]]) # False
all([[[]]]) # True, >= 3 are all True
all(['']) # False
```

## Lecture 12 Trees
### 1. Tree Implementation
> A tree has a root label and a list of branches, and each branch is a tree.

> A tree with zero branches is called a leaf.


Implementing the tree abstraction:
```py
def tree(label, branches = []):
	for branch in branches:
		assert is_tree(branch), 'branches must be trees'
	return [label] + list(branches)

def label(tree):
	return tree[0]

def branches(tree):
	return tree[1:]

def is_tree(tree):
	if type(tree) != list or len(tree) < 1:
		return False
	for branch in branches(tree):
		if not is_tree(branch):
			return False
	return True

def is_leaf(tree):
	return not branches(tree)

tree(1)  '''[1]'''
tree(1, 2) # Error
tree(1, tree(2)) # Error
tree(1, [tree(2)]) '''[1, [2]]'''
tree(1, [[2]]) '''[1, [2]]'''
```

### 2. Tree Processing
```py
def fib_tree(n):
	if n <= 1:
		return tree(n)
	else:
		left, right = fib_tree(n-2), fib_tree(n-1)
		return tree(label(left) + label(right), [left, right])

fib_tree(4)
'''
[3, [1, [0], [1]], [2, [1], [1, [0], [1]]]]
          
          3
      1       2
    0   1   1    1
               0    1   
'''

def count_leaves(t):
	if is_leaf(t):
		return 1
	else:
		branch_count = [count_leaves(b) for b in branches(t)]
		return sum(branch_count)

count_leaves(fib_tree(4))  # 5

def leaves(tree):
	"""Return a list containing the leaf labels of tree.

	>>> leaves(fib_tree(4))
	[0, 1, 1, 0, 1] 
	"""
	if is_leaf(tree):
		return [label(tree)]
	else:
		return sum([leaves(i) for i in branches(tree)] , [])

``` 
Creating Trees
```py
def increment_leaves(t):
	"""Return a tree like t but with leaf labels increment."""
	if is_leaf(t):
		return tree(label(t) + 1)
	else:
		bs = [increment_leaves(b) for b in branches]
		return tree(label(t), bs)
"""
       1            1
     0   1   ->   1   2
"""

def increment(t):
	"""Return a tree like t but with all labels incremented."""
	return tree(label(t) + 1, [increment(b) for b in branches])
"""
        1               2
      0   1     ->    1   2
"""
```

### 3. Printing Trees
```py
def print_tree(t, indent = 0):
	print(' ' * indent + str(label(t)))
	for b in branches(t):
		print_tree(b, indent + 1)

print_tree(fib_tree(4))
"""
3
  1
    0
    1
  2
    1
    1
      0
      1
"""
```
For the above tree structure [3, [1, [0], [1]], [2, [1], [1, [0], [1]]]], first 3 is printed with 0 indent and branches is the 1 tree and 2 tree, in the first loop, 1 is printed and indent is 1 and cause branches(1-tree) is not null it goes into the second loop and indent becomes 2, cause 0 and 1 are all leaves so branches do not exist and it ends, such process is same for the 2-tree.


## Lecture 13 Mutable Values
### 1. Objects
```PY
'''
Strings could be represented by ASCII, Unicode, etc. 
'''
ord('A') # 65
hex(ord("A")) # 0x41
'A' < 'a' # True
print("\a") # BeLL Sound


from unicodedata import name, lookup

name("A") # 'LATIN CAPITAL LETTER A'
lookup("SOCCER BALL") # return a picture 
lookup("BABY").encode() # b'\xf0\x9f\x91\xb6' (on Mac)
```

### 2. Mutation Operations
> 1. All names that refer to the same object are affected by a mutation.

> 2. Only objects of mutable types can change like lists $ dicts.

> 3. A function can change the value of any object in its scope.

```py
suits = ['coin', 'string', 'myriad']
original_suits = suits
suits.pop() # 'myriad'
suits.remove('string')
suits.append('cup')
suits.extend(['sword', 'club'])
suits # ['coin', 'cup', 'sword', 'club']
suits[0:3] = ['heart', 'diamond', 'spade']
original_suits # ['heart', 'diamond', 'spade', 'club']
```

### 3. Tuples
Tuples are immutable sequences.
```py
3, 4, 5, 6 # (3, 4, 5, 6)
tuple([2, 3, 4]) # Same as (2, 3, 4)
2, # (2,)
(3, 4) + (5, 6) # (3, 4, 5, 6)
5 in (4, 5) # True
{(1, 2): 3} # Valid
{(1, [2]): 3} # Error

s = ([1, 2], 3)
s[0] = 4 # Error
s[0][0] = 4 # Valid
```

### 4. Mutation
> A compound data objectt has an "identity" in addition to its pieces of which is composed.

```py
a = [10]
b = a 
'''
a and b points to the same object, no matter how we change the content of either a or b, 
the expression a == b is always true.
'''

a = [10]
b = [10]

'''
a and b points to two different objects, only when 
the content of the two are equal will the a == b expression turns out to be a true.
'''
```
**Identity operator a is b** evaluate to True if both a and b evaluate to the same object.
**Equality operator a == b** evaluates to True if both a and b evalaute to equal values.
```py
a = [10]
b = [10]
a == b # True
a is b # False
```
Mutable default arguments are dangerous:
```py
def f(s = []):
	s.append(5)
	return len(s)

f() # 1
f() # 2

def g(s = []):
	s.append(1)
	return s
g() # [1]
a = g()
a # [1, 1]
g() # [1, 1, 1]
a # [1, 1, 1]
```
The created s list in the sub environent can be accessed in any layer of environment.

## Lecture 14 Mutable Functions
### 1. Non-Local Assignment

> **nonlocal**: Future assignments to that name change its pre-existing binding in the first non-local frame(could not be global) to the current environment in which that name is bound.

```py
def make_withdraw(balance):
	def withdraw(amount):
		nonlocal balance
		if amount > balance:
			return 'Insufficient funds'
		balance = balance - amount
		return balance
	return withdraw

my_account = make_withdraw(100)
my_account(40) # 60
my_account(70) # 'Insufficient funds'
```
We can also implement this using mutable values without a nonlocal statement.
```py
def make_withdraw(balance):
	b = [balance]
	def withdraw(amount):
		if amount > b[0]:
			return 'Insufficient funds'
		b[0] = b[0] - amount
		return b[0]
	return withdraw
```

### 2. Multiple Mutable Functions
```py
a = make_withdraw(100)
b = make_withdraw(100)
a is b # F
a == b # F
```
> Expressions are referentially transparent if substituting an expression with its value does not change the meaning of a program.

Mutable values will violate referential transparency:
```py
def f(x):
	x = 0
	def g(y):
		nonlocal x
		x += 1
		return x + y
b = f(100)
b(0) + b(1) # 1 + 0 + 2 + 1 = 4
```

### 3. Function Examples
```py
def oski(bear):
	def cal(berk):
		nonlocal bear
		if bear(berk) == 0:
			return [berk + 1, berk - 1]
		bear = lambda ley : berk-ley
		return [berk, cal(berk)]
	return cal(2)
oski(abs)
```
First abs is passed to cal as nonlocal, abs(2) != 0 so bear becomes a new function 2-x and return [2, cal(2)]. cal(2) is 0 now and return[3, 1] so we got [2, [3, 1]].

## Lecture 15 Objects
### 1. Object-Oriented Programming
```py
class Account:
	def __init__(self, account_holder):
		self.holder = account_holder
		self.balance = 0

	def deposit(self, amount):
		self.balance = self.balance + amount
		return self.balance

	def withdraw(self, amount):
		if amount > self.balance:
			return 'Insufficient funds'
		self.balance = self.balance - amount
		return self.balance 
```

### 2. Attributes
```py
a = Account("a")
a.balance / getattr(a, "balance")  # 0
hasattr(a, "balance") # T
```
> Object + Function = Bound Method

```py
type(Account.deposit) # <class 'function'>
type(a.deposit) #  <class 'method'>

Account.deposit(a, 1000) # It is function and a is 'self'
a.deposit(1000) # It is a method, works same as the previous call
```

```py
class demo:
	a = 1
	def __init__(self, b):
		self.b = b
a = demo("0")
a.a # 1  
```
Here a = 1 is a class attribute not an instance attribute, when a.a is called the .a started to find attributes in object a but not found there so then it turns to class demo and finds a = 1 there so 1 is therefore returned.

## Lecture 16 Inheritance
### 1. Attribute Assignment
> If the object is an instance, then assignment sets an instance attribute

> If the object is a class, then assignment sets a class attribute

```py
class Account:
	interest = 0.02
	def __init__(self, account_holder):
		self.holder = account_holder
		self.balance = 0

tom = Account("tom")
tom.interest = 0.08 # it will not look for interest in the account class and only adds an attribute named interest

Account.interest = 0.04 # there will change the class attribute

jim = Account("jim")
jim.interest # 0.04
tom.interest # 0.08
Account.interest = 0.05
jim.interest # 0.05
tom.interest # 0.08


```

### 2. Inheritance
> class < name > (< base class >):

```py
class CheckingAccount(Account):
	withdraw_fee = 1
	interest = 0.01
	def withdraw(self, amount):
		return Account.withdraw(self, amount + self.withdraw_fee)
```
To look up a name in a class, it will first looks in the current class, if not found, it will look up the name in the base class  

```py
ch = CheckingAccount("Tom") # Calls Account.__init__
ch.interest # Found in CheckingAccount
ch.deposit(20) # Found in Account
ch.withdraw(5) # Found in CheckingAccount
```

### 3. Object-Oriented Design
> Inheritance is is-a relationship.

> Composition is has-a relationship.

Cause bank is not an account or vice versa, we could not use inheritance when constructing the bank class.
```py
class Bank:
	def __init__(self):
		self.accounts = []

	def open_account(self, holder, amount, kind = Account):
		account = kind(holder)
		account.deposit(amount)
		self.accounts.append(account)
		return account
		'''
		Here the Bank object contains a lot of Account objects.
		'''

	def pay_interest(self):
		for a in self.accounts:
			a.deposit(a.balance * a.interest)

	def too_big_to_fail(self):
		return len(self.accounts) > 1
```

### 4. Attributes Lookup Practice
```py
class A:
	z = -1
	def f(self, x):
		return B(x-1)

class B(A):
	n = 4
	def __init__(self, y):
		if y:
			self.z = self.f(y)
		else:
			self.z = C(y+1)

class C(B):
	def f(self, x):
		return x

a = A()
b = B(1)
b.n = 5

C(2).n # 4
C(2).z # 2 
'''Note: when self.f(2) is called it will start looking for f in C not directly from B. It is because self is C now'''

a.z == C.z # T

a.z == b.z # F

b.z.z.z # 1
"""
b.z is B(0), b.z.z is C(1), b.z.z.z is 1
"""
```
### 5. Mutiple Inheritance
```py
class SavingsAccount(Account):
	deposit_fee = 2
	def deposit(self, amount):
		return Account.deposit(self, amount - self.deposit_fee)
class AsSeenOnTVAccount(CheckingAccount, SavingsAccount):
	def __init__(self, account_holder):
		self.holder = account_holder
		self.balance = 1
```
Such an account inherited from two classes could have an interest rate which was defined in the grandfather class, and 1 fee for withdrawals, 2 fee for deposits and 1 free dollar when you open the account.

```py
such_a_deal = AsSeenOnTVAccount("J")
such_a_deal.balance # 1
such_a_deal.deposit(20) # 19 = 1 + 20 - 2
such_a_deal.withdraw(5) # 13
```

***Ex. 1*** 
```py
class bird():
	def quack(self):
		print('Gugu')

class duck(bird):
	def quack(self):
		print('Gaga')

a = duck()
a.quack() # Gaga
duck.quack(a) # Gaga
bird.quack(a) # Gugu 
"""Note the difference here"""
```


## Lecture 17 Representation
### 1. String Representation
> In Python, all objects produce 2 String representations, str() is legible to humans, repr() is legible to Python interpreter.

```py
repr(10) # '10'
repr(min) # '<built-in function min>'
str(min) # '<built-in function min>'

from fractions import Fraction

half = Fraction(1, 2)
repr(half) # 'Fraction(1, 2)'
str(half) # '1/2'
```
The result of str() is just print() plus quotes.

```py
eval(repr(half)) # Fraction(1, 2)
eval(str(half))  # 0.5

s = "hello"
s # 'hello'
repr(s) # "'hello'"
print(repr(s)) # 'hello'
print(str(s)) # hello
str(s) # 'hello'
eval(repr(s)) # 'hello'
```
repr() will add an additional pair of quotes on the passed argument, while str() will not do so if it recognize the argument is already a string.

### 2. Polymorphic Functions
> Polymorphic function: A function that applies to many different forms of data such as str() and repr()

repr() in invokes a zero-argument method __ repr __ on its argument, likewise, str() invokes __ str __ :
```py
half.__repr__() # Fraction(1, 2)
half.__str__() # '1/2'
```

An instance attribute called __ repr __ is ignored, only class attributes are found
```py
def repr():
	return type(x).__repr__(x)
```

An instance attribute called __ str __ is ignored, if no __ str__ attribute is found, uses repr string.
```py
'''Figure out the following interactive output in different scenarios'''
oski = Bear()
print(oski)
print(str(oski))
print(repr(oski))
print(oski.__str__())
print(oski.__repr__())
-------------------------
class Bear:
	def __repr__(self):
		return "Bear()"
"""
Bear()
Bear()
Bear()
Bear()
Bear()
"""
---------------------------
class Bear:
	def __repr__(self):
		return "Bear()"

	def __str__(self):
		return "A bear"

"""
a bear
a bear
Bear()
a bear
Bear()
"""
--------------------------
class Bear:
	def __init__(self):
		self.__repr__ = lambda: "oski"
		self.__str__ = lambda: 'this bear'

	def __repr__(self):
		return "Bear()"

	def __str__(self):
		return "A bear"

"""
a bear
a bear
Bear()
this bear
oski
"""
------------------------------
class Bear:
	def __init__(self):
		self.__repr__ = lambda: "oski"
		self.__str__ = lambda: 'this bear'

	def __repr__(self):
		return "Bear()"

	def __str__(self):
		return "A bear"

def repr(x):
	return type(x).__repr__(x)

def str(x):
	t = type(x)
	if hasattr(t, "__str__"):
		return t.__str__(x)
	else:
		return repr(x)
'''
a bear
a bear
Bear()
this bear
oski
'''
```

### 3. Interfaces
> Message passing: Objects interact by looking up attributes on each other (passing messages)

> A shared message (attribute name) that elicits similar behavior from different object classes is a powerful method of abstraction

> An **interfacte** is a set of shared messages, along with a specification of what they mean.

Example:
```py
class Ratio:
	def __init__(self, n, d):
		self.numer = n
		self.denom = d

	def __repr__(self):
		return 'Ratio({0}, {1})'.format(self.numer, self.denom)

	def __str__(self):
		return 'Ratio({0}/{1})'.format(self.numer, self.denom)

half = Ratio(1, 2)
print(half) # 1/2
half # Ratio(1, 2)
```

### 4. Special Method Names in Python
Special built-in names always start and end with two underscores 

__ init __ , __ repr __ , __ add __ (add one object to another), __ bool __ (convert an object to T or F), __ float __ (convert an object to float)

```py
zero, one, two = 0, 1, 2
one.__add__(two) # 3
zero.__bool__(), one.__bool__() # (False, True)

class Ratio:
	def __init__(self, n, d):
		self.numer = n
		self.denom = d

	def __repr__(self):
		return 'Ratio({0}, {1})'.format(self.numer, self.denom)

	def __str__(self):
		return 'Ratio({0}/{1})'.format(self.numer, self.denom)

	def __add__(self, other):
		n = self.numer * other.denom + self.denom * other.numer
		d = self.denom * other.denom
		g = gcd(n, d) 
		return Ratio(n // g, d // g)

Ratio(1, 6) + Ratio(1, 3)
# Ratio(1, 2)  # now the + operand is recognized as __add__()

Radio(1, 3) + 1 # Error
'''
if we add other line in the definition:
	def __add__(self, other):
		if isinstance(other, int):
			n = self.numer + self.denom * other
			d = self.denom
		elif isintance(self, Ration):
			PREVIOUS CONTENT
'''

Ratio(1, 3) + 1 # Ratio(4, 3)

1 + Ratio(1, 3) # Error
'''
It is because the operand + and __add__() is naturally a left plus,
 which means to add left to right or left.__add__(right)

If we add other definition:
	def _add__(self, other):

		...(As Before)

	__radd__ = __add__ 
'''

1 + Ratio(1, 3) # Ratio(4, 3)

'''adding float:
	def __add__(self, other):
		... As Before

		elif instance(other, float):
			return float(self) + other

		... AS Before

	__radd__ = __add__  # which means right add

	def __float__(self):
		return self.numer / self.denom
'''

0.2 + Ratio(1, 3)
# 0.533333333333
```
The isinstance() conditional statments are called type dispatching, the float adding is called type coercion which means convert one type to another to make operation possible.

## Lecture 18 Growth
### 1. Measuring Efficiency
```py
def fib(n):
	if n == 0 or n == 1:
		return n
	else:
		return fib(n-2) + fib(n-1)

def count(f): # decorator
	def counted(n):
		counted.call_count += 1
		return f(n)
	counted.call_count = 0
	return counted
'''
this creates an attribute call_count for counted
'''

fib = count(fib) ''' Note orginal fib disappears, this is a rather tricky point
Cause when f(5) calls fib(3) & fib(4) fib is the new fib not the orginial one'''
fib(5).call_count # Error fib(5) is int
fib(5) # 5
fib.call_count # 15
```
For built-in types or functions?? we cannot create an attribute as above such as int or min function, but for created function we could do this.

### 2. Memorization
```py
def memo(f):     # another decorator
	cache = {}
	def memorized(n):
		if n not in cache:
			cache[n] = f(n)
		return cache[n]
	return memorized


fib = count(fib)
count_fib = fib
fib = memo(fib)
fib = count(fib)
fib(30) 
fib.call_count # 59 without memo used to be > 2,000,000
count_fib.call_count # 31 0-30 is called to fib the rest found in cache
```
??????????????????What the hell does all these assign mean? I could only understand 
```py
@count
@memo
def fib():
	...
```

### 3. Space
> Active Environments: 

> 1. Environments for any function calls currently being evaluated (call but hasn't returned)

> 2. Parent environments of functions named in active environments (define a function in another not global). For example in recursion when u reach base case the system would release memory(returned) and other running branches are still using memory until returned.

```py
def count_frames(f):
	def counted(n):
		counted.open_count += 1
		if counted.open_count > counted.max_count:
			counted.max_count = counted.open_count
		result = f(n)
		counted.open_count -= 1
		return result
	counted.open_count = 0
	counted.max_count = 0
	return counted

fib = count_frames(fib)
fib(20) # 6765
fib.open_count # 0
fib.max_count # 20  
```
In fib, the max_count would be the length of the longest recursive path, because there would constantly have functions returned or are not called yet.

### 4. Time
```PY
def count(f):
	def counted(*args):
		counted.all_count += 1
		return f(*args)
	counted.call_count = 0
	return counted

def divides(k, n):
	return n % k == 0

def factors(n):
	total = 0
	for k in range(1, n+1):
		if divides(k, n):
			total += 1
	return total

divides = count(divides)
factors(576) # 21
divides.call_count # 576

def factors_fast(n):
	total = 0
	sqrt_n = sqrt(n)
	k = 1
	while k < sqrt_n:
		if divides(k, n):
			total += 2
		k += 1
	if k * k == n:
		total += 1
	return total

divides = count(divides)
factors_fast(576) # 21
divides.call_count # 23
```

### 5. Order of Growth
name | Time | Space
-|-|-
factors | O(n) | O(1) (means fixed number of names used)
factors_fast | O(&radic;n) | O(1)

### 6. Exponentiation
```py
def exp(b, n):
	if n == 0:
		return 1
	else:
		return b * exp(b, n-1)

def exp_fast(b, n):
	if n == 0:
		return 1
	elif n % 2 == 0:
		return square(exp_fast(n // 2))
	else:
		return b * exp_fast(b, n-1)

```

name | Time | Space
-|-|-
exp | O(n) | O(n)
exp_fast | O(log n) | O(log n)


For the space complexity of exp(), cause we have to store every intermediary expression until we reach n = 0 and that's a difference between recursion and iteration.

### 7. Compare Orders of Growth
Nothing special

## Lecture 19 Composition
### 1. Linked List
```py
class Link:
	empty = ()
	def __init__(self, first, rest = empty):
		assert rest is Link.empty or isinstance(rest, Link)
		self.first = first
		self.rest = rest

s = Link(3, Link(4, Link(5)))
s # Link(3, Link(4, Link(5)))
s.first # 3
s.rest # Link(4, Link(5))
s.rest.first # 4
s.rest.rest.first # 5
s.rest.rest.rest is empty # T

s.rest.first = 7
s # Link(3, Link(7, Link(5)))		
```

### 2. Property Methods
> The @property decorator on a method designates that it will be called whenever it is looked up on an instance

```py
class Link:
	...
	@property
	def second(self):
		return self.rest.first
	...

s.second # 7 
'''with property decorator we do not have to call .second()'''
s.rest.second # 5

class Link:
	... #Included all above added section 
	@second.setter
	def second(self, value):
		self.rest.first = value
	...

s.second = 6
s # Link(3, Link(6, Link(5)))
'''Note setter decorator could only be defined after property decorator'''
```

### 3. Tree Class
```py
class Tree:
	def __init__(self, label, branches = []):
		self.label = label
		for branch in branches:
			assert isinstance(branch, Tree)
		self.branches = list(branches)

	def is_leaf(self):
		return not self.branches

def fib_tree(n):
	if n == 0 or n == 1:
		return Tree(n)
	else:
		left = fib_tree(n-2)
		right = fib_tree(n-1)
		fib_n = left.label + right.label
		return Tree(fib_n, [left, right])

def leaves(tree):
	if tree.is_leaf():
		return [tree.label]
	else:
		return sum([leaves(b) for b in tree.branches], [])
```

### 4. Tree Mutation
Example: prune a fib_tree in order to remove repetition
```py
def prune_repeats(t, seen):
	t.branches = [b for b in t.branches if b not in seen]
	seen.append(t)
	for b in t.branches:
		prune_repeats(b, seen)

fib_tree = memo(fib_tree) #without memo, no repeats
t = fib_tree(8)
prune_repeats(t, [])
print(t) """
21
  8
    3
      1
        0
        1
      2
    5
  13
"""
``` 
??????????????

## Lecture 20 Ordered Sets
### 1. Sets
```py
s = {3, 2, 1, 4, 4}
s # {1, 2, 3, 4}
s.union({1, 5}) # {1. 2, 3, 4, 5} s not changed
s.intersection({3, 4, 5, 6}) # {1, 2, 3, 4} s not changed
s.add("1") # s changed but not printed 
s # {1, 2, 3, 4, "1"}
```
Sets cannot have list as elements.

### 2. Implementation of Sets:
1. Sets as unordered linked list:
```py
def filter_link(f, s): # Return e in s if f(e) is true
	if s is Link.empty:
		return s
	else:
		filtered = filter_link(f, s.rest)
		if f(s.first):
			return Link(s.first, filtered)
		else:
			return filtered

def extend_link(s, t): # Merge two linked list
	if empty(s):
		return t
	else:
		return Link(s.first, extend_link(s.rest, t))
---------------------------------------
def empty(s):
	return s is Link.empty

def contains(s, v):
	if empty(s):
		return False
	elif s.first == v:
		return True
	else:
		return contains(s.rest, v)

def adjoin(s, v): # like add but not change s just print it
	if contains(s, v):
		return s
	else:
		return Link(v, s)

def intersect(set1, set2):
	in_set2 = lambda v: contains(set2, v)
	return filter_link(in_set2, set1)

def union(set1, set2):
	not_in_set2 = lambda v: not contains(set2, v)
	set1_not_set2 = filter_link(not_in_set2, set1)
	return extend_link(set1_not_set2, set2)
```
T(empty) is O(1)

T(contains), T(adjoin) is O(n)

T(intersect), T(union) is O(n<sup>2</sup>)

2. Sets as ordered linked lists
```py
def intersect(set1, set2):
	if empty(set1) or empty(set2):
		return Link.empty
	else:
		e1, e2 = set1.first, set2.first
		if e1 == e2:
			return Link(e1, intersect(set1.rest, set2.rest))
		elif e1 < e2:
			return intersect(set1.rest, set2)
		elif e2 < e1:
			return intersect(set1, set2.rest)

def union(set1, set2):
	if empty(set1):
		return set2
	elif empty(set2):
		return set1
	else:
		e1, e2 = set1.first, set2.first
		if e1 == e2:
			return Link(e1, union(set1.rest, set2.rest))
		elif e1 < e2:
			return Link(e1, union(set1.rest, set2))
		elif e2 < e1:
			return Link(e2, union(set1, set2.rest))
```
T(intersect), T(union) is O(n)

### 3. Set Mutation
Adding to an ordered list
```py
def add(s, v):
	assert not empty(s), "cannot add to an empty set"????
	if s.first > v:
		s.first, s.rest = v, s 
	"""John's answer Link(s.first, s.rest) not s??"""
	elif s.first < v and empty(s.rest):
		s.rest = Link(v) """John's answer Link(v, s.rest) ???"""
	elif s.first < v:
		add(s.rest, v)
	return s
```
**CANNOT UNDERSTAND ABOVE ASSIGNMENT???**

## Lecture 21 Tree Sets
### 1. Binary Trees
```py
class BTree(Tree):
	empty = Tree(None)
	def __init__(self, label, left = empty, right = empty):
		Tree.__init__(self, label, [left, right])

	@property
	def left(self):
		return self.branches[0]

	@property
	def right(self):
		return self.branches[1]

	def is_leaf(self):
		return [self.left, self.right] == [BTree.empty] * 2

t = BTree(3, BTree(1), BTree(5))
t.left # BTree(1)
t.right # BTree(5)
t.left.label # 1

def fib_tree(n):
	if n == 0 or n == 1:
		return BTree(n)
	else:
		left = fib_tree(n-2)
		right = fib_tree(n-1)
		fib_n = left.label + right.label
		return BTree(fib_n, left, right)

def contents(t):
	if t is BTree.empty:
		return []
	else:
		return contents(t.left) + [t.label] + contents(t.right)

contents(fib_tree(3))
# [1, 2, 0, 1, 1]
```

### 2. Binary Search Trees
The best binary search tree is balanced search tree which means the left branch has about the same number of labels as the right branch

```py
def balanced_bst(s):
	if not s:
		return BTree.empty
	else:
		min = len(s) // 2
		left = balanced_bst(s[:mid])
		right = balanced_bst(s[mid+1:])
		return BTree(s[mid], left, right)

def largest(t):
	if t.right is BTree.empty:
		return t.label
	else:
		return largest(t.right)

def second_largest(t):
	if t.is_leaf():
		return None                       5      
	elif t.right is BTree.empty: ->    3     9 <- largest  
		return largest(t.left)   ->  1     7
	elif t.right.is_leaf():                  8 <- second_largest
		return t.label
	else:
		return second_largest(t.right)
```

### 3. Sets as Binary Search Trees
***Optimize contains:***
```py
def contains(s, v):
	if s is BTree.empty:
		return False
	elif s.root == v:
		return True
	elif s.root < v:
		return contains(s.right, v)
	else:
		return contains(s.left, v)
```
Time complexity depends on the height of the tree, if we have a balanced tree, T(contains) is O(log n).

***Optimize adjoin:***
```py
def adjoin(s, v):
	if s is BTree.empty:
		return BTree(v)
	elif s.root == v:
		return s
	elif s.root < v:
		return BTree(s.root, s.left, adjoin(s.right, v))
	else:
		return BTree(s.root, adjoin(s.left, v), s.right)
```
**????????Where does the root come from??Not defined before**
Okay, just the same thing

## Lecture 22 Data Examples
### 1. Lists
```py
s = [2, 3]
t = [5, 6]
s.extend(t)
t[1] = 0
s # [2, 3, 5, 6]
#Cause extend is copy elements into not copy the entire object, so change the content of t will not affect s

a = s + [t]  # None s binding is lost but t is maintained
b = a[1:]
a[1] = 9
b[1][1] = 0
s # [2, 3]
t # [5, 0]
a # [2, 9, [5, 0]]
b # [3, [5, 0]]
"""a[2] and b[1] and t all point to the same object"""

t = list(s)
s[1] = 0
s -> [2, 0]
t -> [2, 3]

s[0:0] = t / s[:0] = t  """quirk, insert t before as elements not entire, cause left is []??"""
s[3:] = t   """for s=[2,3], call s[2:] or s[3:] or s[100:] = t is all valid but s[2] = t error"""
t[1] = 0
s -> [5,6,2,5,6]
t -> [5,0]
```
Therefore:

1. for append, the binding loses if t changes to other types

2. for extend, the binding loses if s.extend(t) but s.extend([t]) acts same as append 

3. for addition and slicing(appear right to '='), it creates new lists containing existing elements(or just cracking one []), the way addtion loses binding is same as extend

4. for list() function, the binding loses if list(t) but list([t]) acts same as append

5. for slicing assignment(appear left to '='), the binding loses just like extend(no additional [] is used)

```py
t= [1, 2, 3]
t[1:3] = [t]
t.extend(t)
t -> [1,[1,2,3],1,[1,2,3]]

t = [[1, 2], [3, 4]]
t[0].append(t[1:2])
t -> [[1, 2, [[3, 4]]], [3, 4]]
"""Note: Slicing will create a new list[], so it's [[3,4]]"""
```

### 2. Objects
```py
class Worker:
	greeting = 'Sir'
	def __init__(self):
		self.elf = Worker
	def work(self):
		return self.greeting + ', I work'
	def __repr__(self):
		return Bourgeoisie.greeting

class Bourgeoisie(Worker):
	greeting = 'Peon'
	def work(self):
		print(Worker.work(self))
		return 'I gather wealth'

jack = Worker()
john = Bourgeoisie()
jack.greeting = 'Maam'

Worker().work() -> 'Sir, I work'
"""Note, Worker() is an object but Worker is not"""

jack -> Peon / Worker() -> Peon  ?????
"""Note, object.attribute or object().attribute is both valid. 
Quote is removed cause its print????"""

jack.work() ->  "Maam, I work"
john.work() ->  Peon, I work  ????????
				"I gather wealth"
john.elf.work(john) -> "Peon, I work"
```
Worker.work(object) force object to use father method but when the object enconnters a self argument, it will start looking for its own attributes in child class**???**

### 3. Mutatble Linked Lists
```py
s = Link(1, Link(2, Link(3)))
s.first = 5
t = s.rest
t.rest = s
s.first -> 5
s.rest.rest.rest.rest.rest.first -> 5
```
First s -> 1-2-3, then s -> 5-2-3, then t points to the second number in s

then t.rest = s, so 3 is removed, the -> pointer after 2 then binds to 5, and s.rest = t, t.rest = s, it creates a 5 - 2 cycle. **??**

### 4. Trees
```py
def decode(signals, tree):
	"""
	>>> t = morse(abcde) ## abcde is a dict mapping letter to code
	>>> [decode(s, t) for s in [...]] ##[...] is codes separated by comma
	[...]  # [...] is decoded letters separated by comma 
	"""
	for signal in signals:
		tree = [b for b in tree.branches if b.entry == signal][0]
	leaves = [b for b in tree.branches if not b.branches]
	assert = len(leaves) == 1
	return leaves[0].entry

def morse(code):
	root = Tree(None)
	for letter, signals in sorted(code.items()):
		tree = root
		for signal in signals:
			match = [b for b in tree.branches is b.entry == signal]
			if match:
				assert len(match) == 1
				tree = match[0]
			else:
				branch = Tree(signal)
				tree.branches.append(branch)
				tree = branch
		tree.branches.append(Tree(letter))
		return root 
```

## Lecture 23 Scheme
### 1. Scheme Fundamentals
```scheme
(quotient 10 2) -> 5
(quotient (+ 8 7) 5) -> 5

(+ (* 3
	(+ (* 2 4)
	   (+ 3 5)))
   (+ (- 10 7))
       6))
-> 57

(+) -> 0
(* 1 2 3 4) -> 24
(*) -> 1

+ -> #[+]
(number? 3) -> #t
(number? +) -> #f
(zero? 2) -> #f
(zero? 0) -> #t
(interger? 2.2) -> #f
```
Scheme doesn't care about indents and spacing.

### 2. Special Forms
> A combination that is not a call expression is a special form
 
```scheme
1. If expression: (if <predicate> <consequent> <alternative>)

2. And and or: (and <e1> ... <en> ), (or <e1> ... <en>)

3. Binding symbols (define <symbol> <expression>)

(define pi 3.14)
(* pi 2) -> 6.28

4. New precedures: (define (<symbol> <formal parameters>) <body>)

(define (abs x) (if (< x 0) (- x) x))
(abs -3) -> 3

(define (square x) (* x x))
(square 3) -> 9

(define (average x y) (/ (+ x y) 2))
(average 10 6) -> 8

(define (sqrt x)
	(define (update guess)
		(if (= (square guess) x)
			guess
			(update (average guess (/ x guess)))))
	(update 1))
(sqrt 256) -> 16
```
For sqrt(), it defines a function inside named update() that takes in an argument guess, if guess<sup>2</sup> == x then we return guess, otherwise, we update the guess to
[guess + (x / guess)] / 2 which is the midpoint between guess and x and we return update(1).

So when we call sqrt(256), we get, 128.5->64.xxxx->32.xxxx -> 16.xxxx and finally converge at 16.0

The if/elif/else in scheme uses forms as follows:
```scheme
(cond
	((> x 0) 'positive)
    ((< x 0) 'negative)
    (else 'zero))
```
### 3. Lambda Expressions
```scheme
(lambda (<formal parameters>) <body>)

(define (plus4 x) (+ x 4))
(define plus4 (lambda (x) (+ x 4)))
```
The above two expressions are equivalent.
```
((lambda (x y z) (+ x y (square z))) 1 2 3) -> 12
```

### 4. Pairs and Lists
> Paris and lists in Scheme are based on linked lists.

· cons： Two argument procedure that creats a pair


· car ： Procedure that returns the first element of a pair  


· cdr ： Procedure that returns the second element of a pair

· nil ： The empty list

A dotted list has some value for the second element that of the last pair that is not a list.
```scheme
(cons 1 (cons 2 nil)) -> (1 2)

(define y (cons 1 (cons 2 nil)))
(define x (cons 1 2))
x -> (1 . 2) ; not a well-formed list cause the second element is not a list

(car x) -> 1   (cdr x) -> 2
(cdr y) -> 1   (cdr y) -> (2)  (cdr (cdr y)) -> ()
;Note the difference here

(cons 1 (cons 2 (cons 3 (cons 4 nil)))) -> (1 2 3 4)

(cons 2) -> (2)
(cons 1 (cons 2 3)) -> (1 2 . 3)
(cons (cons 1 (cons 2 nil)) (cons 3 (cons 4 nil))) -> ((1 2) 3 4)

(pair? (cons 1 2)) -> True
(pair? nil) -> False
(null? nil) -> True
```
Lists can aslo be directly declared by list:
```scheme
(define x (list 1 2 3 4))
(pair? x) -> True
(car x) -> 1
(cdr x) -> (2 3 4)
(cdr (cdr x)) -> (3 4) 
(car (cdr (cdr x))) -> 3

(define y (cons 1 2))
y -> (1 . 2)
(list (car y) (cdr y)) -> (1 2)
(cons (car y) (cons (cdr y) nil)) -> (1 2)

(define (len s)
	(if (null? s)
		0
		(+ 1 (len (cdr s)))
		)
	)

(len (list 1 2 3 4)) -> 4
(len (list 1 (list 2 3) 4)) -> 3
```

### 5. Symbolic Programming
Quatation is used to refer to symbols in Lisp.
```scheme
(define a 1)
(define b 2)
(list a b) -> (1 2)
(list 'a 'b) -> (a b)

(car '(a b c)) -> a
(cdr '(a b c)) -> (b c)
(car (1 2 3)) -> Error
(car '(1 2 3)) -> 1
```
Dots can be used in a quoted list to specify the second element of the final pair
```scheme
(cdr (cdr '(1 2 . 3))) -> 3   ; 1 -> 23
'(1 2 . 3) -> (1 2 . 3)
'(1 2 . (3 4)) -> (1 2 3 4)
'(1 2 3 . nil) -> (1 2 3)
'(cons 1 2) -> (cons 1 2); Argument to quote is not evaluated
(integer? (car '(1 2 3))) #t 
```
Therefore, a pair could appear after a dot and the entire list is still a valid well-formed linked list.

Dot will remove dot/parentheses when possible, unlike (1 2 . 3): [1] -> [2 3] the dot cannot be removed, in (1 . (2 3 4)):[1 [[2]->[3]->[4]]] is actually a well-formed linked list 'cause after the dot is not the last element:
```scheme
'(1 . (2 3 4))  ->  (1 2 3 4)
(cons 1 '(list 2 3)) -> (1 list 2 3) 
(cons (list 2 (cons 3 4)) nil) -> ((2 (3 . 4)))
(car(cdr '(1 (2 3)))) -> (2 3)
(cdr '(1 (2 3))) -> ((2 3))
(equal? '(1 . ((2 . 3))) (cons 1 (cons (cons 2 3) nil))) -> #t; both are (1 (2 . 3))
(equal? '(1 . (2 . 3)) (cons 1 (cons (cons 2 3) nil))) -> #f; first is (1 2 . 3)
'(cons 4 (cons (cons 6 8) ())) -> (cons 4 (cons (cons 6 8) ()))

(cdr '((1 2) . (3 4 . (5)))) -> (3 4 5) ; (5) is different from 5
```


## Lecture 25 Exceptions (Python)
### 1. Handing Errors
Exceptions can be handled by the program, preventing the interpreter from halting, unhandled exceptions will cause Python to halt execution and print a stack trace.

Exceptions are **objects**, they have classes with contructors. They enable *non-local* continuations of control:

If **f** calls **g** and **g** calls **h**, exceptions can shift from control from **h** to **f** without waiting for **g** to return.

### 2. Raise Exceptions
1. Assert Statements:
```py
assert <expression>, <string>
'''python -o will ignore all assertions'''
```

2. Raise Statement:
```py
raise <expression> 
'''<expression> must evaluate to a subclass of BaseException'''
TypeError  # A function was passed wrong argument
NameError  # A name was not found
KeyError   # A key wasn't found in a dictionary
RuntimeError # Catch-all for troubles during interpretation
SyntaxError

raise TypeError("Bad args")
hello -> NameError
{}['a'] -> KeyError
```

### 3. Try Statement
```py
try:
	<try suite>
except <exception class> as <name>:
	<except suite>

try:
	x = 1 / 0
except ZeroDivisionError as e:
	print('handling a', type(e))
	x = 0
'''handled a <class zerodivisionerror>'''

def invert(x):
	y = 1 / x
	print('Never printed if x == 0')
	return y
invert(0) -> throw a ZeroDivisionError

def inverse_safe(x):
	try:
		return invert(0)
	except ZeroDivisionError as e:
		print('handled', e)
		return 0
inverse(0)
'''
handled division by zero
0
'''

inverse_safe(1/0) # throw a ZeroDivisionError and inverse_safe() is not called

try:
	inverse_safe(0)
except ZeroDivisionError as e:
	print("handled")
'''
handled division by zero
0
'''

inverrrrt_safe(1/0) # throw a NameError and 1/0 is not executed
```

### 4. Reduce
Reducing a sequence to a value:
```py
def reduce(f, s, initial):
	"""Combine elements of s pairwise using f, starting with initial
	>>> reduce(mul, [2, 4, 8], 1) # 1*2*4*8
	64
	"""
	if s:
		first, rest = s[0], s[1:]
		return reduce(f, rest, f(initial, first))
	else:
		return initial

def divide_all(n, ds):
	try:
		return reduce(truediv, ds, n)
	except ZeroDivisionError:
		return float('inf')
divide_all(1024, [2,4,8]) -> 16.0
divide_all(1024, [0]) -> inf
```
**???? Why the truediv exception would be passed to divide_all???**

Okay, it seems that the except is used to receive the exception if the except reception matches the set exception, it will continue running, otherwise, like if in the above program we write except NameError: then the function will not return inf but stop at reduce and return a divided by zero error.

## Lecture 26 Calculators
### 1. Interpreters
Donno what to record 

### 2. Parsing 
Skipped

### 3. Calculator
Doono what to record

### 4. Evaluation
Skipped

**Skipped All the Rest**


## Lecture 27 Interperters
 ***Chapter Skipped***

## Lecture 28 Tail Calls
***Chapter Skipped***
```scheme
(define (map f link)
	(define (map-reverse link m)
		(if (null? link)
			m
		(map-reverse (cdr link) (cons (f (car link)) m))
		)
	)
	(reverse (map-reverse link nil))
)

(define (reverse link)
	(define (reverse-iter link r)
		(if (null? link)
			r
			(reverse-iter (cdr link) (cons (car link) r))
		)
	)
)

```

## Lecture 29 Macros
***Chapter Skipped***

## Lecture 30 Data Processing
### 1. Iterators
```py
iter(iterable) '''Return an iterator over the elements of an iterable value'''
next(iterator) '''Return the next element in an iterator'''

s = [3, 4, 5]
t = iter(s)
next(t) -> 3
next(t) -> 4
u = iter(s) """t and u are independent"""
next(u) -> 3
next(t) -> 5

d = {'one':1, "two":2, "three":3}
k = iter(d)
next(k) -> 'one'
next(k) -> 'three'
"""Keys are values are iterated over in an arbitrary order which is non-random, 
depending on many factors, two same dict iterator may have different behavior"""
```
However, it we **do not call any modifications** on d, we call another new iter(d), it will have the same order, even if we call a iter(d.values()) it will follow the first-time order.

```py
s = [[1, 2], 3, 4, 5]
next(s) -> Error
t = iter(s)
next(t) -> [1, 2]
next(t) -> 3
list(t) -> [4, 5]
next(t) -> StopIteration

v = iter(d.values())
next(v) ->　1
next(v) -> 3
d.pop("three") -> 3
next(v) -> RuntimeError: dictionary changed size during iteration
```

### 2. For Statements
Dunno what to record

### 3. Built-in Iterator Functions
Many built-in Python sequence operations return iterators that compute results lazily.
```py
map(func, iterable) '''Iterate over func(x) for x in iterable'''
filter(func, iterable) '''Iterate over x if func(x)'''
zip(iter1, iter2) '''Iterabte over co-indexed (x,y) pairs'''
reversed(sequence) '''Iterabte over x in reverse order'''

```
To view the contents of an iterator, use list(iter) or tuple(iter) or sorted(iter).

```py
m = map(double, range(3,7))
f = lambda y: y >= 10
t = filter(f, m) '''???????m is not a iterator'''
next(t)
'''Result:
6
8
10
10
'''
next(t) 
'''
12
12
'''
list(t) -> []

list(filter(f, map(double, range(3, 7))))
'''
6
8
10
12
[10, 12]
'''
```
**????? iterable ??? iterator????**
```py
t = [1, 2, 1]
reversed(t) == t -> False
list(reversed(t)) == t -> True

```

### 4. Generators
```py
def plus_minus(x):
	yield x
	yield -x

t = plus_minus(3)
next(t) -> 3
next(t) -> -3

def even(start, end):
	even = start + start % 2
	while even < end:
		yield even
		even += 2

t = evens(2, 10)
next(t) -> 2
...
next(t) -> 8
next(t) -> StopIteration

list(evens(1, 10)) -> [2, 4, 6, 8]

type([i for i in range(10)]) -> <class 'list'>
type((i for i in range(10))) -> <class 'generator'>
type(even) -> <class 'function'>
type(even(2, 10)) -> <class 'generator'>
```
**yield from** is a statement that yields all values from an iterator or iterable.
```py
def a_then_b(a, b):
	yield from a
	yield from b

list(a_then_b([3, 4], [5, 6])) -> [3,4,5,6]

'''Two different methods to implement countdown'''
def countdown(k):
	if k > 0:
		yield k
		yield from countdown(k-1)

def countdown(k):
	if k > 0:
		yield k
		for x in countdown(k-1):
			yield x

"""The following method is wrong"""
def wrong_countdown(k):
	if k > 0:
		yield k
		yield wrong_countdown(k-1)

t = wrong_countdown(5)
next(t) -> 5
next(t) -> 'generator ....'
next(t) -> StopIteration

t = wrong_countdown(5)
next(t) -> 5
a = next(t)
next(a) -> 4
```

## Lecture 31 Streams
### 1. Efficient Sequence Processing
```py
def is_prime(x):
	if x <= 1:
		return False
	return all(map(lambda y: x % y, range(2, x)))
# for x = 2 list(map(...)) is an empty list

def sum_primes(a, b):
	return sum(filter(is_prime, range(a, b)))
# sum() can operate on both iterators and iterables
```
The above method has O(1) space complexity 'cause the lazy nature of range and filter, if either one of them was an explicit function, then it will result in linear space requirement.
```scheme
(define (reduce f s start)
	(if (null? s)
		start
		(reduce f
			(cdr s)
			(f start (car s))
		)
	)
)

(define (range a b)
	(if (>= a b)
		nil
		(cons a (range (+ a 1) b))
	)
)

(define (prime? x)
	(if (<= x 1)
		false
		(null? (filter (lambda (y) (= 0 (remainder x y))) (range 2 x)))
	)
)

(define (sum-primes a b)
	(sum (filter prime? (range a b)))
)

```
The above implementation in scheme is O(n) in space because it is technically not a iterator operation but an explicit function.

### 2. Streams
In scheme, a stream is a list, but the rest of the list is computed only when need:
```scheme
(car (cons-stream 1 2)) -> 1
(cdr-stream (cons-stream 1 2)) -> 2

(cons 1 (/ 1 0)) -> Error
(cons-stream 1 (/ 1 0)) -> (1 . #[delayed])
(car ANS) -> 1
(cdr-stream ANS) -> Error

(define s (cons-stream 1 (cons-stream 2 nil)))
(define t (cons-stream 1 (/ 1 0)))
s -> (1 . #[delayed]) ;it doesn't know what's next
t -> (1 . #[delayed])
(cdr-stream s) -> (2 . #[delayed])
(cdr-stream (cdr-stream s)) -> ()

(define (range-stream a b)
	(if (>= a b)
		nil
		(con-steam a (range-stream (+ a 1) b))
	)
)

(range 1 6) -> (1 2 3 4 5)
(range-stream 1 6) -> (1 . #[delayed])
```

### 3. Infinite Streams
```scheme
(define (int-stream start)
	(cons-stream start (int-stream (+ 1 start)))
)

(define (prefix s k)
	(if (= k 0)
		()
		(cons (car s) (prefix (cdr-stream s) (- k 1)))
	)
)

(define ints (int-stream 3)) 
(car ints) -> 3
(car cdr-stream (cdr-stream ints)) -> 5

(define ints (int-stream 3))
(prefix ints 10) -> (3 4 5 6 7 8 9 10 11 12) 

```

### 4. Stream Processing
```scheme
(define (square-stream s)
	(cons-stream (* (car s) (car s)) (square-stream (cdr-stream s)))
)

(define ints (int-stream 3))
(square-stream ints) -> (9 . #[delayed])
(cdr-stream (square-stream ints)) -> (16 . #[delayed])
```

### 5. Higher-Order Functions on Streams
#### Ex. Sieve of Eratosthenes:
```scheme
(define (sieve s)
	(cons-stream (car s) 
		(sieve
			(filter-stream
				(lambda (x) (not (= 0 (remainder x (car s)))))
				(cdr-stream s)
			)
		)
	)
)

(define (map-stream f s)
	(if (null? s)
		nil
		(con-stream (f (car s))
			(map-stream f (cdr-stream s))
		)
	 )
)

(define (filter-stream f s)
	(if (null? s)
		nil
		(if (f (car s))
			(cons-stream (car s) (filter-stream f (cdr-stream)))
			(filter-stream f (cdr-stream s))
		)
	)
)

(define (reduce-stream f s start)
	(if (null? s)
		start
		(reduce-stream f
			(cdr-stream s)
			(f start (car s))
		)
	)
)

(define primes (sieve (int-stream 2)))
primes -> (2 . #[delayed])
(prefix primes 10) -> (2 3 5 7 11 13 17 19 23 29)
```

### 6. Promise
> A promise is an evaluation, along with an environment in which to evaluate it. Delaying an expression creates a promise to evalutate it later in the current environment. Forcing a promise returns its value in the environment in which it was defined.

```scheme
(define promise (let (x 2)) (delay (+ x 1)))
(define x 5)
(force promise) -> 3
```
....

....

***Too complicated, Skipped!!***

## Lecture 32 Declarative Programming
### 1. Declarative Languages
In declarative languages such as SQL & Prolog:

1. A 'program' is a description of the desired result

2. The interpreter figures out how to generate the result 

In imperative programming such as Python & Scheme:

1. A 'program' is a description of computational processes

2. The interpreter carries out execution/evalution rules

```sql
create table " " as
	select <v> as <n>, <v> as <n>, <v> as <n> union
	select <v>       , <v>       , <v>    
  
```

### 2. SQL
A **select** statement creates a new table, either from scratch or by projecting a table

A **create table** statement gives a global name to a table

Dunno what to record

### 3. Projecting Tables
```sql
select [columns] from [table] where [condition] order by [order];
```

### 4. Arithmetic
```sql
select [column] + n * [column] as [] from []
```

## Lecture 33 Tables
### 1. Joining Tables
```sql
select [column] from tableA, tableB where [column1 of A] = [column2 of B] and [column2 of A] = <value> 
```

### 2. Aliases and Dot Expressions
```sql
select a.column2 as first, b.column2 as second from tableA as a, tableA as b where a.column1 = b.column1 and a.column2 < b.column2;
```

Involved...

### 3. Numerical Expressions
Dunno what to record

### 4. String Expressions
```sql
select "hello," || " world"; -> hello, world
```
Donno what to record

## Lecture 34 Aggregation
### 1. Aggregate Functions
```sql
select max(column1) from tableA
select sum(column1) from tableA
select avg(column1) from tableA
select count(column1) from tableA
select count(distinct column1) from tableA 
```

### 2. Groups
Rows in a table can be grouped, and aggregation is performed on each group:

```sql
select [columns] from [table] group by [columns/expression] having [expression]
```
Grouping by multiple columns means to group rows that have the exact same value pair in all the selected columns. 

having operates on the each group separately before where takes effect

### 3. Select Grammar
Dunno what to record


### 4. Create Tables
```sql
create table numbers (n UNIQUE, note default "No comment") -> create an empty table
drop table tableA
```

### 5. Modifying Tables
```sql
insert into table(column) values (value);

update table set column2 = n where n > 2 and n % 2 = 0

delete from column where [expression]
```

## Lecture 35 Databases
### 1. Python and SQL
The entire chapter is quite superficial, confusing, and ambiguous, if you wanna really learn how to use the sqlite3 package, pls check the official website.
```py
import sqlite3
db = sqlite3.Connection('n.db')
db.execute("CREATE TABLE nums SELECT 2 UNION SELECT 3;")
db.execute("INSERT INTO nums VALUES (?), (?), (?);", range(4, 7))
print(db.execute("SELECT * FROM nums").fetchall())
'''
[(2,), (3,), (4,), (5,), (6,)]
'''
db.commit() -> '''Will pass all modification to n.db'''
```

### 2. SQL Injection Attack
If the following happens:
```py
name = "Robert'); DROP TABLE Students; --"
cmd = "INSERT INTO Students VALUES ('" + name + "');"
db.executescript(cmd) 
```
It will drop the table student.

Therefore we should instead use this format:
```py
db.execute("INSERT INTO students VALUES (?)", [name])
```

### 3. Database Connections
Dunno what to record

## Lecture 36 Distributed Data
### 1. Unix
Dunno what to record

### 2. Big Data
Dunno what to record

### 3. Apache Spark
Apache Spark execution model: processing is defined centrally but executed remotely

A Resilient Distribution Dataset(RDD) is distributed in partitions to worker nodes

A driver program defines transformation and actions on an RDD

A cluster manager assigns tasks to individual worker noded to carry them out

Worker nodes perform computation & communicate values to each other

Final results are communicated back to the driver program


### 4. MapReduce
An important early distributed processing systems was MapReduce, developed at Google.

*Step 1*: Each element in an input collection produces zero or more key-value pairs(map)

*Step 2*: All key-value pairs that share a key are aggregated together(shuffle)

*Step 3*: The values for a key are processed as a sequence(reduce)

Apache Spark is invented to replace MapReduce

## Lecture 37 Natural Languages
### 1. Ambiguity
Donno what to record

### 2. Syntax Trees
Donno what to record

### 3. Grammar
Donno what to record

***Chapter Skipped***
