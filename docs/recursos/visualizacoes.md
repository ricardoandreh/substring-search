# Visualizações Interativas

Uma das melhores maneiras de entender como um algoritmo de busca em string funciona é vê-lo em ação. As ferramentas de visualização interativa permitem que você insira seus próprios textos e padrões para observar, passo a passo, como as comparações são feitas, como as estruturas de dados auxiliares são construídas e como os ponteiros se movem.

Utilize os recursos abaixo para aprofundar seu conhecimento durante a fase do **Processo** e para preparar sua **Apresentação Didática**.

---

### **Knuth-Morris-Pratt (KMP)**

Esta visualização é excelente para entender as duas fases do KMP: a construção da tabela de prefixos (LPS - Longest Proper Prefix which is also a Suffix) e a busca no texto utilizando essa tabela para evitar comparações redundantes.

*   **Ferramenta KMP de Hudson:** [https://www.hudsong.dev/tools/kmp](https://www.hudsong.dev/tools/kmp)
   
> **Dica:** Preste atenção em como o ponteiro do padrão "salta" para a posição indicada pela tabela LPS após uma falha na correspondência.

---

### **Aho-Corasick**

O algoritmo Aho-Corasick brilha na busca de múltiplos padrões simultaneamente. A visualização a seguir é fantástica para mostrar como o autômato, uma espécie de "trie" com "links de falha", é construído e utilizado para processar o texto de uma única vez.

*   **AhoCorasick Algorithm Visualizer:** [https://kenneth-ye.github.io/AhoCorasick-Algorithm-Visualizer/](https://kenneth-ye.github.io/AhoCorasick-Algorithm-Visualizer/)

> **Dica:** Observe a construção dos "failure links" (setas azuis). Eles são a chave para a eficiência do algoritmo, indicando para qual estado o autômato deve ir quando uma correspondência falha.
