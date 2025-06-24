# Algoritmo Naive (Força Bruta)

!!! abstract "Definição"
    O algoritmo Naive, também conhecido como força bruta, é a abordagem mais simples e intuitiva para busca de padrões em strings. Ele verifica todas as posições possíveis do texto, comparando caractere por caractere.

## 🧠 Conceito e Motivação

O algoritmo Naive resolve o problema da busca em strings da forma mais direta possível:

1. **Para cada posição** no texto
2. **Compare** o padrão com a substring naquela posição
3. **Se todos os caracteres coincidirem**, padrão encontrado
4. **Caso contrário**, move para a próxima posição

### Exemplo Visual

```
Texto:    "ABABCABABA"
Padrão:   "ABABA"

Tentativa 1: ABABA vs ABABA ✓ (Encontrado na posição 0)
             ^^^^^
Tentativa 2:  BABA vs ABABA ✗
              ^^^^
Tentativa 3:   ABA vs ABABA ✗
               ^^^
...e assim por diante
```

## 💻 Implementação em Python

### Versão Básica

```python linenums="1"
def naive_search(text, pattern):
    """
    Busca naive de padrão em texto.
    
    Args:
        text (str): Texto onde buscar
        pattern (str): Padrão a ser encontrado
    
    Returns:
        list: Lista com todas as posições onde o padrão foi encontrado
    """
    n = len(text)        # Comprimento do texto
    m = len(pattern)     # Comprimento do padrão
    positions = []       # Lista para armazenar posições encontradas
    
    # Percorre todas as posições possíveis no texto
    for i in range(n - m + 1):
        # Verifica se o padrão coincide na posição i
        match = True
        for j in range(m):
            if text[i + j] != pattern[j]:
                match = False
                break
        
        # Se houve match completo, adiciona a posição
        if match:
            positions.append(i)
    
    return positions
```

### Análise Linha por Linha

```python linenums="1"
def naive_search_detalhado(text, pattern):
    n = len(text)        # (1) Obtém o tamanho do texto
    m = len(pattern)     # (2) Obtém o tamanho do padrão
    positions = []       # (3) Inicializa lista de resultados
    
    # (4) Loop principal: testa cada posição possível
    for i in range(n - m + 1):  # Por que n-m+1? 
        # Se o texto tem 10 chars e padrão tem 3,
        # só precisamos testar posições 0 a 7 (10-3+1=8 posições)
        
        match = True         # (5) Assume que há match inicialmente
        
        # (6) Loop interno: compara cada caractere
        for j in range(m):
            # (7) Compara caractere por caractere
            if text[i + j] != pattern[j]:
                match = False    # (8) Se diferente, não há match
                break           # (9) Sai do loop interno (otimização)
        
        # (10) Se todos os caracteres coincidiram
        if match:
            positions.append(i)  # (11) Adiciona posição à lista
    
    return positions            # (12) Retorna todas as posições
```

### Versão com Debugging

```python linenums="1"
def naive_search_debug(text, pattern, verbose=False):
    """Versão com debug para visualizar o processo"""
    n, m = len(text), len(pattern)
    positions = []
    comparisons = 0  # Contador de comparações (para análise)
    
    if verbose:
        print(f"Texto: '{text}' (tamanho: {n})")
        print(f"Padrão: '{pattern}' (tamanho: {m})")
        print("-" * 50)
    
    for i in range(n - m + 1):
        if verbose:
            print(f"\nTentativa na posição {i}:")
            print(f"Comparando '{text[i:i+m]}' com '{pattern}'")
        
        match = True
        for j in range(m):
            comparisons += 1
            if verbose:
                print(f"  {text[i+j]} vs {pattern[j]} - ", end="")
            
            if text[i + j] != pattern[j]:
                match = False
                if verbose:
                    print("❌ Diferente")
                break
            else:
                if verbose:
                    print("✅ Igual")
        
        if match:
            positions.append(i)
            if verbose:
                print(f"🎯 MATCH encontrado na posição {i}!")
    
    if verbose:
        print(f"\nTotal de comparações: {comparisons}")
        print(f"Posições encontradas: {positions}")
    
    return positions, comparisons
```

## 📊 Análise de Complexidade

### Complexidade Temporal

- **Melhor caso**: O(n) - padrão encontrado logo no início
- **Caso médio**: O(n×m) - depende da distribuição dos caracteres
- **Pior caso**: O(n×m) - padrão nunca encontrado ou encontrado no final

```python
# Exemplo do pior caso
texto = "AAAAAAAAAAB"
padrao = "AAAAB"
# Teremos que comparar quase todos os caracteres em cada posição
```

### Complexidade Espacial

- **O(1)** - usa apenas algumas variáveis auxiliares
- **O(k)** se contarmos a lista de resultados, onde k = número de ocorrências

## ⚖️ Prós e Contras

### ✅ Vantagens

1. **Simplicidade**: Fácil de entender e implementar
2. **Sem pré-processamento**: Não precisa preparar dados
3. **Pouco uso de memória**: Complexidade espacial O(1)
4. **Funciona sempre**: Nunca falha, sempre encontra se existir

### ❌ Desvantagens

