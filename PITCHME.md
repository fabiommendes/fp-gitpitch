# Programação funcional em Python 

Python Cerrado 2018

Fábio Macêdo Mendes



---
## Programação orientada a funções
###### (todo o resto é desnecessário)


+++
#### Funções são os blocos básicos da programação...

```python
def fat(n):
    if n == 0:
        return 1
    else:
        return n * fat(n - 1)
```

#### ...e com elas podemos fazer qualquer coisa


+++
### Fatorial funcional fundamentalista (à la 1930)

```python
fat = \
  (lambda f: lambda x: f(f, x))(
    (lambda f, n: 
      (lambda cond, then, else_: cond(then, else_))(
        (lambda n: n(lambda _: lambda a, b: b(), lambda a, b: a()))(n), 
         lambda: lambda f, x: f(x), 
         lambda: (lambda n, m: lambda f, x: n(lambda y: m(f, y), x))(
           n, 
           f(f, (lambda n: lambda f, x: (lambda k: k(lambda a: a))
                (n(lambda g: lambda h: h(g(f)), lambda y: x)))(n))))))
```
@[1] (Única linha compreensível)
@[2] (Lambda, lambda...)
@[4] (Condicionais?)
@[5-10] (Desisto :-[)


+++
### Reparem


```python
def fat(n):
    if n == 0:
        return 1
    else:
        return n * fat(n - 1)
```
@[2] (Nenhum condicional)
@[3] (Nenhum valor literal de número ou booleano)
@[5] (Não faz referência a nenhum número ou operação matemática)
@[1-5] (Somente funções que recebem e retornam funções...)


+++
###  Digressão: números são codificados como funções...

```python
def five(f, x):
    return f(f(f(f(f(x)))))

>>> five(lambda x: x + 1, 0)
5
>>> five(lambda x: '-' + x, '')
'-----'
>>> result = fat(five)
>>> result(lambda x: x + 1, 0)
120
```
@[1-2](Número 5: aplicamos f 5 vezes em x)
@[4](f: incrementa por um, começamos em x=0)
@[4-5]
@[6](Podemos usar outras codificações)
@[6-7]
@[8](Executamos o fatorial)
@[8-10]


+++
### Resumindo...

Uma única expressão Python (208 bytes, gzipped):

@ul
- Define números naturais, multiplicação e subtração
- Declara booleanos
- Implementa o comando "if"
- Implementa recursão de funções
- Ah! E também calcula a função fatorial...
@ulend




---
## Cálculo lambda
##### (podemos utilizar funções para computar qualquer coisa?)


+++
### O que aconteceu nas décadas de 1930/40?

@ul
* Rachaduras nas fundações da matemática
* Existem afirmações verdadeiras na matemática que não podem ser demonstradas (Gödel)
@ulend

    
+++ 
### Ponto central era a noção de prova e algoritmo
- Quais verdades matemáticas podem ser demonstradas por um algoritmo?
- Quais funções podem ser computadas por um processo mecânico?
- Quais passos podem ser realizados por uma máquina idealizada?


+++
### O que pode ser efetivamente computado?

@ul
- Programas são funções $P(In) = Out$
- Quais funções retornam saídas usando apenas processos bem definidos?
@ulend


+++
#### Diferentes respostas

@ul
- Gödel (1933)
    + definiu uma classe funções aceitáveis. são geradas por recursão, composição ou minimização
- Church (1936) 
    + cálculo lambda; funções que recebem e retornam funções 
- Turing (1936)
    + modelo mecânico para computação; máquina de Turing
@ulend


+++
#### Turing: Hardware

@ul
* Modelo para linguagens imperativas
    - Hardware especializado (máquinas de calcular)
    - Código de máquina
    - Assemblers
    - Linguagens imperativas
    - Programação estruturada (1958, Algol)
* O foco passou a ser arquitetura e otimizações
@ulend


+++
#### Church: Software
##### (computação como transformações matemáticas simples do cálculo lambda)

@ul
- Funções de alta ordem (1958, Lisp)
- John Backus (1978): "A programação pode se libertar do estilo Von Neumann?"
- Lazy evaluation e Haskell (1990)
- Renascimento funcional (2000s): 
    - Novas linguagens funcionais
    - Adoção de idiomas funcionais por outras linguagens
    - Saiu dos nichos acadêmicos
@ulend



---
## Conceitos de programação funcional:
### Funções como valores


+++ 
#### Funções são valores

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
#### Exemplo: decoradores
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
#### Nota: repassando argumentos

```python
def add(x, y):
    return x + y

>>> add(1, 2)
3
>>> args = (1, 2)
>>> add(*args)
3
>>> kwargs = {'x': 1, 'y': 2}
>>> add(**kwargs)
3
```
@[1-2] (Definimos uma função)
@[4-5] (Podemos chamar do modo "tradicional")
@[6-8] (Passando lista de argumentos)
@[9-11] (Passando argumentos por nome)


+++
#### Lambdas

```python
add = lambda x, y: x + y
```

@ul
- Podem ser utilizadas como valores diretamente
- Aceita uma única linha de comando que deve ser uma expressão
- Similares às funções do cálculo lambda 
@ulend



---
## Conceitos de programação funcional:
### Funções puras

+++ 
### Funções puras

Calcula um único valor para um conjunto de argumentos (e não faz mais nada além disto)

```python
def pura(x, y):
    # Não modifica estado global
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



---
## Conceitos de programação funcional:
### Composição de funções

+++ 
### Arquiteturas funcionais

@ul
- Centrado em dados
- Transformação de dados T(In) -> Out
- Encadeamos funções simples para gerar funções mais complexas
@ulend


+++ 
### Pipeline: padrão recorrente

```python
a = f1(inputs)
b = f2(a)
c = f3(b)
...
result = fn(n)
```

+++ 
#### Podemos abstrair

```python
def pipe(x, *funcs):
    for f in funcs:
        x = f(x)
    return x

>>> pipe(-2, abs, sqrt, str)
'1.4142...'
```

@[1-4] (Aplicamos funções de forma recorrente nos argumentos)
@[6-7] (Funções são aplicadas da esquerda para a direita)


+++ 
### Composição

```python
def compose(*funcs):
    return lambda x: pipe(x, *funcs)

>>> f = compose(abs, sqrt, str)
>>> f(-2)
'1.4142...'
>>> f(4)
'2.0'
```

@[1-2] (Também podemos criar uma função que cria pipelines)
@[4] (Criamos a transformação)
@[5-8] (Chamamos várias vezes)


+++ 
### Explosão de Lambdas

```python
read_csv = compose(
    str.splitlines,
    lambda lines: map(lambda line: line.split(','), lines),
    lambda lines: map(lambda line: map(float, line), lines),
    lambda lines: map(sum, lines),
)
```
@[2] (Separa string em linhas)
@[3] (Mapeia função para separar linhas nas vírgulas)
@[4] (Aplica a função float a cada elemento)
@[5] (Soma os valores de cada linha)
@[1-6] (Código "write-only")


+++ 
### Podemos mudar o map

```python
def fmap(f): 
    return lambda data: map(f, data)

read_csv = compose(
    str.splitlines,
    fmap(lambda line: line.split(',')),
    fmap(fmap(float)),
    fmap(sum),
)
```
@[1-2] (Função que recebe a função, retorna uma função que recebe os dados e depois aplica map)
@[6-8] (Não precisamos de passar o argumento inútil)
@[6] (Ainda sobrou este lambda :-[)


+++ 
### Curring

```python
add = lambda x: lambda y: x + y

>>> succ = add(1)
>>> succ(41)
42
>>> add(1)(2)
3
```
@[1] (Recebe um argumento por vez e retorna funções que esperam os argumentos restantes)
@[3] (É fácil de criar novas funções por aplicação parcial)
@[4-5]
@[6-7] (A assinatura não fica particularmente bonita)



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

@[1-2] (Lista gerada por um laço for)
@[4-5] (É possível filtrar valores utilizando um "if")


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

@[1] (Geradores são funções que "retornam" várias vezes)
@[7] (yield retorna um valor, mas não interrompe a função)
@[3] (yield from retorna todos valor de uma lista)
@[9-11] (Extraímos elementos chamando gerador em um laço for ou criando lista)


+++
### Manipulando iteradores

```python
>>> itertools.count()
0, 1, 2, 3, ...
>>> map(f, [a, b, c, ...])
f(a), f(b), f(c), ...
>>> filter(pred, [ok_a, bad, ok_b, ...])
ok_a, ok_b, ...
>>> functools.reduce(f, [a, b, c, ...])
f(f(f(a, b), c), ...)
>>> itertools.chain([a, b], [c, d, ...])
a, b, c, d, ...
>>> itertools.islice([a, b, c, ...], 0, 2)
a, b, c
```
@[1-2] (Conta números indefinidamente)
@[3-4] (Aplica f em cada elemento do iterador)
@[5-6] (Mantêm apenas elementos em que pred(ok) == True)
@[7-8] (Reduz o iterador pela aplicação de operador)
@[9-10] (Junta dois ou mais iteráveis)
@[11-2] (Fatia iterador como se fosse lista)

@ul
- Para saber mais: 
    + itertools e functools na stdlib
    + toolz, sidekick
@ulend