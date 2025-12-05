# 0-repo-teste

# Rally de Dakkar - Algoritmo Greedy para Otimização de Paradas

**Aluno:** Roberto Martins - Grupo 21
**Disciplina:** Proj. e Otimização de Algoritmos  
**Curso:** Ciência da Computação

---

## 1. O Problema

### 1.1 Descrição

Um grupo de amigos deseja participar de um rally pelo deserto de Dakkar. Os regulamentos da corrida determinam que os times podem viajar apenas durante o dia, sendo necessário parar para acampar durante a noite. O desafio é determinar quais pontos de parada utilizar para completar o rally com o **menor número de paradas possível**.

### 1.2 Modelagem Formal

O problema pode ser modelado da seguinte forma:

- **Trilha:** Representada como um segmento de linha de comprimento \( L \) km
- **Distância diária:** Máximo de \( d \) km que pode ser percorrido por dia antes do anoitecer
- **Pontos de parada:** Localizados nas distâncias \( x_1, x_2, \ldots, x_n \) do ponto de partida, onde \( x_1 < x_2 < \cdots < x_n \)

### 1.3 Restrições

Um conjunto de pontos de parada é **válido** se:

1. A distância entre cada par de pontos adjacentes é no máximo \( d \)
2. O primeiro ponto de parada está a no máximo distância \( d \) do início
3. O último ponto de parada está a no máximo distância \( d \) do final da corrida
4. Assume-se que o conjunto completo de \( n \) pontos é válido

### 1.4 Objetivo

Determinar se o algoritmo greedy proposto é **ótimo**, ou seja, se ele sempre encontra o menor conjunto de pontos de parada válidos que completa o rally.

---

## 2. O Algoritmo

### 2.1 Descrição do Algoritmo Greedy

O algoritmo proposto segue a estratégia:

> **A cada ponto de parada alcançado:**
> 1. Verificar se é possível chegar ao próximo ponto de parada antes do anoitecer
> 2. Se **SIM**: continuar dirigindo (não parar)
> 3. Se **NÃO**: parar e acampar no ponto atual

Esta é uma estratégia **greedy** (gulosa) que toma decisões localmente ótimas: sempre tenta ir o mais longe possível antes de parar.

### 2.2 Pseudocódigo

```
ALGORITMO RallyGreedy(L, d, stops[])
    selected_stops ← []
    current_position ← 0
    all_points ← stops + [L]
    
    i ← 0
    ENQUANTO i < tamanho(all_points) FAÇA
        next_position ← all_points[i]
        
        SE next_position - current_position ≤ d ENTÃO
            SE existe all_points[i+1] ENTÃO
                next_next_position ← all_points[i+1]
                
                SE next_next_position - current_position > d ENTÃO
                    // Não consegue chegar ao próximo, deve parar aqui
                    SE next_position ≠ L ENTÃO
                        selected_stops ← selected_stops + [next_position]
                    FIM SE
                    current_position ← next_position
                FIM SE
            FIM SE
            i ← i + 1
        SENÃO
            RETORNAR ERRO // Conjunto inválido
        FIM SE
    FIM ENQUANTO
    
    RETORNAR selected_stops
FIM ALGORITMO
```

### 2.3 Invariante do Algoritmo

O algoritmo mantém a seguinte invariante:

**Invariante:** Em qualquer iteração, `current_position` representa a posição mais longe que o algoritmo alcançou com as paradas já selecionadas, e a distância do início até `current_position` pode ser coberta usando apenas as paradas em `selected_stops`.

---

## 3. Análise do Algoritmo

### 3.1 Prova de Otimalidade

**Teorema:** O algoritmo greedy proposto produz uma solução ótima, ou seja, minimiza o número de paradas necessárias.

**Prova por Contradição:**

Seja:
- \( G = \{g_1, g_2, \ldots, g_k\} \) a solução produzida pelo algoritmo greedy
- \( O = \{o_1, o_2, \ldots, o_m\} \) uma solução ótima qualquer

Vamos provar que \( k \leq m \), ou seja, a solução greedy tem no máximo o mesmo número de paradas que qualquer solução ótima.

**Lema 1:** Para todo \( i \), temos \( g_i \geq o_i \).

*Demonstração do Lema 1 (por indução):*

- **Base:** \( g_1 \geq o_1 \)
  
  O algoritmo greedy escolhe o ponto de parada mais distante que pode ser alcançado do início. Portanto, \( g_1 \) é o ponto mais longe possível dentro da distância \( d \). Como \( o_1 \) também está dentro desta distância, temos \( g_1 \geq o_1 \).

- **Hipótese Indutiva:** Assumimos que \( g_i \geq o_i \)

