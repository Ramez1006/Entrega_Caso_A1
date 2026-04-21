# Estudo de Caso A1 — O Problema da Entrega Inteligente
**Unidade Curricular:** Estruturas de Dados e Análise de Algoritmos (0006963)  
**Professor:** Alexandre "Montanha" de Oliveira  
**Nível:** Graduação – Preparatório para a Avaliação A1
** Aluno:** Ramez Marques 
---
## Questão 1 — Classificação do Problema (20 pontos)

### a) Classificação:
O problema de roteamento da FastBite pertence à classe **NP-Completo**, porque: À medida que o número de clientes aumenta, o número de rotas possíveis cresce de forma fatorial.
E Pelo fato de ter dado a dica de  exemplo dos seguintes problemas, que não tem solução, que são eles: Problema do caixeiro viajante, problema de roteamento de veiculos. Nos quais não há solução.
Assim como também de acordo com o enunciado, é possível verificar em tempo polinomial se todas as restrições são satisfeitas (capacidade máxima, prazo dos pedidos urgentes, tempo total de entrega) e calcular o custo total da solução.

Portanto, o problema não pode ser resolvido de forma exata em tempo polinomial (assumindo P ≠ NP), e encontrar a solução ótima requer, no pior caso, a exploração exponencial do espaço de soluções.

---

### b) Redução intuitiva do problema da FastBite ao TSP
 
Lembrando que o problema do caixeiro viajante (TSP) é dado como: um conjunto de cidades e as distâncias entre cada par delas, encontrar o menor circuito ou seja, a rota que visita cada cidade exatamente uma vez e retorna ao ponto de partida com custo mínimo.

**Transformando uma instância da FastBite em uma instância do TSP:**

Considerando um único entregador que recebeu x pedidos já atribuídos. O subproblema que ele precisa resolver é: em qual ordem visitar os x restaurantes (coleta) e os x clientes (entrega), respeitando que a coleta de um pedido deve preceder sua entrega?

Podemos modelar isso como: 
-Cada **nós** que simboliza o nó ou ponto relevante da posição inicial do entregador, os x restaurantes e os x clientes — totalizando 2x + 1 nós.

-Cada **aresta** entre dois nós tem peso igual à distância  entre eles.

-O objetivo é encontrar o **caminho de custo mínimo** que visita todos os nós respeitando as precedências (coleta antes da entrega).

**Exemplo com os dados do cenário mencionado:**

Suponha que o  entregador esteja no F3 (posição 7,6) poderia receber os pedidos P3, P4 e P5. Os nós do grafo seriam:
`F3(7,6) → R_P3(4,4) → R_P4(6,1) → R_P5(5,5) → C_P3(0,0) → C_P4(3,6) → C_P5(8,1)`

Isso demonstra exatamente o TSP com restrições de precedência, no qual ao transformar o ponto de partida em ponto de retorno, obtemos o TSP clássico. Assim, qualquer instância do TSP pode ser codificada como uma instância do roteamento da FastBite (com um único entregador e sem restrições de janela de tempo), e qualquer instância da FastBite pode ser reduzida a múltiplas instâncias do TSP.

