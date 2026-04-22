# Estudo de Caso A1 — O Problema da Entrega Inteligente
**Unidade Curricular:** Estruturas de Dados e Análise de Algoritmos (0006963)  
**Professor:** Alexandre "Montanha" de Oliveira  
**Nível:** Graduação – Preparatório para a Avaliação A1


**Aluno:** Ramez Marques 
---
## Questão 1 — Classificação do Problema 

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

## Questão 2 — Abordagem Gulosa (Greedy) 

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


---

### b) O algoritmo é **guloso (greedy)** quando toma a **melhor decisão local disponível a cada passo**, sem reconsiderar decisões anteriores e sem olhar para o futuro. A estratégia pressupõe que a sequência de ótimos locais converge para um ótimo global — o que nem sempre é verdade.

Acerca do algoritmo da FastBite, a **propriedade de escolha local** é dupla:

1. **Na atribuição:** "Escolha o entregador mais próximo do restaurante do pedido atual." Isso é ótimo localmente para aquele pedido específico, mas ignora como essa escolha afeta os pedidos seguintes.

2. **No roteamento:** "Vá sempre ao ponto mais próximo da posição atual." Isso é o clássico algoritmo de *nearest neighbor* para o TSP 

Em ambos os casos, a decisão é irrevogável: uma vez atribuído, o pedido não é reatribuído. Essa é a marca registrada dos algoritmos gulosos.


---

### c) Contraexemplo concreto

Considerando uma versão simplificada com 3 pedidos e 2 entregadores (E1 em (0,0) com capacidade 2, e E2 em (10,0) com capacidade 2):

- **P1:** Restaurante em (1,0), cliente em (9,0) — rota longa
- **P2:** Restaurante em (2,0), cliente em (3,0) — rota curta
- **P3:** Restaurante em (9,0), cliente em (11,0) — rota curta, próximo de E2

**Algoritmo guloso:**
- P1: E1 está a distância 1 de (1,0), E2 está a distância 9. → Atribui P1 a E1.
- P2: E1 está a distância 2 de (2,0), E2 está a distância 8. → Atribui P2 a E1. E1 lotado.
- P3: Só E2 disponível. → Atribui P3 a E2.

**Resultado guloso:**
- E1 faz: (0,0)→(1,0)→(2,0)→(3,0)→(9,0) — distância total: 1+1+1+6 = **9**
- E2 faz: (10,0)→(9,0)→(11,0) — distância total: 1+2 = **3**
- **Total: 12**

**Solução melhor (não encontrada pelo guloso):**
- E1 recebe P2 e não P1: (0,0)→(2,0)→(3,0) — distância: 2+1 = **3**
- E2 recebe P1 e P3: (10,0)→(9,0)→(1,0)→(11,0)→... — na verdade seria ineficiente.

Melhor ainda: E1 recebe {P1, P2}, E2 recebe {P3}... como acima. Mas agora considere:
- E2 recebe P1 e P3: (10,0)→(9,0)→(1,0)→(9,0)→(11,0) — custoso.

**Contraexemplo mais direto:** Suponha que o entregador mais próximo de um restaurante já está quase no limite de capacidade e seria muito mais valioso para um pedido urgente futuro que surgirá no mesmo ciclo. O algoritmo guloso "desperdiça" o entregador no pedido atual e deixa o pedido urgente com um entregador mais distante — aumentando o risco de atraso. O guloso não tem visão de futuro para reconhecer isso.


---

### d) Complexidade de tempo do algoritmo guloso


**Fase de atribuição:**

Para cada um dos n pedidos, calculamos a distância a cada um dos m entregadores disponíveis e escolhemos o mínimo. Isso é O(m) por pedido. Para n pedidos: **O(n × m)**.

**Fase de roteamento (nearest neighbor para cada entregador):**

Cada entregador tem no máximo x pontos (onde x ≤ capacidade máxima). O algoritmo de vizinho mais próximo percorre os 2x pontos fazendo a cada passo uma busca linear pelos restantes. Custo por entregador: O(x²). Para m entregadores: **O(m × x²)**, onde x é uma constante pequena (até 3 no caso da FastBite), então isso é O(m).

**Complexidade total:** **O(n × m)** — polinomial e viável em tempo real.

Para a FastBite com n = 50 pedidos e m = 20 entregadores no horário de pico: 50 × 20 = 1.000 operações de comparação — trivialmente executável em milissegundos, bem dentro do limite de 2 segundos.



---

