# Programação funcional em Python 

Python Cerrado 2018

Fábio Macêdo Mendes



---
## Programação orientada a funções


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
### Fatorial funcional à la 1930

Programação funcional surgiu antes dos computadores

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
@[1] 
@[2] (Lambda, lambda, lambda...)
@[5-8] (WAT?)
@[9-11] (???)


+++
### Comparação


```python
def fat(n):
    if n == 0:
        return 1
    else:
        return n * fat(n - 1)
```
@[2] (Lambda não possui condicional)
@[3] (não usa literais de números ou booleanos)
@[5] (não faz referência a nenhum número ou operação matemática)
@[1-5] (Somente funções que recebem e retornam funções...)


+++
###  Como funciona?
#### (números codificados como funções)

```python
def five(f, x):
    return f(f(f(f(f(x)))))

>>> five(lambda x: x + 1, 0)
5
>>> five(lambda x: '-' + x, '')
'-----'
>>> fat_of_five = fat(five)
>>> fat_of_five(lambda x: x + 1, 0)
120
```
@[1-2](Número 5: aplica função 5 vezes no argumento)
@[4](função incrementa por um e começamos em x=0)
@[4-5]
@[6](Podemos usar outras codificações)
@[6-7]
@[8](Agora o fatorial...)
@[8-10] (:-])


+++
### Fatorial from scratch

@ul
- Uma única expressão Python, 208 bytes, gzipped
- Define números naturais, multiplicação e subtração
- Booleanos e o comando "if"
- Implementa recursão de funções
- No espaço restante implementa o fatorial...
@ulend




---
## Cálculo lambda
##### (podemos utilizar funções para computar qualquer coisa?)


+++
### O que aconteceu nas décadas de 1930/40 que fez as pessoas começarem a pensar daquele jeito?

@ul
* Abalos nas fundações da matemática: teorema da incompetude de Gödel
* *Existem afirmações verdadeiras na matemática que não podem ser demonstradas*
@ulend

    
+++ 
### Ponto central é a noção de prova e algoritmo

@ul
- Quais verdades matemáticas podem ser demonstradas sistematicamente?
- Quais funções podem ser computadas por um processo mecânico?
- Quais passos podem ser realizados por uma máquina idealizada?
@ulend


+++
### O que pode ser efetivamente computado?

@ul
- Programas são funções $$P(inputs) = results$$
- Que tipo de funções matemáticas retornam valores usando apenas passos bem definidos?
@ulend


+++
### Diferentes respostas

@ul
- **Gödel (1933)**
    + classe funções aceitáveis geradas por recursão, composição ou minimização
- **Church (1936)**
    + cálculo lambda; funções que recebem e retornam funções e operam por manipulação simbólicas
- **Turing (1936)**
    + modelo mecânico para computação; máquina de Turing
@ulend


+++
### Turing: Hardware
##### (modelo principal para linguagens imperativas)
@ul
- Hardware especializado (máquinas de calcular)
- Código de máquina
- Assemblers
- Linguagens imperativas
- Programação estruturada (1958, Algol)
- O foco mudou para arquitetura e otimizações
@ulend


+++
### Church: Software
##### (computação como transformações matemáticas simples do cálculo lambda)

@ul
- Funções de alta ordem (1958, Lisp)
- John Backus (1978): *"A programação pode se libertar do estilo Von Neumann?"*
- Lazy evaluation e Haskell (1990)
- Renascimento funcional (2000s): 
    @ul[](false)
    - Novas linguagens funcionais
    - Adoção de idiomas funcionais por outras linguagens
    - Saiu dos nichos acadêmicos
    @ulend
@ulend



---
## Conceitos de programação funcional:
### Funções como valores


+++ 
### Funções em Python são valores

```python
def add(x, y):
    return x + y

operator = add

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
@[1-2](Funções de primeira ordem/funções "comuns")
@[4](Podemos salvar em variáveis e usar como valores)
@[6-10](Segunda ordem: funções como argumentos)
@[12-15](Segunda ordem: closures)
@[17-20](Transformações de funções)


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
@[7-10](Função transformada)
@[7](Usamos um decorador dentro do nosso decorador!)


+++
#### Nota sobre decoradores: repassando argumentos

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
@[4-5] (Chamada de função "tradicional")
@[6-8] (Passando lista de argumentos)
@[9-11] (Passando argumentos por nome)


+++
### Nota: Lambdas

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
    # Não produz efeitos colaterais
    # Não modifica estado global
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
        dispare_os_mísseis()
    return x + y
```
@[1-3](Funções sem argumentos; seriam constantes?)
@[5-6](Funções sem retorno; por que chamou?)
@[8-11](Efeitos colaterais: parece função mais não é)
@[1-11](Python é uma linguagem impura: pureza precisa ser mantida por disciplina)


