# Desafios Online: Teste suas Habilidades

Depois de implementar os algoritmos, é hora de colocá-los à prova em cenários reais. As plataformas de programação competitiva como Beecrowd e HackerRank oferecem problemas com grandes volumes de dados, onde uma solução ingênua (Força Bruta) certamente falhará por exceder o tempo limite de execução (Time Limit Exceeded - TLE).

Use estes desafios para afiar suas habilidades, entender as complexidades na prática e preparar-se para a análise comparativa da sua **Tarefa**.

---

### **Aquecimento (Ideal para Naive ou Lógica Simples)**

Estes problemas geralmente têm limites de entrada menores ou podem ser resolvidos com manipulações de string diretas, sendo um bom ponto de partida.

#### **1. Ajuste ou Não Ajuste II (Beecrowd 1241)**

*   **Link:** [https://www.beecrowd.com.br/judge/pt/problems/view/1241](https://www.beecrowd.com.br/judge/pt/problems/view/1241)
*   **Por que este problema?** É um exercício fundamental para verificar se uma string é um sufixo de outra. Ajuda a aquecer a lógica de comparação de strings caractere por caractere a partir do final.
*   **Dica de Algoritmo:** Uma simples iteração de trás para frente, comparando os caracteres, é suficiente e eficiente para passar nos casos de teste.

#### **2. Find a string (HackerRank - Easy)**

*   **Link:** [https://www.hackerrank.com/challenges/find-a-string/problem](https://www.hackerrank.com/challenges/find-a-string/problem)
*   **Por que este problema?** É a tarefa mais clássica de busca: contar o número de ocorrências de uma sub-string dentro de um texto. Os limites são pequenos, permitindo que uma solução Naive passe sem problemas.
*   **Dica de Algoritmo:** A abordagem de Força Bruta, que desliza uma "janela" do tamanho do padrão sobre o texto, é perfeita para este desafio.

---

### **Desafios Intermediários (Ideal para KMP ou Rabin-Karp)**

Aqui a eficiência se torna crucial. Uma solução O(N*M) não passará. Você precisará de um algoritmo de busca linear, como KMP ou Rabin-Karp.

#### **1. String Periódica (Beecrowd 1308)**

*   **Link:** [https://www.beecrowd.com.br/judge/pt/problems/view/1308](https://www.beecrowd.com.br/judge/pt/problems/view/1308)
*   **Por que este problema?** Este é um problema clássico que testa o seu entendimento sobre a estrutura interna do KMP. Ele pede o tamanho do menor bloco que, repetido, forma a string inteira.
*   **Dica de Algoritmo:** A resposta está diretamente relacionada ao valor do último elemento do array de prefixo (LPS) do KMP. Se `n` é o tamanho da string e `lps[n-1]` é o último valor do array, o tamanho do período será `n - lps[n-1]` se `n` for divisível por esse valor.

#### **2. Find a string in a grid (HackerRank - Medium)**

*   **Link:** [https://www.hackerrank.com/challenges/grid-search/problem](https://www.hackerrank.com/challenges/grid-search/problem)
*   **Por que este problema?** Eleva o desafio para um grid 2D. Você precisa encontrar um padrão que também é um grid. Requer adaptar a lógica de busca para todas as 8 direções (horizontal, vertical e diagonais) a partir de cada célula.
*   **Dica de Algoritmo:** Embora seja possível com uma busca em profundidade (DFS), você pode otimizar a busca em cada direção usando KMP ou Rabin-Karp para evitar re-comparações em uma linha, coluna ou diagonal.

---

### **Desafios Avançados (Ideal para Aho-Corasick)**

Estes problemas envolvem buscar múltiplos padrões em um único texto. Usar KMP para cada padrão seria muito lento. É aqui que o Aho-Corasick mostra seu poder.

#### **1. HAY POINTS (Beecrowd 1261)**

*   **Link:** [https://www.beecrowd.com.br/judge/pt/problems/view/1261](https://www.beecrowd.com.br/judge/pt/problems/view/1261)
*   **Por que este problema?** Você recebe um dicionário de palavras com valores e depois precisa ler vários textos, somando os valores das palavras do dicionário encontradas. É um cenário perfeito para Aho-Corasick.
*   **Dica de Algoritmo:** Construa um autômato de Aho-Corasick com todas as palavras do dicionário. Para cada texto, processe-o com o autômato uma única vez, acumulando os valores sempre que um nó final (que representa uma palavra) for atingido.

#### **2. Determining DNA Health (HackerRank - Hard)**

*   **Link:** [https://www.hackerrank.com/challenges/determining-dna-health/problem](https://www.hackerrank.com/challenges/determining-dna-health/problem)
*   **Por que este problema?** Este é considerado o problema canônico para Aho-Corasick no HackerRank. Você tem um conjunto de genes (padrões) com valores de "saúde", e precisa calcular a saúde total de trechos de um grande filamento de DNA (texto). Os limites são enormes e exigem uma implementação altamente otimizada.
*   **Dica de Algoritmo:** A solução envolve construir o autômato Aho-Corasick. Uma otimização importante é, ao percorrer os "failure links" após encontrar uma correspondência, continuar subindo na cadeia de falhas para acumular os valores de todos os padrões que terminam naquela posição (ex: se encontrou "apple", também encontrou "ple").