## Questão 3 — Programação Dinâmica e Divisão e Conquista (25 pontos)

### a) Sim, o problema de roteamento de um único entregador com x pedidos (2x pontos a visitar) é uma instância do TSP com restrições de precedência. O TSP admite solução por Programação Dinâmica ( que é a abordagem canônica de PD para esse problema).

1. **Explicação: A rota ótima de A até Z passando por um conjunto S de cidades contém rotas ótimas para os subconjuntos de S. Ou seja, se a rota ótima visita B antes de C, então o trecho de B em diante também é ótimo.

2. **Ao explorar diferentes permutações, o problema de "chegar à cidade X tendo visitado o conjunto S" aparece repetidas vezes. A PD memoiza esses resultados evitando recomputação.

**Custo de memória e tempo:**

- O conjunto S pode ser qualquer subconjunto dos 2x pontos → 2^(2x) subconjuntos possíveis.
- Para cada subconjunto, registramos o custo para cada ponto de chegada → 2x valores.
- **Memória:** O(2^(2x) × 2x) = **O(x × 2^(2x))**
- **Tempo:** Para cada estado, consideramos 2x transições → O(2^(2x) × (2x)²) = **O(x² × 2^(2x))**

**Conclusão: A PD é viável para entregadores com até **x ≈ 10 pedidos**, mas a FastBite permite no máximo 3 pedidos simultâneos. Com x = 3, a PD é perfeitamente aplicável ao problema de roteamento individual. Contudo, o gargalo real é a fase de *atribuição* entre entregadores — problema que a PD sozinha não resolve.

---

### b) Divisão e Conquista aplicado ao roteamento da FastBite

Sim. A ideia central é: se os pedidos de uma região geográfica só são relevantes para entregadores dessa mesma região, então os subproblemas são (aproximadamente) independentes e podem ser resolvidos em paralelo.

**Funcionamneto de divisão Geográfica da Cidade:**

Se colocarmos a cidade como dividida em 4 quadrantes (ou zonas), teriamos o seguinte processo:

1. **Divisão:** Particionar os pedidos e os entregadores por zona geográfica.

2. **Conquista (recursiva):** Resolver o problema de atribuição e roteamento dentro de cada zona de forma independente — usando guloso, PD ou outra heurística.

3. **Combinação:** Agregar as soluções de cada zona como a solução global.


Isso é especialmente poderoso porque permite **paralelismo**: os 4 subproblemas podem ser processados simultaneamente em threads, diminuindo o tempo de resposta.

**Observação:**

Existe uma fragilidade da divisão geográfica está nas **fronteiras entre zonas**:


- Um restaurante pode estar no quadrante NE mas o cliente no SW — o pedido não pertence naturalmente a nenhuma zona.
- Um entregador posicionado exatamente na fronteira pode atender pedidos de múltiplas zonas com custo similar.
- Pedidos urgentes próximos à fronteira podem ser melhor atendidos por um entregador da zona vizinha, mas a divisão impede essa visualização.

No cenário da FastBite, P3 tem restaurante em (4,4) e cliente em (0,0) — se a divisão for feita pelo ponto médio (3,3 ou 4,4), esse pedido cruza zonas e complica a atribuição. A solução é tratar pedidos de fronteira separadamente ou criar uma zona de sobreposição (*buffer zone*), mas isso pode aumentar a complexidade da combinação.

**Resumindo:** A divisão e conquista é uma estratégia estrutural poderosa para escalabilidade,mas não garante a otimalidade global e requer tratamento cuidadoso das fronteiras, devido o que foi exposto acima na parte de observação.


---

## Questão 4 — Comparação das Abordagens 

### Tabela Comparativa:

**Observação:** A tabela comparativa se encontra no pdf


### Análise Crítica:

Para a FastBite, a abordagem mais adequada como **solução principal** é a **gulosa (Greedy)**, complementada por uma etapa de refinamento local. O algoritmo guloso atende ao requisito operacional mais crítico: decidir em até 2 segundos para milhares de pedidos diários, com complexidade O(n × m) que garante resposta em milissegundos mesmo no pico de carga.

O problema mais combinatorialmente explosivo — e o ganho de qualidade para x ≤ 3 (máximo da FastBite) é marginal em relação ao custo de implementação. A Divisão e Conquista é uma excelente estratégia **arquitetural e de escalabilidade**, não um algoritmo de otimização por si só: ela pode ser usada para paralelizar a execução do próprio greedy em múltiplas zonas, acelerando ainda mais o sistema.

