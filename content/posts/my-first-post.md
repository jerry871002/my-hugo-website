---
title: "My First Post"
date: 2019-10-06T09:20:53+08:00
draft: false
toc: true
---

## 語彙閉包（closure）
---

```python
def get_speak_func(text, volumn):
    def whisper():
        return text.lower() + '...'
    def yell():
        return text.upper() + '!'
    if volumn > 0.5:
        return yell
    else:
        return whisper
```


```python
get_speak_func('Hello, World', 0.7)()
```




    'HELLO, WORLD!'



> 雖然 `whisper` 跟 `yell` 都沒有 `text` 參數，但仍然能夠使用<br>
能夠做到這點的函式，稱為「閉包」

### 可以用來預先設定行為的組態


```python
def make_adder(n):
    def add(x):
        return x + n
    return add
```


```python
plus_3 = make_adder(3)
plus_5 = make_adder(5)
```


```python
print(plus_3(4))
print(plus_5(4))
```

    7
    9


> `make_adder` 就好像一座「工廠」

## 函式就是物件，物件也可以是函式（可被呼叫）
---


```python
class Adder:
    def __init__(self, n):
        self.n = n

    def __call__(self, x):
        return self.n + x
```


```python
plus_3 = Adder(3)
plus_3(4)
```




    7



> 有 `__call__` 方法的物件，就是「可被呼叫的」（callable）

### 用 `callable` 檢查


```python
print(callable(plus_3))
print(callable(make_adder))
print(callable('hello'))
```

    True
    True
    False


## lambda + closure
---


```python
def make_adder(n):
    return lambda x: x + n
```


```python
plus_3 = make_adder(3)
plus_5 = make_adder(5)
print(plus_3(4))
print(plus_5(4))
```

    7
    9


## 裝飾器（decorator）
---

> 吃一個函式進來，吐一個函式出去<br>
i.e. 參數是一個函式，return 的也是一個函式


```python
def uppercase(func):
    def wrapper():
        original_result = func()
        modified_result = original_result.upper()
        return modified_result
    return wrapper
```


```python
def greet():
    return 'Hello'
```


```python
print(greet)
print(greet())
```

    <function greet at 0x112c36d08>
    Hello



```python
@uppercase
def greet():
    return 'Hello'
```


```python
print(greet)
print(greet())
```

    <function uppercase.<locals>.wrapper at 0x112c36d90>
    HELLO


### 一次用多個裝飾器


```python
def strong(func):
    def wrapper():
        return '<strong>' + func() + '</strong>'
    return wrapper

def emphasis(func):
    def wrapper():
        return '<em>' + func() + '</em>'
    return wrapper
```


```python
@strong
@emphasis
def greet():
    return 'Hello!'
```


```python
greet()
```




    '<strong><em>Hello!</em></strong>'



> 裝飾器的效果是「**由下而上**」<br>
可以稱為「裝飾器堆疊（decorator stacking）」

### 不想用裝飾器語法的話


```python
def greet():
    return 'Hello!'

decorated_greet = strong(emphasis(greet))

decorated_greet()
```




    '<strong><em>Hello!</em></strong>'



### 裝飾有參數的函式


```python
def trace(func):
    def wrapper(*args, **kargs):
        print(f'TRACE: calling {func.__name__}() '
              f'with {args}, {kargs}')

        original_result = func(*args, **kargs)

        print(f'TRACE: calling {func.__name__}() '
              f'returned {original_result!r}')

        return original_result
    return wrapper
```


```python
@trace
def say(name, line):
    return f'{name}: {line}'
```


```python
say('Jane', 'Hello, World!')
```

    TRACE: calling say() with ('Jane', 'Hello, World!'), {}
    TRACE: calling say() returned 'Jane: Hello, World!'





    'Jane: Hello, World!'



### 「可除錯」的裝飾器

> 裝飾器會讓原始的函式被「隱藏」


```python
def uppercase(func):
    def wrapper():
        return func().upper()
    return wrapper

@uppercase
def greet():
    """Return a friendly greet"""
    return 'Hello!'
```


```python
print(greet.__name__)
print(greet.__doc__)
```

    wrapper
    None


> 自己寫的裝飾器應該用 `functools.wraps` 來保存原始的資料


```python
import functools

def uppercase(func):
    @functools.wraps(func)
    def wrapper():
        return func().upper()
    return wrapper

@uppercase
def greet():
    """Return a friendly greet"""
    return 'Hello!'
```


```python
print(greet.__name__)
print(greet.__doc__)
```

    greet
    Return a friendly greet


## `*args` 與 `**kargs`
---


```python
def foo(required, *args, **kargs):
    print(required)
    if args:
        print(args)
    if kargs:
        print(kargs)
```


```python
foo()
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-42-c19b6d9633cf> in <module>
    ----> 1 foo()


    TypeError: foo() missing 1 required positional argument: 'required'



```python
foo('hello')
```

    hello



```python
foo('hello', 1, 2, 3)
```

    hello
    (1, 2, 3)



```python
foo('hello', 1, 2, 3, key1='value', key2=999)
```

    hello
    (1, 2, 3)
    {'key1': 'value', 'key2': 999}


### 轉傳 `*args` 與 `**kargs`

* 用在繼承的類別


```python
class Car:
    def __init__(self, color, mileage):
        self.color = color
        self.mileage = mileage

class AlwaysBlueCar(Car):
    def __init__(self, *args, **kargs):
        print(args)
        print(kargs)      
        super().__init__(*args, **kargs)
        self.color = 'blue'
```


```python
AlwaysBlueCar('green', 48392).color
```

        ('green', 48392)
        {}

        'blue'




```python
AlwaysBlueCar(mileage=48392, color='green').color
```

        ()
        {'mileage': 48392, 'color': 'green'}

        'blue'



* 用在包裹的函式


```python
def trace(f):
    @functools.wraps(f)
    def decorated_function(*args, **kargs):
        print(f)
        print(args)
        print(kargs)
        result = f(*args, **kargs)
        print(result)
    return decorated_function

@trace
def greet(greeting, name):
    return '{}, {}!'.format(greeting, name)
```


```python
greet('Hello', 'Bob')
```

        <function greet at 0x111982048>
        ('Hello', 'Bob')
        {}
        Hello, Bob!


## 函式參數拆箱
---


```python
def print_vector(x, y, z):
    print('<%s, %s, %s>' % (x, y, z))
```


```python
print_vector(0, 1, 0)
```

    <0, 1, 0>


> 又繁瑣又容易出錯的方式


```python
tuple_vec = (1, 0, 1)

print_vector(tuple_vec[0],
             tuple_vec[1],
             tuple_vec[2])
```

    <1, 0, 1>


> 簡單又容易除錯


```python
print_vector(*tuple_vec)
```

    <1, 0, 1>
