# Divisão e Conquista: matrizes e ordenação

Resumo didático do PDF `Aulas-11-Projeto-DivisãoEConquista - Modo de Compatibilidade.pdf`. O material apresenta a estratégia de Divisão e Conquista por meio da multiplicação de matrizes e de algoritmos de ordenação, com atenção às recorrências e aos casos em que o particionamento fica desequilibrado.

## 1. A estratégia de Divisão e Conquista

O método organiza a solução em três etapas:

1. **Dividir:** separar uma entrada grande em subproblemas menores e disjuntos.
2. **Conquistar:** resolver os subproblemas, em geral recursivamente.
3. **Combinar:** reunir as soluções para obter a resposta do problema original.

Para analisar o custo, conte as chamadas recursivas e o trabalho realizado fora delas. Uma forma comum é `T(n) = aT(n/b) + f(n)`, em que `a` é o número de subproblemas, cada um com tamanho `n/b`, e `f(n)` é o custo de dividir e combinar.

## 2. Multiplicação de matrizes

Divida cada matriz quadrada de ordem `n` em quatro blocos de ordem `n/2`. Se

```text
A = | A11 A12 |    B = | B11 B12 |
	| A21 A22 |        | B21 B22 |
```

então cada bloco do produto `C = AB` é formado por duas multiplicações de blocos e uma soma. O método recursivo direto calcula oito produtos de matrizes menores e faz somas de blocos:

```text
T(n) = 8T(n/2) + Θ(n²)
```

Pelo Teorema Mestre, `T(n) = Θ(n³)`, a mesma ordem assintótica da multiplicação convencional. Dividir em blocos, por si só, não torna o algoritmo mais rápido: é preciso reduzir o número de multiplicações recursivas.

### Algoritmo de Strassen

Strassen calcula sete produtos recursivos, usando somas e subtrações adicionais entre blocos. Com `M1` a `M7`:

```text
M1 = (A11 + A22)(B11 + B22)    M2 = (A21 + A22)B11
M3 = A11(B12 - B22)            M4 = A22(B21 - B11)
M5 = (A11 + A12)B22            M6 = (A21 - A11)(B11 + B12)
M7 = (A12 - A22)(B21 + B22)

C11 = M1 + M4 - M5 + M7       C12 = M3 + M5
C21 = M2 + M4                 C22 = M1 - M2 + M3 + M6
```

A recorrência passa a ser `T(n) = 7T(n/2) + Θ(n²)`, portanto:

```text
T(n) = Θ(n^(log₂ 7)) ≈ Θ(n^2.807)
```

Essa ordem é melhor que `Θ(n³)` para matrizes suficientemente grandes. Na prática, as constantes e o custo das somas importam; implementações podem usar a multiplicação convencional para blocos pequenos. Se a dimensão não for potência de 2, pode-se completar as matrizes com zeros até uma dimensão adequada.

## 3. MergeSort

O MergeSort divide o vetor em duas metades, ordena cada metade recursivamente e intercala as duas sequências ordenadas. A intercalação percorre os elementos uma vez, custando `Θ(n)`:

```text
T(1) = Θ(1)
T(n) = 2T(n/2) + Θ(n)
```

Há `log₂ n` níveis, e o trabalho total em cada nível é `Θ(n)`. Assim, o tempo é `Θ(n log n)` nos casos melhor, médio e pior. A intercalação normalmente usa espaço auxiliar `Θ(n)`; em compensação, o custo não depende da ordem inicial dos elementos.

## 4. QuickSort

O QuickSort escolhe um pivô e particiona o vetor em elementos menores e maiores que ele. Depois, ordena recursivamente as partes. O particionamento custa `Θ(n)`, mas os tamanhos das partes dependem da posição do pivô:

```text
T(n) = T(k) + T(n-k-1) + Θ(n)
```

- **Partições equilibradas:** se as partes têm tamanhos próximos, `T(n) = Θ(n log n)`.
- **Pior caso:** se o pivô fica sempre em uma extremidade, as partes têm tamanhos `0` e `n-1`; então `T(n) = T(n-1) + Θ(n) = Θ(n²)`.
- **Pivô aleatório:** evita depender de uma escolha fixa desfavorável; o tempo esperado é `Θ(n log n)`, embora o pior caso continue sendo `Θ(n²)`.

O material apresenta esquemas de partição como Hoare e Lomuto. Na partição de Lomuto, costuma-se usar um elemento do início ou do fim como pivô; isso é simples, mas pode produzir partições ruins em entradas específicas. Escolher o pivô aleatoriamente ou usar uma estratégia como mediana de três ajuda a reduzir esse risco, sem eliminar o pior caso teórico.

QuickSort costuma particionar no próprio vetor e usa espaço adicional principalmente na pilha recursiva. Em geral, é rápido na prática, mas não é estável por padrão e seu desempenho depende da qualidade das partições.

## 5. Heap e HeapSort

Um **heap máximo** é uma árvore binária completa em que cada pai tem valor maior ou igual aos valores de seus filhos. Em um vetor indexado a partir de 1, os filhos do índice `i` ficam nos índices `2i` e `2i+1`. Essa representação permite localizar filhos sem armazenar ponteiros.

`Heapify` restaura a propriedade de heap a partir de um nó, descendo pela árvore; seu custo é `O(log n)`. Construir um heap a partir de um vetor inteiro, com `Build-Heap`, custa `O(n)`.

O HeapSort constrói um heap máximo e repete: troca a raiz (maior elemento) com o último elemento da parte ainda não ordenada, reduz o tamanho do heap e executa `Heapify` na nova raiz.

```text
Build-Heap: Θ(n)
n extrações, cada uma com Heapify: Θ(n log n)
Total: Θ(n log n)
```

O HeapSort ordena no próprio vetor e mantém `Θ(n log n)` no melhor, médio e pior caso. Diferentemente do MergeSort, não precisa de um vetor auxiliar linear; normalmente não é estável.

## 6. Comparação dos algoritmos

| Algoritmo | Melhor caso  | Caso médio/esperado                      | Pior caso    | Espaço auxiliar típico                                        |
| --------- | ------------ | ---------------------------------------- | ------------ | ------------------------------------------------------------- |
| MergeSort | `Θ(n log n)` | `Θ(n log n)`                             | `Θ(n log n)` | `Θ(n)`                                                        |
| QuickSort | `Θ(n log n)` | `Θ(n log n)` esperado com pivô aleatório | `Θ(n²)`      | `Θ(log n)` esperado na pilha; `Θ(n)` no pior caso             |
| HeapSort  | `Θ(n log n)` | `Θ(n log n)`                             | `Θ(n log n)` | `Θ(1)` iterativo; `O(log n)` na pilha com `Heapify` recursivo |

Para multiplicação de matrizes, a versão convencional custa `Θ(n³)`; Strassen reduz para `Θ(n^2.807)` ao diminuir de oito para sete o número de produtos recursivos.

## Roteiro para analisar um algoritmo recursivo

1. Identifique como a entrada é dividida e quantos subproblemas são criados.
2. Conte o trabalho fora das chamadas recursivas, incluindo partição, somas ou intercalação.
3. Escreva a recorrência e declare o caso-base.
4. Analise se os subproblemas têm tamanhos equilibrados ou dependem dos dados e do pivô.
5. Resolva a recorrência e diferencie o melhor, o caso esperado/médio e o pior caso.
6. Compare também memória auxiliar e estabilidade quando essas propriedades forem relevantes.
