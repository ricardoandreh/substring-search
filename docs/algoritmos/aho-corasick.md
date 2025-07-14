# Aho-Corasick Algorithm

## Introdução

O algoritmo Aho-Corasick é uma extensão poderosa do algoritmo KMP que permite buscar múltiplos padrões simultaneamente em um texto. Desenvolvido por Alfred Aho e Margaret Corasick em 1975, este algoritmo é amplamente utilizado em sistemas de detecção de malware, filtros de conteúdo, análise de logs e bioinformática.

!!! info "Características Principais"
    - **Busca múltiplos padrões** em uma única passagem
    - **Complexidade temporal linear** O(n + m + z)
    - **Baseado em uma trie** com links de falha
    - **Não há retrocessos** durante a busca

## Conceito e Motivação

### Problema
Imagine que você precisa verificar se um texto contém qualquer uma das seguintes palavras proibidas: `["spam", "scam", "phishing", "malware"]`. Com algoritmos tradicionais, você precisaria fazer 4 buscas separadas. O Aho-Corasick resolve isso em uma única passagem!

### Como Funciona
O algoritmo constrói uma **trie** (árvore de prefixos) dos padrões e adiciona **links de falha** que permitem transições eficientes quando um padrão não é encontrado.

### Estrutura de Dados
```
Trie + Failure Links + Output Function = Aho-Corasick Automaton
```

## Implementação Detalhada

### Inicialização da Classe

```python
class AhoCorasick:
    def __init__(self,
                 patterns: List[str],
                 case_sensitive: bool = True,
                 only_whole_words: bool = False):
        """
        Inicializa o automato Aho-Corasick.
        """
        self.case_sensitive = case_sensitive
        self.only_whole_words = only_whole_words
        
        # Validação e preprocessamento dos padrões
        self.patterns = self._validate_and_preprocess_patterns(patterns)
        
        if not self.patterns:
            raise ValueError("Nenhum padrão válido fornecido")
        
        # Estruturas do automato
        self.goto = {}              # Função de transição
        self.fail = {}              # Função de falha
        self.output = defaultdict(set)  # Função de saída
        
        # Construção do automato
        self._build_goto_function()
        self._build_failure_function()
```

**Explicação linha por linha:**

1. **Parâmetros de configuração:**
   - `case_sensitive`: Controla se a busca diferencia maiúsculas/minúsculas
   - `only_whole_words`: Define se deve buscar apenas palavras completas

2. **Estruturas principais:**
   - `self.goto = {}`: Função de transição - mapeia (estado, caractere) → próximo estado
   - `self.fail = {}`: Função de falha - mapeia estado → estado de fallback
   - `self.output = defaultdict(set)`: Função de saída - mapeia estado → conjunto de padrões encontrados

3. **Construção do automato:**
   - `_build_goto_function()`: Constrói a trie dos padrões
   - `_build_failure_function()`: Adiciona os links de falha

### Validação e Preprocessamento

```python
def _validate_and_preprocess_patterns(self, patterns: List[str]) -> List[str]:
    """Valida e preprocessa os padrões de entrada."""
    if not patterns:
        return []
    
    processed = []
    seen = set()
    
    for pattern in patterns:
        if not isinstance(pattern, str):
            continue
        
        # Preprocessamento baseado nas configurações
        if not self.case_sensitive:
            pattern = pattern.lower()
        
        # Remove duplicatas
        if pattern not in seen:
            processed.append(pattern)
            seen.add(pattern)
    
    return processed
```

**Explicação detalhada:**

1. **Validação básica (linhas 3-4):**
   - `if not patterns:` - Verifica se a lista não está vazia
   - `return []` - Retorna lista vazia se não há padrões

2. **Inicialização (linhas 6-7):**
   - `processed = []` - Lista para padrões processados
   - `seen = set()` - Conjunto para detectar duplicatas