+++
### Nota: mais anti-patterns

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
- Centrado em dados: T(data) -> new_data
- Encadeamos funções simples para gerar funções mais complexas
- Grande reaproveitamento de código
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
### Podemos resumir em uma função

```python
def pipe(x, *funcs):
    for f in funcs:
        x = f(x)
    return x

>>> pipe(-2, abs, sqrt, str)
'1.4142135623730951'
```

@[1-4] (Aplicamos cada função ao resultado da anterior)
@[6-7] (Funções são aplicadas da esquerda para a direita)


+++ 
### Composição

```python
def compose(*funcs):
    return lambda x: pipe(x, *funcs)

>>> f = compose(abs, sqrt, str)
>>> f(-2)
'1.4142135623730951'
>>> f(4)
'2.0'
```

@[1-2] (Tiramos o argumento do pipe para criar uma transformação)
@[4] (Criamos a função...)
@[5-8] (... e chamamos várias vezes)


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
@[1-6] (Código semi-ilegível)


+++ 
### Podemos mudar o map e simplificar

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
@[1-2] (Recebe f e retorna função que aplica f no argumento com map)
@[6-8] (Não precisamos criar o lambda que recebe as linhas)
@[6] (Este ainda resiste :-[)


+++ 
### Currying

```python
add = lambda x, y: x + y
add_curried = lambda x: lambda y: x + y

>>> succ = add_curried(1)
>>> succ(41)
42
>>> add(1, 2), add_curried(1)(2)
(3, 3)
```
@[2] (Recebe um argumento por vez e retorna funções que esperam os argumentos restantes)
@[4] (É fácil de criar novas funções por aplicação parcial)
@[5-6]
@[7-8] (A assinatura não fica particularmente bonita)


+++ 
### Auto-Curry

```python
def curry(arity, f):
    def curried(*args, **kwargs):
        if len(args) >= arity:
            return f(*args)
        return lambda *extra: curried(*args, *extra)
    return curried

curry = curry(2, curry)

```
@[1] (Aridade = número de argumentos esperados)
@[3-4] (Retorna se receber todos argumentos)
@[5] (Caso contrário, retorna uma aplicação parcial)
@[8] (Aplicamos no próprio curry para usá-la como decorador)



---
## Conceitos de programação funcional:
### Geradores, iteradores, etc

+++
### Lazyness

@ul
- Estruturas de dados funcionais são imutáveis
- Python não oferece estruturas de dados satisfatórias
- Em muitos casos podemos substituir listas por "streams"
- Protocolo de iterável é amplamente suportado
@ulend


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
### Nota: Também temos dicionários, conjuntos e geradores

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
### Nota: manipulando iteradores

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
@[11-12] (Fatia iterador como se fosse lista)


---
## Receitas
#### (algoritmos)

+++
### Raiz de um número

* Raiz de y: iteramos a relação de recorrência $$x' = (y/x + x) / 2$$
* Inicia-se tipicamente em x=1
* Convergência rápida

+++
### Recorência
#### (vamos construir nosso arsenal funcional)

```python
def repeat(f, x):
    yield x
    while True:
        x = f(x)
        yield x

>>> repeat(lambda x: (2 / x + x) * 0.5, 1.0)
1.0, 1.5, 1.41666, ...
```
@[2] (Gera valor sem aplicar)
@[4-5] (Aplica função e gera)
@[1-5] (Trata-se de um gerador infinito)


+++
### Convergência
#### (sabendo quando parar)

```python
def within(eps, it):
    it = iter(it)
    y = next(it)
    for x in it:
        if abs(x - y) < eps:
            return x
        y = x

>>> within(0.1, repeat(lambda x: x / 2, 1.0))
1.0, 0.5, 0.25, 0.125, 0.0625
```
@[3] (Extrai o primeiro elemento do iterador)
@[4] (Retorna os termos subsequentes, comparando com o anterior)
@[5-6] (Para quando a diferença estiver na tolerância)


+++
### Relações de recorrência

```python
@curry(2)
def step_newton(n, y):
    if n == 2:
        return lambda x: (y / x + x) * 0.5
    else:
        alpha = n - 1
        factor = 1 / n
        return lambda x: (y / x**alpha + x) * factor
```
@[2-3] (Especializamos para raiz quadrada)
@[4-7] (Recorência para qualquer n)


+++
### Juntamos tudo

```python
def sqrt(y):
    return within(1e-9, repeat(step_newton(2, y), 1))

>>> sqrt = compose(step_newton(2),
...                lambda f: repeat(f, 1),
...                within(1e-9)) 
```
@[1-2] (Mantemos algum apego ao Python tradicional)
@[4-7] (Funcional A+)
@[4] (Podemos trocar a regra de recorrência)
@[6] (Critério de convergência)
@[5] (Convergência acelerada e outros métodos)



---
## Receitas
#### (estruturas de dados)

+++
### Lista simplesmente encadeada

* Estutura de dados imutável favorita
* Compartilhamento eficiente
* Fácil de manipular

+++
### Lista: pares terminados em nil

```python
cons = namedtuple('Pair', ['head', 'tail'])

@curry(3)
def foldr(f, start, lst):
    if lst is None:
        return start
    else:
        return f(lst.head, foldr(f, start, lst.tail))

@curry(3)
def foldl(f, start, lst):
    while lst is not None:
        head, lst = lst
        start = f(head, start)
    return start
```
@[1] (A cabeça carrega o valor e a cauda o resto da lista)
@[4-9] (Como reduce, mas reduz da direita pra esquerda)
@[5] (Cauda None termina a lista)


+++
### Operações com listas

```python
from operator import add, mul

sum = fold(add, 0)
product = fold(mul, 1)
length = fold(lambda _, n: n + 1, 0)
concat = flip(foldr(cons))
reverse = foldl(cons, None)
map = lambda f, lst: foldr(lambda x, y: cons(f(x), y), nil, lst)
filter = lambda pred, lst: foldr(filter_f, nil, lst)
intersperse = lambda sep, lst: cons(lst.head, foldr(intespace_f(sep), None, lst.tail))

filter_f = lambda x, lst: cons(x, lst) if pred(x) else lst
intespace_f = lambda sep: lambda x, lst: cons(sep, cons(x, lst))
```
@[3-4] (Aplicamos soma/produto a todos termos)
@[5] (Ignoramos o valor para acumular o contador)
@[6] (Concatena duas listas)
@[7] (Inverte a lista)
@[8] (Mapeia função em lista)
@[9] (Seleciona elementos)
@[12] (Fazemos um cons condicional)
@[10] (Insere elementos alternadamente, ugh!)


+++
### Extra: convertendo para listas

```python
def from_seq(it):
    it = iter(it)
    try:
        # Esta recursão certamente vai bater no limite da stack :/
        return cons(next(it), from_seq(it))
    except StopIteration:
        return nil

def to_seq(lst):
    while lst is not nil:
        x, lst = lst
        yield x
```



---
## Saiba mais
### Referências, projetos, linguagens, etc


+++
### Referências

 + [Why Functional Programming Matters](https://www.cs.kent.ac.uk/people/staff/dat/miranda/whyfp90.pdf)


+++
### Bibliotecas

- Stdlib
    + itertools, functools, operator
- Outros
    + [toolz](https://toolz.readthedocs.io/en/latest/)
    + [fn.py](https://github.com/kachayev/fn.py)
    + [sidekick (beta)](https://pypi.org/project/sidekick/)
    + [django.utils.functional](https://docs.djangoproject.com/en/2.1/_modules/django/utils/functional/)


+++
### Linguagens



```python
def map(func, lst):
    return [func(x) for x in lst]

def filter(pred, lst):
    return [x for x in lst if pred(x)]
```

@[1-2] (Lista gerada por um laço for)
@[4-5] (É possível filtrar valores utilizando um "if")


@ul
- Saiba mais: 
    + itertools e functools na stdlib
    + toolz, sidekick
@ulend