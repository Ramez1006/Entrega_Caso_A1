# Estudo de Caso A1 — O Problema da Entrega Inteligente
**Unidade Curricular:** Estruturas de Dados e Análise de Algoritmos (0006963)  
**Professor:** Alexandre "Montanha" de Oliveira  
**Nível:** Graduação – Preparatório para a Avaliação A1


**Aluno:** Ramez Marques 
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

---

### c) Inviabilidade da força bruta — cálculo combinatório


**Cenário Apresentado:** 8 pedidos, 3 entregadores, cada um com capacidade para até 3 pedidos.

Supondo as quantidades de permutações possiveis de entrega, dadas acima, precisamos distribuir 8 pedidos entre 3 entregadores, com cada um recebendo no máximo 3. Uma distribuição possível é, por exemplo, (3, 3, 2). O número de maneiras de fazer essa distribuição é dado pela:

C(8,3) × C(5,3) × C(2,2) = 56 × 10 × 1 = 560 combinações de atribuição 

**Roteamento de cada entregador:**

Após a atribuição, cada entregador precisa definir a ordem de coleta e entrega dos seus pedidos. Para um entregador com x pedidos, há 2x pontos a visitar (x restaurantes + x clientes), mas com restrição de precedência. O número de permutações possíveis é limitado por (2x)! no pior caso, mas mesmo considerando as restrições, permanece exponencial.

- Entregador com 3 pedidos: até 6 pontos → 6! / (fator de precedência) ≈ **720 / 8 = 90** rotas válidas, mas na ordem de grandeza de centenas.
- Entregador com 2 pedidos: até 4 pontos → 4! = **24** permutações.



**Cálculo total:**

Total ≈ 560 (atribuições) × 90 × 90 × 24 (rotas) ≈ 560 × 194.400 ≈ 108.864.000

**Complexidade assintótica:** O(n! × m^n) onde n é o número de pedidos e m é o número de entregadores — intratável para qualquer instância realista.

**Conclusão:** A força bruta é absolutamente inviável para a FastBite. Mesmo com 8 pedidos, o número de combinações já excede 10⁸, e o sistema precisa decidir em até 2 segundos.


---

## Questão 2 — Abordagem Gulosa (Greedy) (25 pontos)

**Parte de Atribuição:**

1. Lista-se todos os pedidos não atribuídos exemplos: {P1, P2, P3, P4, P5}.
2. Para cada pedido (em ordem de prioridade ou chegada), calcula-se a distância Manhattan de cada entregador disponível (com capacidade restante) ao restaurante do pedido.
3. Atribui-se o pedido ao entregador mais próximo do restaurante correspondente.

Cálculo  do cenário sendo aplicado:

**Pedido P1** — Restaurante em (0,2):
- E1 em (1,1): |0-1| + |2-1| = 1 + 1 = **2**
- E2 em (5,3): |0-5| + |2-3| = 5 + 1 = **6**
- E3 em (7,6): |0-7| + |2-6| = 7 + 4 = **11**
- → **P1 atribuído a E1** (distância 2). E1 agora tem 1/2 pedidos.

**Pedido P2** — Restaurante em (1,1):
- E1 em (1,1): |1-1| + |1-1| = **0** (já está lá)
- E2 em (5,3): **6**
- E3 em (7,6): **11**
- → **P2 atribuído a E1** (distância 0). E1 agora tem 2/2 pedidos — capacidade esgotada.

**Pedido P3** — Restaurante em (4,4):
- E2 em (5,3): |4-5| + |4-3| = 1 + 1 = **2**
- E3 em (7,6): |4-7| + |4-6| = 3 + 2 = **5**
- → **P3 atribuído a E2** (distância 2). E2 agora tem 1/2 pedidos.

**Pedido P4** — Restaurante em (6,1):
- E2 em (5,3): |6-5| + |1-3| = 1 + 2 = **3**
- E3 em (7,6): |6-7| + |1-6| = 1 + 5 = **6**
- → **P4 atribuído a E2** (distância 3). E2 agora tem 2/2 — capacidade esgotada.

**Pedido P5** — Restaurante em (5,5):
- E3 em (7,6): |5-7| + |5-6| = 2 + 1 = **3**
- → **P5 atribuído a E3** (único disponível).


**Atribuição final:** E1: {P1, P2} | E2: {P3, P4} | E3: {P5}


Cada entregador percorre seus pontos indo sempre ao ponto mais próximo de sua posição atual.

**E1** (posição inicial 1,1), pedidos P1 e P2:
- Pontos a visitar: R_P1(0,2), C_P1(4,5), R_P2(1,1), C_P2(7,2)
- De (1,1): mais próximo é R_P2(1,1) com distância 0 → visita R_P2 (coleta P2)
- De (1,1): mais próximo dos restantes é R_P1(0,2) com distância 2 → visita R_P1 (coleta P1)
- De (0,2): mais próximo é C_P2(7,2) com distância 7 → visita C_P2 (entrega P2)
- De (7,2): visita C_P1(4,5) com distância 6 → entrega P1
- Rota: (1,1)→(1,1)→(0,2)→(7,2)→(4,5) — distância total: 0+2+7+6 = **15**