3. **Processamento de cada padrão (linhas 9-10):**
   - `for pattern in patterns:` - Itera sobre cada padrão
   - `if not isinstance(pattern, str):` - Verifica se é string

4. **Normalização (linhas 13-14):**
   - `if not self.case_sensitive:` - Se não diferencia maiúsculas/minúsculas
   - `pattern = pattern.lower()` - Converte para minúsculas

5. **Remoção de duplicatas (linhas 16-19):**
   - `if pattern not in seen:` - Se ainda não foi processado
   - `processed.append(pattern)` - Adiciona à lista final
   - `seen.add(pattern)` - Marca como processado

### Construção da Função Goto (Trie)

```python
def _build_goto_function(self):
    """Constrói a função goto (trie)."""
    # Estado inicial
    self.goto[0] = {}
    state_count = 1
    
    # Constrói a trie
    for pattern_idx, pattern in enumerate(self.patterns):
        current_state = 0
        
        for char in pattern:
            if char not in self.goto[current_state]:
                self.goto[current_state][char] = state_count
                self.goto[state_count] = {}
                state_count += 1
            
            current_state = self.goto[current_state][char]
        
        # Marca o estado final
        self.output[current_state].add(pattern_idx)
```

**Explicação linha por linha:**

1. **Inicialização (linhas 3-4):**
   - `self.goto[0] = {}` - Estado inicial (raiz) sem transições
   - `state_count = 1` - Contador para próximos estados

2. **Para cada padrão (linhas 7-8):**
   - `for pattern_idx, pattern in enumerate(self.patterns):` - Itera com índice
   - `current_state = 0` - Inicia na raiz

3. **Construção do caminho (linhas 10-15):**
   - `for char in pattern:` - Para cada caractere do padrão
   - `if char not in self.goto[current_state]:` - Se transição não existe
   - `self.goto[current_state][char] = state_count` - Cria transição
   - `self.goto[state_count] = {}` - Inicializa novo estado
   - `state_count += 1` - Incrementa contador

4. **Avanço no automato (linha 15):**
   - `current_state = self.goto[current_state][char]` - Move para próximo estado

5. **Marca fim do padrão (linha 18):**
   - `self.output[current_state].add(pattern_idx)` - Adiciona padrão ao estado final

### Construção da Função de Falha

```python
def _build_failure_function(self):
    """Constrói a função de falha usando BFS."""
    # Inicializa a função de falha
    self.fail[0] = 0
    
    # Fila para BFS
    queue = deque()
    
    # Estados de profundidade 1 têm falha para o estado 0
    for char, state in self.goto[0].items():
        self.fail[state] = 0
        queue.append(state)
    
    # Processa os demais estados
    while queue:
        current_state = queue.popleft()
        
        for char, next_state in self.goto[current_state].items():
            queue.append(next_state)
            
            # Encontra o estado de falha
            failure_state = self.fail[current_state]
            
            while failure_state != 0 and char not in self.goto[failure_state]:
                failure_state = self.fail[failure_state]
            
            if char in self.goto[failure_state]:
                failure_state = self.goto[failure_state][char]
            
            self.fail[next_state] = failure_state
            
            # Combina as saídas
            self.output[next_state].update(self.output[failure_state])
```

**Explicação detalhada:**

1. **Inicialização (linhas 3-6):**
   - `self.fail[0] = 0` - Raiz tem falha para si mesma
   - `queue = deque()` - Fila para processamento BFS

2. **Estados de profundidade 1 (linhas 8-11):**
   - `for char, state in self.goto[0].items():` - Filhos diretos da raiz
   - `self.fail[state] = 0` - Todos apontam para a raiz
   - `queue.append(state)` - Adiciona à fila

3. **Processamento BFS (linhas 13-15):**
   - `while queue:` - Enquanto há estados para processar
   - `current_state = queue.popleft()` - Remove próximo estado
   - `for char, next_state in self.goto[current_state].items():` - Para cada transição

