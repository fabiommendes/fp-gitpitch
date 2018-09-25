# Programação funcional em Python 

Python Cerrado 2018

Fábio Macêdo Mendes


---
## Programação orientada a funções


+++
Funções são os blocos básicos da programação

```python
def fat(n):
    if n == 0:
        return 1
    else:
        return n * fat(n - 1)
```


+++
### Fatorial funcional fundamentalista (à la 1930)

```python
fat = (
    lambda f: lambda x: 
        f(f, x))((
        lambda f, n: 
        (lambda cond, then, else_: cond(then, else_))
        ((lambda n: n(lambda _: 
                      lambda a, b: b(), 
                      lambda a, b: a()))(n), 
             lambda: lambda f, x: f(x), 
             lambda: 
                 (lambda n, m: lambda f, x: n(lambda y: m(f, y), x))
                 (n, 
                  f(f, 
                    ((lambda n:
                      lambda f, x: 
                      (lambda k: k(lambda a: a))
                      (n(lambda g: lambda h: h(g(f)), 
                         lambda y: x))))(n))))))
```


+++
### Reparem

@ul
* Nenhum condicional (?)
* Nenhum booleano (!?)
* Não faz referência a nenhum número ou operação matemática (WAT?!)
* Somente funções que recebem e retornam funções...
* (E funciona)
@ulend

+++
###  Números são codificados como funções...

```python
def five(f, x):
    return f(f(f(f(f(x)))))

>>> five(lambda x: x + 1, 0)
5
>>> five(lambda x: '-' + x, '')
'-----'
```
@[1-2](Número 5: aplicamos f 5 vezes em x)
@[4](f: incrementa por um, começamos em x=0)
@[4-5]
@[6](Podemos usar outras codificações)
@[6-7]

+++
Podemos trazer nosso fatorial de volta para o "mundo normal" e verificar que ele 
realmente funciona:

```python
>>> fat(five)(lambda x: x + 1, 0)
120 
```


+++
### Resumindo...

Uma única expressão Python (208 bytes, gzipped):

@ul

- Define números naturais, multiplicação e subtração
- Define booleanos
- Implementa o comando "if"
- Implementa recursão de funções
- (Ah!, e também a função fatorial...)

@ulend



---
## Cálculo lambda


+++
### O que aconteceu nas décadas de 1930/40?

* Problemas sérios nos fundamentos da matemática (Gödel)
* (Existem verdades matemáticas que não podem ser demonstradas por um procedimento formal)
* O que define um algoritmo?
    - Quais verdades matemáticas podem ser demonstradas por um algoritmo?
    - Quais funções podem ser computadas por um processo mecânico?
    - Quais passos podem ser realizados por uma máquina?
    

+++
### O que pode ser efetivamente computado?

Em que condições uma função produz resultados usando apenas processos bem definidos?

* Gödel (1933): funções geradas por recursão, composição ou minimização
* Church (1936): cálculo lambda; funções que recebem e retornam funções 
* Turing (1936): modelo mecânico para computação; máquina de Turing


+++
### Turing: Hardware

* Evoluiu para um modelo para as linguagens imperativas
    - Hardware especializado (máquinas de calcular)
    - Código de máquina
    - Assemblers
    - Linguagens imperativas
    - Programação estruturada (1958, Algol)
* O foco passou a ser arquitetura e otimizações


+++
### Church: Software

* Cálculo lambda: computação por transformações matemáticas simples
* Implementação inicial mais difícil:
    - Funções de alta ordem (1958, Lisp)
    - John Backus (1978): "A programação pode se libertar do estilo Von Neumann?"
    - Lazy evaluation 
    - Haskell (1990)
    - Haskell ganhou IO (1998)
    - Renascimento funcional: 
        - Novas linguagens funcionais
        - Adoção de idiomas funcionais por outras linguagens
        - Saiu dos nichos acadêmicos




---
## Conceitos de programação funcional:
### Funções como valores