Assim dessa maneira a combinação prática ideal seria: **D&C para particionamento geográfico + Greedy para atribuição e roteamento inicial + busca local para refinamento**, tudo com limite de tempo estrito.

---

## Questão 5 — Solução de Engenharia Real 


### a)Uma **heurística** é uma estratégia computacional que busca encontrar **soluções boas o suficiente** para um problema em tempo aceitável, sem garantia de otimalidade. Ela utiliza conhecimento do domínio e intuição sobre a estrutura do problema para guiar a busca de forma inteligente, descartando o espaço de soluções que provavelmente é ruim sem explorá-lo exaustivamente.


Já no contexto da FastBite, as heurísticas são:


1. **Inviabilidade computacional da solução exata:** Como demonstrado, o espaço de soluções cresce fatorialmente. Não existe algoritmo polinomial conhecido para o VRP (e provavelmente não existe, dado que é NP-Completo).

2. **Restrição de tempo real:** O sistema tem 2 segundos para decidir. Uma solução exata para 50 pedidos levaria bilhões de anos.

3. **Valor marginal decrescente da otimalidade:** Uma rota 5% melhor que a heurística economiza segundos de entrega. Uma rota perfeita economiza poucos segundos a mais, mas ao custo de ser computacionalmente impossível.

4. **Variabilidade do ambiente:** O trânsito muda, pedidos chegam, entregadores se movem. Uma solução "ótima" calculada com dados de 30 segundos atrás pode ser pior que uma solução boa calculada com dados atuais.

---

### b) Uma solução robusta poderia ser estruturada em **quatro fases:

**Fase 1 — Particionamento geográfico (Divisão e Conquista):**
Dividir a cidade em zonas (por exemplo, 4 a 9 quadrantes). Pedidos e entregadores são alocados às suas zonas correspondentes. Isso reduz drasticamente o tamanho de cada subproblema e permite execução paralela. Pedidos de fronteira são tratados pela zona mais próxima ou por uma zona de sobreposição configurável.

**Fase 2 — Heurística gulosa como solução inicial:**
Dentro de cada zona, aplica-se o algoritmo guloso de atribuição (entregador mais próximo do restaurante) e roteamento (nearest neighbor). Isso produz rapidamente uma solução viável — não ótima, mas funcional — em O(n × m) por zona.

**Fase 3 — Refinamento local (busca local / 2-opt):**
Com o tempo restante após as fases 1 e 2, aplica-se melhoria iterativa. A técnica mais simples é o *swap* de pedidos: pega-se dois entregadores aleatórios e verifica-se se trocar um pedido entre eles reduz o custo total. Se sim, efetua-se a troca. Outra técnica é o *2-opt*: inverte-se um segmento da rota de um entregador e verifica-se se o custo diminui. Cada iteração tem custo O(n²) mas gera melhorias concretas.

**Fase 4 — Interrupção por tempo (anytime algorithm):**
O sistema monitora o relógio. Ao atingir 1,8 segundos (com margem de segurança), interrompe o refinamento e utiliza a melhor solução encontrada até o momento. Isso garante que, independentemente da complexidade do ciclo, o sistema sempre entrega uma resposta dentro do prazo.

---


### c)Vale a pena buscar a solução exata quando **o espaço de soluções é pequeno o suficiente** para ser explorado dentro do tempo disponível.


**Exemplo:**

Para um **entregador individual com apenas 2 ou 3 pedidos**, a solução exata pode ser calculada em microssegundos por força bruta (6 a 720 permutações), tornando a abordagem ótima trivialmente viável.

---

## Questão 6


O "Bom o suficiente" não é  um sinal de incompetência técnica: é uma decisão de engenharia fundamentada em trade-offs reais. No contexto da FastBite, com 80.000 pedidos diários e uma janela de decisão de 2 segundos, buscar a solução ótima seria tecnicamente inviável e economicamente irracional. O custo computacional de aproximar a otimalidade em cenários NP-Completos cresce exponencialmente, enquanto o ganho de qualidade diminui de forma marginal após certo ponto, então por isso disse dentro de "aspas".

Devido a dificuldade do cenário para simplificarmos e tentarmos chegar a uma solução: Uma empresa de delivery não precisa da rota perfeita: precisa de uma rota boa entregue em 2 segundos, que satisfaça o cliente e seja operacionalmente viável. Uma diferença de 3% no custo da rota impacta marginalmente a experiência do usuário, enquanto uma decisão que demora 5 segundos pode inviabilizar todo o sistema.

