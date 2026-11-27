# Marco 3 — Rastreamento Manual (Prim Lazy — algs4-py)

## 1. Estruturas de Dados e Estados Iniciais (algs4)

Considerando a instância pequena do enunciado (\(S = 2\) canais de satélite, \(P = 4\) postos avançados):

- \(0 = (0,100)\)
- \(1 = (0,300)\)
- \(2 = (0,600)\)
- \(3 = (150,750)\)

E os módulos da biblioteca algs4 em Python (`bag.py`, `edge.py`, `edge_weighted_graph.py`, `lazy_prim_mst.py` e `min_pq.py`), temos as seguintes estruturas:

### Edge (edge.py)

Encapsula cada aresta não direcionada \((v,w)\) com seu peso real, correspondente à distância euclidiana entre dois postos.

Disponibiliza os métodos:

- `either()`: retorna uma das extremidades da aresta.
- `other(v)`: retorna a outra extremidade.
- `weight()`: retorna o peso da aresta.
- `__lt__()`: permite comparar arestas pelo peso.

### EdgeWeightedGraph (edge_weighted_graph.py) e Bag (bag.py)

O grafo é inicializado com \(V = 4\) vértices e um vetor `_adj` contendo 4 estruturas `Bag`, responsáveis por armazenar as arestas incidentes em cada vértice.

Como o grafo é completo \(K_4\), existem \(E = 6\) arestas.

As distâncias calculadas são:

| Aresta | Distância |
|---|---:|
| (0-1) | 200.00 |
| (0-2) | 500.00 |
| (0-3) | 667.08 |
| (1-2) | 300.00 |
| (1-3) | 474.34 |
| (2-3) | 212.13 |

### MinPQ (min_pq.py)

Fila de prioridade mínima implementada como um heap binário.

Diferentemente de Kruskal, no Prim Lazy a fila não recebe todas as arestas do grafo inicialmente. Ela começa vazia e recebe as arestas conforme os vértices são visitados.

### Estrutura marked

Vetor booleano responsável por indicar quais vértices já foram incorporados à árvore geradora mínima.

Inicialmente:

`marked = [False, False, False, False]`

Nenhum vértice foi visitado.

### Lista mst

Inicializada vazia, armazena as arestas selecionadas para a Árvore Geradora Mínima.

`mst = []`

Como existem 4 vértices, a árvore precisa de \(V-1 = 3\) arestas para conectar todos os postos.

---

## 2. Rastreamento das Operações e Atualização das Estruturas

O algoritmo Prim Lazy começa pelo vértice 0. A partir dele, adiciona todas as arestas incidentes à fila de prioridade.

A cada iteração, a menor aresta é extraída. Caso ela conecte dois vértices que ainda não foram visitados, é adicionada à árvore e o novo vértice é visitado.

### Estado Inicial — Passo 0

Estruturas:

```python
pq = []
marked = [False, False, False, False]
mst = []
```

O algoritmo executa `visit(0)`.

Como o vértice 0 ainda não foi visitado, ele é marcado:

```python
marked = [True, False, False, False]
```

São inseridas na fila todas as arestas incidentes ao vértice 0:

```text
pq:
[(0-1: 200.00),
 (0-2: 500.00),
 (0-3: 667.08)]
```

`mst = []`

### Iteração 1 — Extração da aresta (0, 1), peso 200.00

Operação:

```python
e = pq.del_min()
```

A menor aresta da fila é extraída:

`e = (0-1: 200.00)`

Identificação dos vértices:

```python
v = e.either()  # 0
w = e.other(v)  # 1
```

Verificação:

- O vértice 0 já está marcado.
- O vértice 1 ainda não está marcado.

Como apenas um dos vértices foi visitado, a aresta é válida e não forma ciclo.

A aresta é adicionada à árvore:

```python
mst = [(0-1: 200.00)]
```

Em seguida, o vértice 1 é visitado:

```python
marked = [True, True, False, False]
```

As novas arestas incidentes ao vértice 1 são inseridas na fila. A aresta (0-1) não é inserida novamente porque seu outro extremo já está marcado.

Estado da fila:

```text
pq:
[(1-2: 300.00),
 (1-3: 474.34),
 (0-2: 500.00),
 (0-3: 667.08)]
```

### Iteração 2 — Extração da aresta (1, 2), peso 300.00

Operação:

```python
e = pq.del_min()
```

A menor aresta da fila é:

`e = (1-2: 300.00)`

Identificação:

```python
v = 1
w = 2
```

Verificação:

- O vértice 1 já está marcado.
- O vértice 2 ainda não está marcado.

A aresta é válida e é adicionada à árvore:

```python
mst = [
    (0-1: 200.00),
    (1-2: 300.00)
]
```

O vértice 2 é visitado:

```python
marked = [True, True, True, False]
```

Durante a visita, a aresta (2-3), de peso 212.13, é inserida na fila.

As arestas (0-2) e (1-2) não são inseridas novamente, pois seus outros extremos já estão marcados.

Estado da fila:

```text
pq:
[(2-3: 212.13),
 (1-3: 474.34),
 (0-2: 500.00),
 (0-3: 667.08)]
```

### Iteração 3 — Extração da aresta (2, 3), peso 212.13

Operação:

```python
e = pq.del_min()
```

A menor aresta da fila é:

`e = (2-3: 212.13)`

Identificação:

```python
v = 2
w = 3
```

Verificação:

- O vértice 2 já está marcado.
- O vértice 3 ainda não está marcado.

A aresta é válida e é adicionada à árvore:

```python
mst = [
    (0-1: 200.00),
    (1-2: 300.00),
    (2-3: 212.13)
]
```

O vértice 3 é visitado:

```python
marked = [True, True, True, True]
```

Nesse momento, todos os vértices foram visitados e a árvore possui exatamente \(V-1 = 3\) arestas.

O algoritmo encerra sua execução.

As arestas restantes na fila não são necessárias para construir a árvore geradora mínima.

---

## 3. Extração do Resultado com os Canais de Satélite (S = 2)

A árvore geradora mínima encontrada pelo Prim Lazy contém as seguintes arestas:

| Aresta | Peso |
|---|---:|
| (0-1) | 200.00 |
| (1-2) | 300.00 |
| (2-3) | 212.13 |

Para utilizar os 2 canais de satélite, devemos remover as \(S-1 = 1\) arestas de maior peso da árvore geradora mínima.

Ordenando as arestas pelo peso:

1. (0-1): 200.00
2. (2-3): 212.13
3. (1-2): 300.00

A maior aresta é (1-2), com peso 300.00.

Essa conexão é substituída pela comunicação via satélite, dividindo a rede em duas componentes:

- Componente 1: postos {0, 1}.
- Componente 2: postos {2, 3}.

O alcance mínimo necessário para os rádios será determinado pela maior aresta que permaneceu na árvore.

Portanto:

\[
\boxed{R = 212.13}
\]

**Resultado final: 212.13 km.**

---

## 4. Justificativa das Decisões e do Resultado

### Escolha gulosa do Prim Lazy

O algoritmo Prim Lazy utiliza uma fila de prioridade mínima (`MinPQ`) para selecionar sempre a menor aresta disponível que conecta um vértice já visitado a um vértice ainda não visitado.

A estrutura `marked` permite identificar quais vértices já fazem parte da árvore geradora mínima.

Dessa forma, uma aresta só é adicionada à árvore quando conecta um vértice visitado a outro não visitado, evitando a formação de ciclos.

### Por que o algoritmo é Lazy?

O Prim Lazy recebe esse nome porque não remove imediatamente da fila todas as arestas que se tornam desnecessárias.

Uma aresta pode permanecer na fila mesmo depois que seus dois extremos foram visitados.