+++ 
```python
def add(x, y):
    return x + y

def map(func, lst):
    result = []
    for x in lst:
        result.append(func(x))
    return result

def incr_by(n):
    def incrementer(x):
        return x + n
    return incrementer

def flip(f):
    def flipped(x, y):
        return f(y, x)
    return flipped
```
@[1-2](Funções de primeira ordem)
@[4-8](Segunda ordem: funções como valores)
@[10-13](Segunda ordem: closures)
@[15-18](Transformações de funções)


+++
### Exemplo: decoradores
```python
@route('users/<id>')
def profile(id):
    return "Hello {get_user(id)}"

def route(url):
    def decorator(func):
        @functools.wraps(func)
        def decorated(arg):
            response = func(arg)
            return wrap_response(response)
        
        register_url(url, decorated)
        return decorated
        
    return decorator
```
@[1-3](Função decorada)
@[5](Função route recebe uma url...)
@[15](... e retorna um decorador)
@[6](Decorador recebe uma função...)
@[13](... e retorna uma função decorada)
@[7](Usamos um decorador dentro do nosso decorador!)
@[7-10](Função transformada)


+++
### Repassando argumentos

```python
def func(*args, **kwargs):
    # args: argumentos passados por posição (tupla)
    # kwargs: argumentos passados por nome (dicionário)
    return ...

>>> args = (1, 2)
>>> add(*args)
3
>>> kwargs = {'x': 1, 'y': 2}
>>> add(**kwargs)
3
```


---
## Conceitos de programação funcional:
### Geradores, iteradores, etc


+++
### Compreensão de listas

```python
def map(func, lst):
    return [func(x) for x in lst]

def filter(pred, lst):
    return [x for x in lst if pred(x)]
```

+++
### Também temos dicionários, conjuntos e geradores

```python
dicionario = {x: func(x) for x in lst}
conjunto = {func(x) for x in lst}
gerador = (func(x) for x in lst)
```

+++
### Geradores 

```python
def fibos():
    x, y = 1, 1
    yield from (x, y)
    
    while True:
        x, y = y, y + x
        yield y

# Loop infinito: objectos são calculados sob demanda
for x in fibos():
    print(x)    
```

+++
### Manipulando iteradores

* itertools.count(): 0, 1, 2, 3, ...
* map(func, it): aplica função em todos elementos do iterador
* filter(pred, it): exclui todos elementos para os quais pred(x) é falso
* functools.reduce(func, it): "reduz" iterável pela aplicação da função: f(f(f(f(x0, x1), x2), x3), ...)
* itertools.chain(it1, it2, ...): junta iteráveis
* itertools.islice(it, start, stop, step): fatia iterável como lista 


---
## Conceitos de programação funcional:
### Funções puras

+++ 
### Funções puras

Calcula um único valor para um conjunto de argumentos (e não faz mais nada além disto)

```python
def pura(x, y):
    # Não pode modificar estado global
    # Não produz efeitos colaterais
    # Não depende de estado global mutável
    # Não tem saídas aleatórias
    # Não pode modificar estrutura de dados mutáveis
    # Não faz IO
    return x + y
```
@[2]
@[3]
@[4]
@[5]
@[6]
@[7]
@[1-8](Funções puras são definidas pela interface e não pela implementação)

+++
### Anti-patterns impuras

```python
def sem_argumentos_de_entrada():
    resposta = 42
    return resposta

def sem_valor_retorno(x):
    print(f'Hello {x}')
    
def impura_disfarçada(x, y):
    if random.random() < 0.01:
        lance_os_mísseis()
    return x + y
```
@[1-3](Funções sem argumentos: constantes?)
@[5-6](Funções sem retorno: por que chamou?)
@[8-11](Efeitos colaterais: parece função mais não é)
@[1-11](Python é uma linguagem impura: pureza precisa ser mantida por disciplina)


+++
### Mais anti-patterns

```python
def join_lists(list_a, list_b):
    list_a.extend(list_b)
    return list_a

def read_file():
    return open('file.txt').read()
```
@[1-3](Modifica a lista list_a)
@[5-6](Resultado depende do conteúdo de file.txt)