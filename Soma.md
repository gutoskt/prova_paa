# Somatórios, análise de custo e Divisão e Conquista

Resumo didático do PDF `Aulas-3e4-SomasProgNRecursivo.pdf`. O material ensina a manipular somatórios, contar operações de algoritmos e entender soluções recursivas como o MergeSort.

## 1. Somatórios

O símbolo sigma resume uma soma. Em `Σ(i=1 até n) i`, por exemplo, somamos os valores de `i` de 1 a `n`:

```text
Σ(i=1 até n) i = 1 + 2 + ... + n
```

Fórmulas úteis:

| Soma | Resultado |
| --- | --- |
| `Σ(i=1 até n) 1` | `n` |
| `Σ(i=1 até n) i` | `n(n+1)/2` |
| `Σ(i=1 até n) i²` | `n(n+1)(2n+1)/6` |
| `Σ(i=1 até n) i³` | `[n(n+1)/2]²` |
| `Σ(i=0 até n) a^i`, `a ≠ 1` | `(a^(n+1)-1)/(a-1)` |
| `Σ(i=1 até n) 1/i` | `Θ(log n)` |

Uma progressão aritmética soma termos com diferença constante. A soma de `1` a `n` é `n(n+1)/2`. Em uma progressão geométrica, cada termo é multiplicado por uma razão `a`; suas somas crescem exponencialmente quando `a > 1`.

As propriedades facilitam a manipulação:

- Constante pode sair da soma: `Σ c·aᵢ = c·Σ aᵢ`.
- Soma pode ser separada: `Σ(aᵢ+bᵢ) = Σaᵢ + Σbᵢ`.
- Reordenar os termos não muda o resultado, desde que nenhum termo seja perdido ou repetido.

### Perturbação

Para descobrir uma fórmula, escreva a soma de uma segunda maneira e combine as expressões. Exemplo, deduzindo a soma de `1` a `n`:

```text
S = 1 + 2 + 3 + ... + (n-1) + n
S = n + (n-1) + (n-2) + ... + 2 + 1
2S = (n+1) + (n+1) + ... + (n+1) = n(n+1)
S = n(n+1)/2
```

Na soma geométrica, a mesma ideia é aplicada a `S = 1 + a + ... + aⁿ`: multiplicando por `a` e subtraindo uma expressão da outra, os termos intermediários se cancelam.

### Soma telescópica

Uma soma da forma `Σ(aᵢ - aᵢ₋₁)` cancela os termos internos:

```text
(aₖ-aₖ₋₁) + (aₖ₊₁-aₖ) + ... + (aₙ-aₙ₋₁) = aₙ-aₖ₋₁
```

Ela é útil quando uma expressão pode ser escrita como diferença entre termos consecutivos.

### Aproximação por integral

Integrais ajudam a estimar o crescimento de somas quando uma fórmula exata não é necessária. Por exemplo, `Σ(i=1 até n) i` cresce como `n²`, e a soma harmônica `Σ 1/i` cresce como `log n`. A integral indica a ordem de crescimento, não necessariamente o valor exato da soma.

## 2. Como contar o custo de um algoritmo

Escolha a operação importante (por exemplo, uma comparação ou um `printf`) e conte quantas vezes ela acontece. Em laços aninhados, conte o laço interno para cada valor do externo. Se os limites dependem do índice externo, transforme a contagem em somatório.

### Exemplo completo: dois laços dependentes

Considere:

```text
for x = 0 até x < n:
    for y = 0 até y < x:
        executar_operacao()
```

1. Fixe `x`: o laço interno executa `x` vezes.
2. Some as execuções para todos os valores de `x`:
   `T(n) = Σ(x=0 até n-1) x`.
3. Use a fórmula da progressão aritmética:
   `T(n) = n(n-1)/2 = (n²-n)/2`.
4. Para expressar o crescimento, descarte constantes e termos menores: `T(n) = Θ(n²)`.

O ponto principal é não multiplicar automaticamente os limites dos laços. Como o segundo depende de `x`, a contagem correta é uma soma.

Na ordenação por inserção, o elemento da posição `j` pode ser comparado com até `j-1` elementos anteriores. No pior caso, o total é `Σ(j=2 até n)(j-1) = n(n-1)/2`, portanto o custo é quadrático, `Θ(n²)`. Já o melhor caso de uma entrada ordenada pode ser linear.

## 3. Divisão e Conquista e recorrências

Divisão e Conquista organiza a solução em três etapas:

1. **Dividir:** separar a entrada em subproblemas menores.
2. **Conquistar:** resolver os subproblemas, muitas vezes recursivamente.
3. **Combinar:** juntar as respostas para resolver o problema original.

Uma recorrência descreve o custo em função de entradas menores. Ela deve incluir o custo das chamadas recursivas e o trabalho feito fora delas.

### MergeSort

O MergeSort divide o vetor em duas metades, ordena cada metade recursivamente e intercala as duas metades ordenadas. A intercalação percorre os elementos uma vez, então custa `Θ(n)`.

Para `n` potência de 2:

```text
T(1) = Θ(1)
T(n) = 2T(n/2) + Θ(n)
```

Em cada nível da recursão, o total de elementos intercalados é `n`. Há `log₂ n` níveis de divisão; portanto, somando o trabalho dos níveis, `T(n) = Θ(n log n)`.

Exemplo do PDF, do início ao fim:

```text
Entrada:  [85, 24, 63, 45, 17, 31, 96, 50]
Dividir:  [85, 24, 63, 45] | [17, 31, 96, 50]
          [85, 24] | [63, 45] | [17, 31] | [96, 50]
          [85] [24] [63] [45] [17] [31] [96] [50]
Intercalar pares: [24, 85] | [45, 63] | [17, 31] | [50, 96]
Intercalar grupos: [24, 45, 63, 85] | [17, 31, 50, 96]
Intercalar final: [17, 24, 31, 45, 50, 63, 85, 96]
```

Na intercalação final, compare os primeiros elementos das metades: `24` e `17`, então copie `17`; depois compare `24` e `31`, copie `24`; continue da mesma forma até uma metade acabar e copie o restante da outra. Assim, cada nível combina listas já ordenadas e o resultado final também fica ordenado.

## 4. Exemplo: encontrar mínimo e máximo

O PDF compara estratégias para encontrar os dois extremos de um vetor:

- Fazer uma comparação independente com o máximo e outra com o mínimo para cada elemento usa `2(n-1)` comparações.
- Usar `if/else` evita sempre a segunda comparação: o custo varia com a ordem dos dados. O melhor caso é `n-1`; no pior, `2(n-1)`.
- Comparar os elementos em pares pode reduzir o número de comparações. Para `n` par, compara-se o primeiro par entre si e depois cada par com o mínimo e o máximo atuais: `1 + 3(n-2)/2 = 3n/2 - 2` comparações.

As três estratégias continuam com custo assintótico `Θ(n)`, mas a última faz menos comparações. Isso ilustra por que contar operações ajuda a comparar algoritmos que têm a mesma ordem de crescimento.

## Roteiro para resolver questões

1. Identifique a operação que deve ser contada.
2. Determine quantas vezes cada laço executa, observando limites que dependem de outros índices.
3. Escreva a contagem como somatório ou recorrência.
4. Resolva usando uma fórmula de soma, cancelamento telescópico ou expansão da recorrência.
5. Apresente o custo exato quando pedido e a ordem assintótica (`Θ`) para resumir o crescimento.

Para o MergeSort, declare também o caso-base. Para os laços, não esqueça que uma entrada já ordenada pode ter custo diferente do pior caso.