Quando essa aresta é extraída, o algoritmo verifica se ambos os extremos já estão marcados. Caso estejam, a aresta é descartada.

Isso significa que a fila pode conter arestas que não serão utilizadas na árvore geradora mínima.

### Redução via canais de satélite

Após a construção da árvore geradora mínima, removemos as \(S-1\) maiores arestas.

Cada aresta removida representa uma conexão que pode ser substituída por comunicação via satélite.

Como temos 2 canais de satélite, removemos uma aresta, dividindo a rede em duas componentes conectadas por rádio internamente.

### Validação do resultado (212.13 km)

A conexão mais longa da árvore geradora mínima é a aresta (1-2), com 300.00 km.

Como essa conexão pode ser substituída pelo canal de satélite, ela deixa de determinar o alcance necessário dos rádios.

As conexões restantes possuem pesos de 200.00 km e 212.13 km.

Assim, o alcance mínimo necessário é determinado pela maior dessas duas distâncias:

\[
R = \max(200.00,\ 212.13)
\]

\[
\boxed{R = 212.13\text{ km}}
\]

---

## 5. Explicação Individual de uma Etapa no Código (lazy_prim_mst.py)

O trecho central responsável pela seleção das arestas na implementação Prim Lazy é:

```python
while not self._pq.is_empty() and len(self._mst) < G.V() - 1:
    e = self._pq.del_min()
    v = e.either()
    w = e.other(v)

    if not self._marked[v]:
        self._mst.enqueue(e)
        self.visit(v)

    elif not self._marked[w]:
        self._mst.enqueue(e)
        self.visit(w)
```

### Explicação linha por linha

**1. Condição do laço**

```python
while not self._pq.is_empty() and len(self._mst) < G.V() - 1:
```

O algoritmo continua enquanto a fila não estiver vazia e a árvore ainda não possuir \(V-1\) arestas.

**2. Extração da menor aresta**

```python
e = self._pq.del_min()
```

Remove da fila a aresta de menor peso disponível.

**3. Identificação dos extremos**

```python
v = e.either()
w = e.other(v)
```

Obtém os dois vértices conectados pela aresta.

**4. Verificação do primeiro vértice**

```python
if not self._marked[v]:
```

Verifica se o primeiro vértice ainda não foi visitado.

Se não foi visitado, a aresta conecta um vértice novo à árvore.

**5. Inclusão da aresta**

```python
self._mst.enqueue(e)
```

Adiciona a aresta à árvore geradora mínima.

**6. Visitação do vértice**

```python
self.visit(v)
```

Marca o vértice como visitado e adiciona suas arestas incidentes à fila de prioridade.

**7. Verificação do segundo vértice**

```python
elif not self._marked[w]:
```

Caso o primeiro vértice já esteja marcado, verifica se o segundo ainda não foi visitado.

Se estiver livre, a aresta também é válida.

**8. Inclusão e visitação**

```python
self._mst.enqueue(e)
self.visit(w)
```

Adiciona a aresta à árvore e visita o segundo vértice.

Caso os dois extremos já estejam marcados, nenhuma das condições é satisfeita. A aresta é descartada, pois conectaria dois vértices que já pertencem à árvore, formando um ciclo.

---

## 6. Conclusão

O algoritmo Prim Lazy construiu uma Árvore Geradora Mínima para os quatro postos avançados, selecionando as arestas de menor peso que conectam novos vértices à árvore.

A utilização da fila de prioridade mínima permite escolher as conexões de maneira gulosa, enquanto o vetor `marked` controla a visitação dos vértices e evita ciclos.

Após a construção da árvore, a remoção da maior aresta permite utilizar os dois canais de satélite e dividir a rede em duas componentes.

O alcance mínimo necessário para os rádios é de **212.13 km**, resultado obtido com sucesso utilizando o algoritmo Prim Lazy.
