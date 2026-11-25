# Marco 2 — Escolha do Algoritmo (*Arctic Network* — Prim Não Otimizado / *Lazy*)

## 1. Escolha e Justificativa do Algoritmo

Como identificado no Marco 1, o problema pertence à classe de **Árvore Geradora Mínima (MST — *Minimum Spanning Tree*)** na variação de **Floresta Geradora Mínima de $S$ componentes / Árvore Geradora de Gargalo**.

* **Algoritmo escolhido:** **Algoritmo de Prim (versão *Lazy* / não otimizada)** utilizando uma fila de prioridade (*Min-Heap*).

### Justificativa da escolha em relação às outras classes

* **Por que NÃO Dijkstra?** Dijkstra resolve o problema de *Caminho Mínimo de Fonte Única*, minimizando a soma acumulada das distâncias desde um vértice inicial até cada destino. No *Arctic Network*, o objetivo não é calcular rotas acumuladas, mas sim conectar globalmente todos os $P$ postos minimizando o maior salto individual (gargalo $D$).
* **Por que NÃO Ford-Fulkerson / Edmonds-Karp?** Esses algoritmos tratam de *Fluxo Máximo* em redes capacitadas entre fonte e sumidouro. O problema não possui fluxo de dados nem restrições de capacidade nas arestas.
* **Por que Prim *Lazy* e como adaptá-lo aos $S$ satélites?**
  1. O Prim *Lazy* constrói a MST expandindo a árvore a partir de um vértice arbitrário (ex: $v_1$). A cada passo, todas as arestas incidentes ao vértice recém-visitado são inseridas em uma fila de prioridade mínima (*Min-Heap*), mantendo inclusive arestas obsoletas cujas duas extremidades já foram visitadas (por isso o termo *lazy*, pois o descarte só ocorre no momento da remoção da *heap*).
  2. Como o grafo do problema é **completo** ($K_P$), não há necessidade de armazenar uma lista de adjacência prévia: ao visitar um vértice $u$, calcula-se sua distância euclidiana para todos os vértices $v$ ainda não visitados e insere-se $(w(u,v), v)$ na *heap*.
  3. Ao final da execução completa do Prim, obtemos as **$P - 1$ arestas da MST**. Como possuímos $S$ canais de satélite, podemos substituir as **$S - 1$ arestas mais pesadas** da MST por conexões via satélite (de custo zero), dividindo a árvore em $S$ componentes conexas. Assim, basta **ordenar as $P - 1$ arestas da MST obtida** e selecionar a $(P - S)$-ésima menor aresta (ou remover as $S - 1$ maiores) para obter o alcance mínimo $D$.

---

## 2. Representação da Instância Pequena (Exemplo 1)

Considerando o caso de teste fornecido no enunciado:

* **Parâmetros:** $S = 2$ canais de satélite, $P = 4$ postos avançados.
* **Vértices ($V$):**
  * $v_1 = (0, 100)$
  * $v_2 = (0, 300)$
  * $v_3 = (0, 600)$
  * $v_4 = (150, 750)$

### Execução Passo a Passo do Prim *Lazy* (iniciando em $v_1$)

| Passo | Vértice Visitado | Conteúdo da *Min-Heap* $(peso, destino)$ após inserções | Aresta Extraída da *Heap* | Status / Ação |
| :---: | :---: | :--- | :---: | :--- |
| **1** | $v_1$ | $(200.00, v_2)$, $(500.00, v_3)$, $(667.08, v_4)$ | $(200.00, v_2)$ | $v_2$ não visitado $\rightarrow$ **Aceita aresta $(v_1, v_2) = 200.00$** |
| **2** | $v_2$ | $(300.00, v_3)$, $(474.34, v_4)$, $(500.00, v_3)$, $(667.08, v_4)$ | $(300.00, v_3)$ | $v_3$ não visitado $\rightarrow$ **Aceita aresta $(v_2, v_3) = 300.00$** |
| **3** | $v_3$ | $(212.13, v_4)$, $(474.34, v_4)$, $(500.00, v_3)$, $(667.08, v_4)$ | $(212.13, v_4)$ | $v_4$ não visitado $\rightarrow$ **Aceita aresta $(v_3, v_4) = 212.13$** |
| **4** | — | $(474.34, v_4)$, $(500.00, v_3)$, $(667.08, v_4)$ | — | Todos os $4$ vértices visitados ($3$ arestas na MST). Encerra. *(Entradas restantes na heap são descartadas)*. |

### Aplicação dos $S = 2$ Canais de Satélite sobre a MST

* **Arestas da MST obtida:** $\{200.00, 300.00, 212.13\}$
* **Ordenando as arestas da MST:** $[200.00,\; 212.13,\; 300.00]$
* **Remoção das $S - 1 = 1$ maiores arestas:** Remove-se a aresta de peso `300.00` (ligação entre $v_2$ e $v_3$, substituída pelos satélites).
* **Maior aresta restante (gargalo $D$):** `212.13`.

---

## 3. Validação da Entrada e Saída Esperada

* **Entrada validada:**
  ```text
  1
  2 4
  0 100
  0 300
  0 600
  150 750
