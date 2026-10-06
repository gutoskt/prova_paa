# Recorrências: métodos de resolução

Resumo didático do PDF `Aulas-5a10-Recorrencias.pdf`. Uma recorrência define uma função ou custo usando valores de instâncias menores. Para resolvê-la, use as condições iniciais e escolha o método adequado: iteração, árvore de recursão, Teorema Mestre, aniquilador ou substituição.

## 1. Entendendo uma recorrência

Exemplo:

```text
T(1) = c
T(n) = 2T(n/2) + n, para n > 1
```

Há duas chamadas para problemas de tamanho `n/2`, e o trabalho fora das chamadas custa `n`. `T(1)=c` é o caso-base: ele encerra a recursão. A recorrência só fica determinada quando conhecemos também seus casos-base.

## 2. Método da iteração

Expanda a recorrência repetidamente, procure um padrão e pare quando alcançar o caso-base. O último passo é resolver o somatório que aparece.

### Exemplo 1: custo constante por chamada

```text
s(0) = 0
s(n) = c + s(n-1)
```

Expandindo `k` vezes:

```text
s(n) = kc + s(n-k)
```

Quando `k=n`, chegamos a `s(0)=0`, então `s(n)=nc`. Logo, o crescimento é `Θ(n)`.

### Exemplo 2: custo que diminui a cada chamada

```text
s(0) = 0
s(n) = n + s(n-1)
```

```text
s(n) = n + (n-1) + (n-2) + ... + 1 + s(0)
	= Σ(i=1 até n) i
	= n(n+1)/2
```

Portanto, `s(n)=Θ(n²)`. O trabalho de cada chamada não é constante: ele forma uma soma de `1` até `n`.

### Recorrência com subproblemas divididos

Para `T(n)=aT(n/b)+cn`, a expansão produz custos por nível proporcionais a `cn`, `cn(a/b)`, `cn(a/b)²`, e assim por diante. Quando `n=b^k`, há `k=log_b n` níveis. Comparar `a` com `b` determina o crescimento:

| Relação | Resultado        |
| ------- | ---------------- |
| `a < b` | `Θ(n)`           |
| `a = b` | `Θ(n log n)`     |
| `a > b` | `Θ(n^(log_b a))` |

Esse resumo vale para essa forma específica, com custo linear `cn` fora das chamadas.

## 3. Árvore de recursão

Desenhe um nó para cada chamada recursiva. Anote o custo de cada nó, some os custos de cada nível e, no fim, some os níveis. A altura é o número de vezes que o tamanho pode ser reduzido até chegar ao caso-base.

### Exemplo do PDF: duas chamadas de tamanho `n/3`

O código faz duas chamadas recursivas com entrada `n/3` e executa um laço de `n` operações:

```text
T(n) = 2T(n/3) + n
```

No nível `i`, há `2^i` subproblemas de tamanho `n/3^i`; o trabalho desse nível é:

```text
2^i · (n/3^i) = n(2/3)^i
```

A altura é aproximadamente `log_3 n`. Os custos formam uma série geométrica decrescente, `n + 2n/3 + 4n/9 + ...`, cuja soma é `Θ(n)`. As folhas também não ultrapassam essa ordem; logo, `T(n)=Θ(n)`.

Árvores são especialmente úteis para Divisão e Conquista. Servem para estimar o custo; quando a estimativa está clara, pode-se prová-la por indução.

## 4. Teorema Mestre

O Teorema Mestre resolve recorrências da forma:

```text
T(n) = aT(n/b) + f(n)
```

Compare o custo `f(n)` feito fora das chamadas com `n^(log_b a)`, que representa a contribuição recursiva. O PDF apresenta estes três casos:

1. Se `f(n)` é polinomialmente menor que `n^(log_b a)`, então `T(n)=Θ(n^(log_b a))`.
2. Se `f(n)=Θ(n^(log_b a))`, então `T(n)=Θ(n^(log_b a) log n)`.
3. Se `f(n)` é polinomialmente maior que `n^(log_b a)` e vale a condição de regularidade `a f(n/b) ≤ c f(n)` para algum `c<1`, então `T(n)=Θ(f(n))`.

### Exemplo completo: `T(n)=9T(n/3)+n`

1. Identifique `a=9`, `b=3` e `f(n)=n`.
2. Calcule o custo recursivo de referência: `n^(log_3 9)=n²`.
3. Compare: `n` é polinomialmente menor que `n²`, então aplica-se o caso 1.
4. Resultado: `T(n)=Θ(n²)`.