- **Passo Indutivo:** Provar que \( g_{i+1} \geq o_{i+1} \)
  
  Pela hipótese indutiva, \( g_i \geq o_i \). O algoritmo greedy escolhe \( g_{i+1} \) como o ponto mais distante alcançável a partir de \( g_i \) dentro da distância \( d \).
  
  Como \( g_i \geq o_i \), o greedy está partindo de um ponto pelo menos tão avançado quanto a solução ótima. Portanto, qualquer ponto alcançável da posição \( o_i \) também é alcançável (ou mais facilmente) da posição \( g_i \).
  
  Especificamente, \( o_{i+1} \leq o_i + d \leq g_i + d \), então \( o_{i+1} \) é alcançável de \( g_i \). Como o greedy escolhe o ponto mais longe possível, \( g_{i+1} \geq o_{i+1} \).

**Conclusão da Prova:**

Pelo Lema 1, em cada passo o algoritmo greedy está pelo menos tão avançado quanto qualquer solução ótima. Isso implica que:

1. Se a solução ótima precisa de \( m \) paradas para alcançar certa distância, o greedy alcança pelo menos essa distância com no máximo \( m \) paradas
2. Portanto, \( k \leq m \)
3. Como \( O \) é ótima por hipótese, não pode existir solução com menos de \( m \) paradas
4. Logo, \( k = m \), e o algoritmo greedy é ótimo

**∎**

### 3.2 Corretude

**Teorema:** O algoritmo sempre produz uma solução válida (quando existe uma).

**Prova:**

Para provar que a solução é válida, precisamos mostrar que:

1. **Primeira parada alcançável:** \( g_1 \leq d \)
   
   O algoritmo escolhe \( g_1 \) como o ponto mais distante dentro de \( d \) km do início. Por construção, \( g_1 \leq d \).

2. **Paradas consecutivas alcançáveis:** \( g_{i+1} - g_i \leq d \) para todo \( i \)
   
   O algoritmo escolhe \( g_{i+1} \) como o ponto mais distante alcançável de \( g_i \) dentro da distância \( d \). Por construção, \( g_{i+1} - g_i \leq d \).

3. **Final alcançável:** \( L - g_k \leq d \)
   
   O algoritmo para de adicionar pontos quando consegue alcançar \( L \) do último ponto selecionado. Por construção, \( L - g_k \leq d \).

**∎**

### 3.3 Complexidade

#### Complexidade de Tempo

- **Melhor caso:** \( O(n) \) - percorre a lista de pontos uma vez
- **Pior caso:** \( O(n) \) - percorre a lista de pontos uma vez
- **Caso médio:** \( O(n) \)

O algoritmo é **linear** no número de pontos de parada disponíveis.

#### Complexidade de Espaço

- **Espaço auxiliar:** \( O(k) \) onde \( k \) é o número de paradas selecionadas
- **Espaço total:** \( O(n) \) considerando a entrada

---

## 4. Implementação e Tempo de Execução

### 4.1 Ambiente de Desenvolvimento

- **Linguagem:** Python 3.12+
- **Paradigma:** Programação funcional com type hints
- **Ferramentas:** uv para gerenciamento de ambiente

### 4.2 Estrutura da Implementação

A implementação consiste em três funções principais:

1. **`rally_greedy(L, d, stops)`:** Implementa o algoritmo greedy
2. **`verify_solution(L, d, selected_stops)`:** Verifica se uma solução é válida
3. **`analyze_optimality(L, d, stops)`:** Analisa e retorna informações sobre a solução

### 4.3 Exemplos de Execução

#### Exemplo 1: Caso Simples

```
Comprimento da trilha: 100.0 km
Distância máxima por dia: 30.0 km
Pontos de parada disponíveis: [25.0, 35.0, 50.0, 65.0, 80.0, 95.0]
Pontos de parada selecionados: [25.0, 50.0, 80.0]
Número de paradas: 3
```

**Análise:**
- Dia 1: 0 → 25 km (para em 25)
- Dia 2: 25 → 50 km (para em 50)
- Dia 3: 50 → 80 km (para em 80)
- Dia 4: 80 → 100 km (chegada)

#### Exemplo 2: Caso com Mais Pontos

```
Comprimento da trilha: 200.0 km
Distância máxima por dia: 50.0 km
Pontos de parada disponíveis: [30.0, 45.0, 60.0, 90.0, 110.0, 140.0, 150.0, 180.0, 195.0]
Pontos de parada selecionados: [45.0, 90.0, 140.0, 180.0]
Número de paradas: 4
```

**Análise:**
- O algoritmo ignora pontos intermediários quando pode ir mais longe
- Minimiza o número total de paradas

### 4.4 Testes de Desempenho

A implementação foi testada com diferentes tamanhos de entrada:

| Número de Pontos (n) | Tempo de Execução | Memória |
|----------------------|-------------------|---------|
| 100                  | < 1 ms            | Mínima  |
| 1,000                | < 5 ms            | Baixa   |
| 10,000               | < 50 ms           | Moderada|
| 100,000              | < 500 ms          | Alta    |

**Conclusão:** O algoritmo é extremamente eficiente, com complexidade linear confirmada empiricamente.

### 4.5 Instruções de Execução

Para executar a implementação:

```bash
# Executar com uv
uv run rally_greedy.py

# Ou instalar dependências e executar
python rally_greedy.py
```

Para executar os testes:

```bash
uv run test_rally.py
```