4. **Encontra estado de falha (linhas 19-21):**
   - `failure_state = self.fail[current_state]` - Inicia no failure do pai
   - `while failure_state != 0 and char not in self.goto[failure_state]:` - Busca onde o caractere existe
   - `failure_state = self.fail[failure_state]` - Segue cadeia de failures

5. **Define failure link (linhas 23-26):**
   - `if char in self.goto[failure_state]:` - Se encontrou transição válida
   - `failure_state = self.goto[failure_state][char]` - Aponta para o estado correspondente
   - `self.fail[next_state] = failure_state` - Define o failure link

6. **Combina saídas (linha 29):**
   - `self.output[next_state].update(self.output[failure_state])` - Herda padrões do failure state

### Algoritmo de Busca

```python
def search(self, text: str) -> Dict[str, List[Tuple[int, int]]]:
    """
    Busca todos os padrões no texto.
    
    Returns:
        Dicionário com padrão -> lista de (início, fim) das ocorrências
    """
    if not text:
        return {}
    
    # Preprocessa o texto
    search_text = text if self.case_sensitive else text.lower()
    
    results = defaultdict(list)
    current_state = 0
    
    for i, char in enumerate(search_text):
        # Encontra o próximo estado
        while current_state != 0 and char not in self.goto[current_state]:
            current_state = self.fail[current_state]
        
        if char in self.goto[current_state]:
            current_state = self.goto[current_state][char]
        
        # Verifica se há padrões que terminam neste estado
        if current_state in self.output:
            for pattern_idx in self.output[current_state]:
                pattern = self.patterns[pattern_idx]
                start_pos = i - len(pattern) + 1
                end_pos = i + 1
                
                # Verifica se é palavra completa (se necessário)
                if self.only_whole_words:
                    if not self._is_whole_word(text, start_pos, end_pos):
                        continue
                
                results[pattern].append((start_pos, end_pos))
    
    return dict(results)
```

**Explicação linha por linha:**

1. **Validação e preprocessamento (linhas 7-13):**
   - `if not text:` - Verifica se texto não está vazio
   - `search_text = text if self.case_sensitive else text.lower()` - Normaliza texto
   - `results = defaultdict(list)` - Dicionário para resultados
   - `current_state = 0` - Inicia na raiz do automato

2. **Processamento do texto (linha 15):**
   - `for i, char in enumerate(search_text):` - Percorre cada caractere com índice

3. **Busca por transição válida (linhas 17-18):**
   - `while current_state != 0 and char not in self.goto[current_state]:` - Enquanto não encontrar transição
   - `current_state = self.fail[current_state]` - Segue failure links

4. **Transição de estado (linhas 20-21):**
   - `if char in self.goto[current_state]:` - Se encontrou transição válida
   - `current_state = self.goto[current_state][char]` - Move para próximo estado

5. **Verifica padrões encontrados (linhas 23-34):**
   - `if current_state in self.output:` - Se há padrões terminando aqui
   - `for pattern_idx in self.output[current_state]:` - Para cada padrão
   - `pattern = self.patterns[pattern_idx]` - Obtém o padrão original
   - `start_pos = i - len(pattern) + 1` - Calcula posição inicial
   - `end_pos = i + 1` - Posição final (exclusiva)

6. **Validação de palavra completa (linhas 30-33):**
   - `if self.only_whole_words:` - Se deve verificar palavras completas
   - `if not self._is_whole_word(text, start_pos, end_pos):` - Verifica boundaries
   - `continue` - Pula se não é palavra completa

7. **Adiciona resultado (linha 34):**
   - `results[pattern].append((start_pos, end_pos))` - Adiciona match encontrado

### Validação de Palavra Completa

```python
def _is_whole_word(self, text: str, start: int, end: int) -> bool:
    """Verifica se a ocorrência é uma palavra completa."""
    # Verifica o caractere anterior
    if start > 0 and text[start - 1].isalnum():
        return False
    
    # Verifica o caractere posterior
    if end < len(text) and text[end].isalnum():
        return False
    
    return True
```

