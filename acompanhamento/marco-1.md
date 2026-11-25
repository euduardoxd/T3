# Marco 1 — Formulação do Problema (*Arctic Network*)

## 1. Resumo do Problema

O Departamento de Defesa Nacional precisa interligar um conjunto de postos avançados em uma única rede de comunicação funcional, de modo que qualquer posto consiga alcançar qualquer outro (direta ou indiretamente). Existem duas tecnologias disponíveis:

* **Canais de satélite ($S$):** Permitem comunicação direta entre quaisquer dois postos equipados com esse recurso, independentemente da distância geográfica entre eles.
* **Transceptores de rádio:** Todos os postos possuem rádios idênticos com um mesmo alcance máximo $D$. Dois postos se comunicam por rádio se a distância euclidiana entre eles for menor ou igual a $D$.

Como aumentar o alcance $D$ eleva o custo dos equipamentos, o problema consiste em escolher estrategicamente quais $S$ postos receberão os canais de satélite para minimizar o alcance mínimo $D$ necessário nos rádios para conectar toda a rede.

---

## 2. Entrada, Saída, Objetivo e Restrições

* **Entrada:**
  * Um inteiro $N$ representando o número de casos de teste.
  * Para cada caso de teste:
    * Dois inteiros $S$ (quantidade de canais de satélite disponíveis) e $P$ (quantidade total de postos avançados).
    * $P$ pares de coordenadas inteiras $(x, y)$ em quilômetros, indicando a localização de cada posto no plano 2D.
* **Saída:**
  * Para cada caso de teste, um único número real formatado com **2 casas decimais** indicando o valor mínimo de $D$.
* **Objetivo:**
  * Minimizar o alcance máximo $D$ necessário para que o grafo da rede seja **conexo** (exista pelo menos um caminho de comunicação entre qualquer par de postos), aproveitando de forma ótima os $S$ canais de satélite.
* **Restrições:**
  * Todo posto possui exatamente um transceptor de rádio com o mesmo raio de alcance $D$ (homogeneidade de hardware).
  * No máximo $S$ postos podem receber canais de satélite (os canais permitem comunicação de custo/distância zero entre todos os postos que os possuem).
  * A distância de comunicação via rádio é limitada pela distância euclidiana: $d(u, v) = \sqrt{(x_u - x_v)^2 + (y_u - y_v)^2} \le D$.
  * Limites típicos da entrada: $1 \le S \le 100$, $S < P \le 500$ e coordenadas $0 \le x, y \le 10\,000$.

---

## 3. Modelagem em Grafo

| Elemento | Representação no Problema | Detalhe Técnico |
| :--- | :--- | :--- |
| **Vértices ($V$)** | Postos avançados | $\|V\| = P$, cada vértice $v_i \in V$ localizado em $(x_i, y_i)$ |
| **Arestas ($E$)** | Links possíveis de comunicação via rádio | Grafo completo $K_P$, com $\|E\| = \frac{P(P - 1)}{2}$ |
| **Direção** | Não-direcionado | Comunicação bidirecional e simétrica entre pares de postos |
| **Pesos ($w$)** | Distância euclidiana entre postos | $w(u, v) = \sqrt{(x_u - x_v)^2 + (y_u - y_v)^2}$ *(não há capacidades de fluxo)* |

---

## 4. Identificação Preliminar da Família do Problema

O problema pertence à família de **Árvore Geradora Mínima (MST — *Minimum Spanning Tree*)** — mais especificamente, uma **Floresta Geradora Mínima de $S$ componentes** (ou *Bottleneck Spanning Tree*).

### Justificativa

1. **Conectividade global e gargalo:** Conectar todos os $P$ vértices minimizando a maior aresta utilizada (gargalo $D$) é uma propriedade clássica da MST: toda árvore geradora mínima também minimiza o peso da sua aresta mais pesada.
2. **Efeito dos $S$ canais de satélite:** Como há $S$ canais de satélite disponíveis, é possível conectar até $S$ postos distintos a custo zero entre si. Na prática, isso equivale a permitir que a rede de rádio forme até **$S$ componentes conexas independentes**, bastando atribuir um canal de satélite a um posto representante de cada componente para unificar toda a rede.
3. **Solução ótima:** Ao construir a MST completa de $P - 1$ arestas e remover as **$S - 1$ arestas mais pesadas** (substituindo essas conexões longas pelos canais de satélite), a maior aresta restante na floresta será exatamente o valor mínimo de $D$ — ou seja, a $(P - S)$-ésima menor aresta da MST.
