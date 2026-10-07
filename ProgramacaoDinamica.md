# Programação Dinâmica

Resumo didático do PDF `Aulas-12-14-ProgramacaoDinamica.pdf`. O material apresenta programação dinâmica como técnica para resolver problemas de otimização com subproblemas sobrepostos, usando como exemplos a multiplicação de cadeias de matrizes, a maior subsequência comum e a distância de edição.

## 1. Ideia central

Programação dinâmica divide um problema em estados menores, calcula cada estado uma vez e reutiliza os resultados. É especialmente útil quando:

- **Há subestrutura ótima:** uma solução ótima pode ser construída a partir de soluções ótimas de subproblemas.
- **Há subproblemas sobrepostos:** a solução recursiva volta a calcular os mesmos estados.

Uma solução recursiva direta pode ser exponencial quando repete muitos cálculos. A programação dinâmica guarda os resultados em uma tabela, reduzindo esse trabalho redundante.

### Como construir uma solução

1. Defina com precisão o que representa cada estado da tabela.
2. Identifique os casos-base.
3. Escreva a recorrência que calcula um estado a partir de estados menores.
4. Escolha uma ordem de preenchimento que garanta que os valores necessários já estejam disponíveis.
5. Leia o valor final e, se for preciso obter a solução e não só seu custo, guarde as escolhas feitas para reconstruí-la.

Há duas formas comuns de implementar a técnica:

- **Memoização (top-down):** mantém a definição recursiva e armazena cada resultado calculado.
- **Tabela (bottom-up):** preenche iterativamente os estados, começando pelos casos-base.

## 2. Multiplicação de cadeias de matrizes

Multiplicar matrizes não é associativo em custo: os parênteses não mudam o resultado, mas podem mudar muito o número de operações escalares. Para matrizes `A₁, A₂, ..., Aₙ`, em que `Aᵢ` tem dimensão `pᵢ₋₁ × pᵢ`, queremos escolher a ordem de multiplicação de menor custo.

Por exemplo, considere dimensões `A: 30×1`, `B: 1×40`, `C: 40×10` e `D: 10×25`:

| Parentização |  Custo em multiplicações escalares |
| ------------ | ---------------------------------: |
| `((AB)C)D`   |  `1.200 + 12.000 + 7.500 = 20.700` |
| `(AB)(CD)`   | `1.200 + 10.000 + 30.000 = 41.200` |
| `A((BC)D)`   |          `400 + 250 + 750 = 1.400` |

Defina `M(i,j)` como o custo mínimo para multiplicar a cadeia de `Aᵢ` até `Aⱼ`. Para uma única matriz, não há multiplicação. Para uma cadeia maior, teste cada ponto de separação `k`:

```text
M(i,i) = 0

M(i,j) = min, para i ≤ k < j, de:
         M(i,k) + M(k+1,j) + p(i-1) · p(k) · p(j)
```

O último termo é o custo de multiplicar os dois resultados: uma matriz de dimensão `p(i-1) × p(k)` por uma de dimensão `p(k) × p(j)`. Uma recursão direta repete muitos estados e tem custo exponencial. Na tabela bottom-up, calculamos primeiro cadeias de comprimento 1, depois 2, 3 e assim por diante. Há `Θ(n²)` estados e até `n` pontos de corte por estado, resultando em `Θ(n³)` tempo e `Θ(n²)` espaço. Guardar o melhor `k` permite reconstruir os parênteses ótimos.

## 3. Maior subsequência comum

Uma subsequência mantém a ordem dos elementos, mas pode ignorar posições. A **maior subsequência comum (LCS)** de duas sequências é a subsequência de maior comprimento presente em ambas; não precisa ocupar posições consecutivas.

Para sequências `X` e `Y`, defina `L(i,j)` como o comprimento da LCS dos prefixos `X[1..i]` e `Y[1..j]`:

```text
L(i,0) = L(0,j) = 0

Se X[i] = Y[j]:
    L(i,j) = L(i-1,j-1) + 1
Caso contrário:
    L(i,j) = max(L(i-1,j), L(i,j-1))
```

Preencha a tabela em ordem crescente de prefixos. Para comprimentos `m` e `n`, o tempo e o espaço são `Θ(mn)`. Para reconstruir a subsequência, comece na última célula: quando os caracteres coincidem, inclua o caractere e ande na diagonal; caso contrário, siga para a célula vizinha de maior valor. Se apenas o comprimento for necessário, é possível reduzir o espaço para `Θ(min(m,n))` mantendo duas linhas da tabela.

## 4. Distância de edição

A distância de edição mede o custo mínimo para transformar uma sequência em outra. Com custo unitário por operação, são permitidas inserção, remoção e substituição de um elemento; manter elementos iguais não tem custo.

Defina `D(i,j)` como o custo mínimo para transformar os primeiros `i` caracteres de `X` nos primeiros `j` caracteres de `Y`:

```text
D(i,0) = i
D(0,j) = j

Se X[i] = Y[j]:
    D(i,j) = D(i-1,j-1)
Caso contrário:
    D(i,j) = 1 + min(
        D(i-1,j),     remoção
        D(i,j-1),     inserção
        D(i-1,j-1)    substituição
    )
```

A tabela tem `Θ(mn)` células e é preenchida em `Θ(mn)` tempo. Para recuperar uma sequência de operações, percorra a tabela de volta a partir de `D(m,n)`, identificando qual transição produziu cada valor. Se apenas a distância for necessária, duas linhas bastam, reduzindo o espaço para `Θ(min(m,n))`.

## 5. Como reconhecer e analisar o problema

Em problemas de otimização, não basta encontrar uma solução viável: queremos minimizar ou maximizar uma medida, como custo, quantidade ou comprimento. Para aplicar programação dinâmica, procure subproblemas que reaparecem e verifique se a escolha ótima pode ser expressa usando soluções ótimas menores.

Ao analisar a implementação, conte os estados distintos e o trabalho feito para calcular cada um. O custo total é, em geral, o número de estados multiplicado pelo trabalho por estado. Também diferencie o custo de obter o valor ótimo do custo de reconstruir a solução, e considere o espaço usado pela tabela.

## Roteiro

1. Defina o estado e o significado de cada índice.
2. Estabeleça casos-base que correspondam a subproblemas vazios ou mínimos.
3. Derive a recorrência considerando as escolhas possíveis.
4. Determine a ordem de preenchimento e calcule cada estado uma única vez.
5. Identifique a célula que contém a resposta final.
6. Guarde decisões ou percorra a tabela ao contrário para reconstruir a solução, se necessário.
7. Analise tempo pelo número de estados e pelo trabalho em cada estado; analise espaço pela quantidade de valores armazenados.