O MergeSort é outro exemplo: `T(n)=2T(n/2)+Θ(n)`. Aqui `n^(log_2 2)=n` e o custo externo também é `Θ(n)`, então aplica-se o caso 2: `T(n)=Θ(n log n)`.

## 5. Método do aniquilador

Esse método resolve recorrências lineares homogêneas com coeficientes constantes, como:

```text
t(0) = 0
t(1) = 1
t(n) = t(n-1) + t(n-2)
```

Use a ideia de tentar uma solução `r^n`. Substituindo na recorrência:

```text
r^n = r^(n-1) + r^(n-2)
r² - r - 1 = 0
```

As raízes são `r₁=(1+√5)/2` e `r₂=(1-√5)/2`. Como são distintas, a solução geral é `t(n)=C₁r₁^n+C₂r₂^n`. Use os valores iniciais para determinar as constantes:

```text
t(0)=0  => C₁ + C₂ = 0
t(1)=1  => C₁r₁ + C₂r₂ = 1
```

Resolvendo, `C₁=1/√5` e `C₂=-1/√5`. Portanto:

```text
t(n) = (r₁^n - r₂^n)/√5
```

Em geral, para uma recorrência de ordem `d`, forme a equação característica de grau `d`. Raízes distintas geram termos `Cᵢrᵢ^n`; raízes repetidas acrescentam fatores como `n`, `n²` etc. As condições iniciais determinam as constantes.

## 6. Método da substituição

Substituição é uma prova por indução de um limite que primeiro estimamos. Para provar um limite superior, suponha que os casos menores obedecem à hipótese e mostre que isso implica o limite para `n`. Para limite inferior, faça a prova análoga com uma desigualdade inferior.

### Exemplo completo: provar `T(n)=O(n log n)`

Considere:

```text
T(1) = 1
T(n) = 2T(floor(n/2)) + n
```

Queremos mostrar que existem constantes `c` e `n₀` tais que `T(n) ≤ cn log₂ n` para `n ≥ n₀`.

**Base:** não use `n=1` para essa hipótese, pois `log₂ 1=0` e `T(1)=1`. Verifique separadamente `n=2` e `n=3` (os filhos de `n=3` ainda incluem `1`) e escolha `c` grande o bastante; `c≥2` cobre esses casos.

**Hipótese de indução:** para `m<n`, suponha que `T(m) ≤ cm log₂ m`.

**Passo indutivo:** para `n≥4`, `floor(n/2)≥2`, então podemos aplicar a hipótese a `floor(n/2)`:

```text
T(n) = 2T(floor(n/2)) + n
	≤ 2c floor(n/2) log₂(floor(n/2)) + n
	≤ cn log₂(n/2) + n
	 = cn log₂ n - cn + n
	≤ cn log₂ n, para c ≥ 1
```

Assim, a hipótese se mantém e `T(n)=O(n log n)`. Para concluir `Θ(n log n)`, também é preciso estabelecer um limite inferior; neste caso, o Teorema Mestre fornece esse limite.

Se a indução não fechar, a estimativa ainda pode estar correta. Fortaleça a hipótese, ajuste o caso-base ou tente subtrair um termo de ordem menor, como `T(n)≤cn-b` em vez de `T(n)≤cn`.

## 7. Troca de variável

Quando o tamanho diminui por raiz ou outra transformação inconveniente, troque a variável para obter uma recorrência mais familiar.

### Exemplo do PDF

```text
T(n) = 2T(√n) + log₂ n
```

Defina `m=log₂ n`, então `n=2^m`, e seja `S(m)=T(2^m)`. Como `√n=2^(m/2)`:

```text
S(m) = 2S(m/2) + m
```

Pelo Teorema Mestre, `S(m)=Θ(m log m)`. Voltando a `n`:

```text
T(n) = Θ(log n · log log n)
```

## Roteiro para escolher o método

1. Expansão com redução `n-1` ou `n/b`: comece pela iteração.
2. Várias chamadas recursivas: desenhe a árvore e some por nível.
3. Forma `aT(n/b)+f(n)`: compare `f(n)` com `n^(log_b a)` pelo Teorema Mestre.
4. Recorrência linear homogênea com coeficientes constantes: use a equação característica (aniquilador).
5. Para provar um limite ou quando a solução é conhecida/estimada: use substituição e indução.
6. Entrada reduzida por raiz: tente uma troca de variável, como `m=log n`.

Sempre declare os casos-base, confira arredondamentos como `floor(n/2)` e diferencie `O`, `Ω` e `Θ`: limite superior, inferior e limite justo, respectivamente.