**Explicação:**

1. **Verifica caractere anterior (linhas 3-4):**
   - `if start > 0 and text[start - 1].isalnum():` - Se há caractere anterior alfanumérico
   - `return False` - Não é palavra completa

2. **Verifica caractere posterior (linhas 6-7):**
   - `if end < len(text) and text[end].isalnum():` - Se há caractere posterior alfanumérico
   - `return False` - Não é palavra completa

3. **Confirma palavra completa (linha 9):**
   - `return True` - É uma palavra completa

### Busca com Contexto

```python
def search_with_context(self,
                      text: str,
                      context_length: int = 20) -> Dict[str, List[Dict]]:
    """
    Busca padrões retornando contexto adicional.
    
    Returns:
        Dicionário com informações detalhadas das ocorrências
    """
    results = self.search(text)
    detailed_results = defaultdict(list)
    
    for pattern, positions in results.items():
        for start, end in positions:
            context_start = max(0, start - context_length)
            context_end = min(len(text), end + context_length)
            
            detailed_results[pattern].append({
                'start': start,
                'end': end,
                'match': text[start:end],
                'context': text[context_start:context_end],
                'context_start': context_start,
                'context_end': context_end
            })
    
    return dict(detailed_results)
```

**Explicação:**

1. **Busca básica (linhas 9-10):**
   - `results = self.search(text)` - Realiza busca normal
   - `detailed_results = defaultdict(list)` - Dicionário para resultados detalhados

2. **Processamento dos resultados (linhas 12-13):**
   - `for pattern, positions in results.items():` - Para cada padrão encontrado
   - `for start, end in positions:` - Para cada ocorrência

3. **Cálculo do contexto (linhas 14-15):**
   - `context_start = max(0, start - context_length)` - Início do contexto (não negativo)
   - `context_end = min(len(text), end + context_length)` - Fim do contexto (não excede texto)

4. **Criação do resultado detalhado (linhas 17-24):**
   - Dicionário com informações completas sobre cada match
   - Inclui posição, texto matched, contexto e limites

## Exemplo Prático: Demonstração Completa

```python
# Exemplo de uso do código
if __name__ == "__main__":
    # Exemplo 1: Busca básica
    patterns = ["he", "she", "his", "hers"]
    text = "ushers"
    
    ac = AhoCorasick(patterns, case_sensitive=False)
    results = ac.search(text)
    
    print("Exemplo 1 - Busca básica:")
    print(f"Texto: '{text}'")
    print(f"Padrões: {patterns}")
    print("Resultados:")
    for pattern, positions in results.items():
        for start, end in positions:
            print(f"  '{pattern}' encontrado em posição {start}-{end}")
    
    # Saída esperada:
    # 'he' encontrado em posição 2-4
    # 'she' encontrado em posição 1-4
    # 'hers' encontrado em posição 2-6
```

### Visualização da Execução

Para o exemplo `patterns = ["he", "she", "his", "hers"]` e `text = "ushers"`:

```
Texto: u s h e r s
Pos:   0 1 2 3 4 5

Estado por posição:
0: u → estado 0
1: s → estado 0
2: h → estado 1
3: e → estado 2 (encontrou "he")
4: r → estado 3
5: s → estado 4 (encontrou "hers")

Matches encontrados:
- "he" nas posições 2-4
- "she" nas posições 1-4 (via failure link)
- "hers" nas posições 2-6
```

## Análise de Complexidade

### Complexidade Temporal
- **Construção da trie**: O(Σm) onde Σ é a soma dos comprimentos dos padrões
- **Construção dos failure links**: O(Σm)
- **Busca**: O(n + z) onde n é o tamanho do texto e z é o número de matches
- **Total**: O(n + m + z)