1. **Ineficiente**: O(n×m) pode ser muito lento
2. **Retrocesso desnecessário**: Não aproveita informações já obtidas
3. **Ruim para textos grandes**: Performance degrada rapidamente
4. **Muitas comparações redundantes**: Re-examina caracteres já vistos

## 🎯 Casos de Uso

### Quando usar o Algoritmo Naive?

1. **Textos pequenos**: Quando n e m são pequenos
2. **Implementação rápida**: Para protótipos ou testes
3. **Padrões muito pequenos**: m ≤ 3 caracteres
4. **Uma única busca**: Não vale a pena pré-processar

### Exemplo Prático: Filtro Simples

```python linenums="1"
def filtro_palavras_proibidas(texto, palavras_proibidas):
    """
    Exemplo prático: filtro de palavras proibidas
    """
    texto_limpo = texto
    
    for palavra in palavras_proibidas:
        # Busca naive para cada palavra proibida
        positions, _ = naive_search_debug(texto_limpo.lower(), 
                                        palavra.lower())
        
        # Substitui por asteriscos
        for pos in reversed(positions):  # Reversed para não afetar índices
            texto_limpo = (texto_limpo[:pos] + 
                          '*' * len(palavra) + 
                          texto_limpo[pos + len(palavra):])
    
    return texto_limpo

# Teste
texto = "Este texto contém palavras inadequadas e conteúdo impróprio"
proibidas = ["inadequadas", "impróprio"]
resultado = filtro_palavras_proibidas(texto, proibidas)
print(resultado)
# Output: "Este texto contém palavras *********** e conteúdo *********"
```

## 🧪 Experimentos e Testes

### Teste de Performance

```python linenums="1"
import time
import random
import string

def gerar_texto_aleatorio(tamanho):
    """Gera texto aleatório para testes"""
    return ''.join(random.choices(string.ascii_lowercase, k=tamanho))

def benchmark_naive(tamanhos_texto, tamanho_padrao=5):
    """Testa performance do algoritmo naive"""
    resultados = []
    
    for n in tamanhos_texto:
        texto = gerar_texto_aleatorio(n)
        padrao = gerar_texto_aleatorio(tamanho_padrao)
        
        inicio = time.time()
        positions, comparisons = naive_search_debug(texto, padrao)
        fim = time.time()
        
        resultados.append({
            'tamanho_texto': n,
            'tempo': fim - inicio,
            'comparacoes': comparisons,
            'ocorrencias': len(positions)
        })
    
    return resultados

# Executar benchmark
tamanhos = [100, 500, 1000, 5000, 10000]
resultados = benchmark_naive(tamanhos)

for r in resultados:
    print(f"Texto: {r['tamanho_texto']:5d} | "
          f"Tempo: {r['tempo']:.4f}s | "
          f"Comparações: {r['comparacoes']:6d}")
```

## 🔍 Variações do Algoritmo Naive

### 1. Busca Case-Insensitive

```python linenums="1"
def naive_search_case_insensitive(text, pattern):
    """Busca ignorando maiúsculas/minúsculas"""
    return naive_search(text.lower(), pattern.lower())
```

### 2. Busca com Wildcards

```python linenums="1"
def naive_search_wildcard(text, pattern, wildcard='?'):
    """
    Busca com wildcard (? = qualquer caractere)
    Exemplo: "a?c" encontra "abc", "axc", "a1c", etc.
    """
    n, m = len(text), len(pattern)
    positions = []
    
    for i in range(n - m + 1):
        match = True
        for j in range(m):
            if pattern[j] != wildcard and text[i + j] != pattern[j]:
                match = False
                break
        
        if match:
            positions.append(i)
    
    return positions

# Teste
texto = "abcdefghijk"
padrao = "c?e"  # Deve encontrar "cde"
print(naive_search_wildcard(texto, padrao))  # [2]
```

### 3. Busca de Múltiplos Padrões

```python linenums="1"
def naive_search_multiplos(text, patterns):
    """Busca múltiplos padrões usando algoritmo naive"""
    resultados = {}
    
    for pattern in patterns:
        resultados[pattern] = naive_search(text, pattern)
    
    return resultados

# Teste
texto = "abcabcabc"
padroes = ["abc", "bca", "cab"]
resultados = naive_search_multiplos(texto, padroes)
for padrao, posicoes in resultados.items():
    print(f"'{padrao}': {posicoes}")
```

## 📝 Exercícios Práticos

!!! question "Exercício 1: Implementação Básica"
    Implemente uma versão do algoritmo naive que retorne apenas a primeira ocorrência do padrão.

!!! question "Exercício 2: Contador de Ocorrências"
    Modifique o algoritmo para contar quantas vezes o padrão aparece no texto (sem armazenar posições).

!!! question "Exercício 3: Busca Bidirecional"
    Implemente uma versão que busque o padrão tanto da esquerda para direita quanto da direita para esquerda.

!!! question "Exercício 4: Análise de Complexidade"
    Crie casos de teste que demonstrem o melhor e pior caso do algoritmo.

---

**Próximo:** [KMP (Knuth-Morris-Pratt)](kmp.md) - Vamos ver como evitar os retrocessos desnecessários! 🚀