### Complexidade Espacial
- **Trie**: O(Σm) para armazenar todos os estados
- **Failure links**: O(número de estados)
- **Total**: O(Σm)

## Prós e Contras

### ✅ Vantagens
- **Busca múltiplos padrões simultaneamente**
- **Complexidade temporal linear** mesmo para muitos padrões
- **Flexibilidade de configuração** (case-sensitive, whole words)
- **Informações detalhadas** sobre as ocorrências
- **Robusto para grandes conjuntos de padrões**

### ❌ Desvantagens
- **Maior consumo de memória** devido à trie
- **Implementação mais complexa** que algoritmos simples
- **Tempo de construção inicial** pode ser significativo
- **Overhead para poucos padrões**

## Quando Usar

### 🎯 Cenários Ideais
- **Filtros de conteúdo** com múltiplas palavras-chave
- **Análise de logs** com vários padrões de erro
- **Detecção de spam** com lista de termos proibidos
- **Bioinformática** para busca de sequências
- **Sistemas de monitoramento** em tempo real

### 🚫 Não Recomendado
- **Busca de padrão único** (use KMP)
- **Poucos padrões muito curtos** (overhead desnecessário)
- **Memória muito limitada** (usa mais espaço)
- **Padrões que mudam frequentemente**

## Exemplo Avançado: Sistema de Moderação

```python
# Sistema de moderação de comentários
class CommentModerator:
    def __init__(self):
        # Palavras proibidas
        forbidden_words = [
            "spam", "scam", "phishing", "malware",
            "virus", "hack", "exploit", "bot"
        ]
        
        # Criar moderador com busca por palavras completas
        self.moderator = AhoCorasick(
            forbidden_words,
            case_sensitive=False,
            only_whole_words=True
        )
    
    def moderate_comment(self, comment: str) -> Dict:
        """Modera um comentário e retorna resultado detalhado."""
        # Busca com contexto
        results = self.moderator.search_with_context(comment, context_length=15)
        
        # Analisa resultado
        is_approved = len(results) == 0
        violations = []
        
        for pattern, matches in results.items():
            for match in matches:
                violations.append({
                    'word': pattern,
                    'position': f"{match['start']}-{match['end']}",
                    'context': match['context']
                })
        
        return {
            'approved': is_approved,
            'violations': violations,
            'comment': comment,
            'statistics': self.moderator.get_statistics()
        }

# Uso do sistema
moderator = CommentModerator()
result = moderator.moderate_comment("This is spam and malware!")

print(f"Aprovado: {result['approved']}")
for violation in result['violations']:
    print(f"Violação: '{violation['word']}' em {violation['position']}")
    print(f"Contexto: '{violation['context']}'")
```

## Exercícios Práticos

### 1. Implementação de Filtro de E-mail
Crie um filtro que detecte spam usando os padrões:
`["free", "offer", "click", "now", "urgent", "limited"]`

### 2. Análise de Logs
Desenvolva um sistema para monitorar logs procurando por:
`["error", "exception", "failed", "timeout", "crash"]`

### 3. Busca Sensível ao Contexto
Implemente uma busca que diferencia "java" (linguagem) de "Java" (ilha).

### 4. Visualização de Resultados
Crie uma função que destaque as ocorrências encontradas no texto original.

## Referências

1. Aho, A. V., & Corasick, M. J. (1975). Efficient string matching: an aid to bibliographic search.
2. Cormen, T. H., et al. (2009). Introduction to Algorithms, 3rd Edition.
3. Gusfield, D. (1997). Algorithms on Strings, Trees and Sequences.
4. Documentação oficial Python: `collections.defaultdict` e `collections.deque`

---

!!! success "Conclusão"
    O algoritmo Aho-Corasick é uma ferramenta poderosa para busca eficiente de múltiplos padrões. Sua implementação em Python oferece flexibilidade e performance, sendo ideal para aplicações que precisam processar grandes volumes de texto com múltiplos critérios de busca.