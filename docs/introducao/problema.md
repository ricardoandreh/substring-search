# O Problema da Busca em Strings

## Definição do Problema

A **busca em strings** (string matching) é o problema fundamental de encontrar todas as ocorrências de um **padrão** P de comprimento m em um **texto** T de comprimento n.

### Exemplo Básico

```
Texto:    "ABABDABACDABABCABCABCABCABC"
Padrão:   "ABAB"
Posições: [0, 15]
```

## Por que é um Problema Importante?

### 1. Ubiquidade
A busca em strings aparece em praticamente todos os sistemas computacionais:

!!! example "Aplicações Cotidianas"
    - **Editores de texto**: Função "Localizar" (Ctrl+F)
    - **Navegadores web**: Busca na página
    - **Bancos de dados**: Consultas com LIKE
    - **Sistemas operacionais**: Comando grep/find

### 2. Desafios Computacionais

#### Volume de Dados
- **Genoma humano**: ~3 bilhões de pares de bases
- **Internet**: Trilhões de páginas web
- **Logs de sistema**: Terabytes diários

#### Requisitos de Performance
- **Tempo real**: Buscas devem ser instantâneas
- **Throughput**: Processar milhões de consultas/segundo
- **Memória limitada**: Algoritmos devem ser eficientes em espaço

### 3. Complexidade Algorítmica

#### Abordagem Ingênua
```python
def busca_ingenua(texto, padrao):
    n, m = len(texto), len(padrao)
    for i in range(n - m + 1):
        if texto[i:i+m] == padrao:
            return i
    return -1
```

**Complexidade**: O(n×m) - Ineficiente para textos grandes!

## Cenários Reais de Aplicação

### 1. Bioinformática 🧬

```python
# Busca de sequências de DNA
dna = "ATCGATCGATCGATCG..."
gene = "ATCGATCG"
# Encontrar todas as ocorrências do gene
```

**Desafios**:
- Genomas com bilhões de nucleotídeos
- Milhares de genes para buscar
- Tolerância a mutações

### 2. Segurança Cibernética 🛡️

```python
# Detecção de malware por assinaturas
arquivo_suspeito = "..."
assinaturas_malware = ["virus_sig1", "trojan_sig2", ...]
# Buscar múltiplas assinaturas simultaneamente
```

**Desafios**:
- Milhares de assinaturas conhecidas
- Arquivos de gigabytes
- Processamento em tempo real

### 3. Processamento de Linguagem Natural 📝

```python
# Análise de sentimentos
texto = "Este produto é fantástico e recomendo!"
palavras_positivas = ["fantástico", "ótimo", "excelente", ...]
# Encontrar termos que indicam sentimento
```

**Desafios**:
- Dicionários com milhares de termos
- Processamento de documentos extensos
- Análise multilíngue

## Métricas de Avaliação

### 1. Complexidade Temporal
- **Pré-processamento**: Tempo para preparar estruturas auxiliares
- **Busca**: Tempo para encontrar ocorrências
- **Pior caso vs. Caso médio**

### 2. Complexidade Espacial
- Memória adicional necessária
- Escalabilidade com tamanho do padrão/texto

### 3. Casos de Uso Específicos
- **Padrão único vs. múltiplos padrões**
- **Texto estático vs. streaming**  
- **Sensibilidade a maiúsculas/minúsculas**

## Classificação dos Algoritmos

### Por Estratégia

| Estratégia | Algoritmos | Característica |
|------------|------------|----------------|
| **Força Bruta** | Naive | Simples, ineficiente |
| **Pré-processamento do Padrão** | KMP, Boyer-Moore | Evita comparações desnecessárias |
| **Hashing** | Rabin-Karp | Comparação rápida via hash |
| **Autômatos** | Aho-Corasick | Múltiplos padrões simultaneamente |

### Por Aplicação

| Cenário | Algoritmo Recomendado | Motivo |
|---------|----------------------|---------|
| **Padrão único, texto pequeno** | Naive | Simplicidade |
| **Padrão único, texto grande** | KMP | Eficiência linear |
| **Múltiplos padrões** | Aho-Corasick | Busca simultânea |
| **Hashing disponível** | Rabin-Karp | Flexibilidade |

## Questões para Reflexão

!!! question "Pense sobre isso..."
    1. Por que o algoritmo naive pode ser problemático para textos grandes?
    2. Qual seria o impacto de usar um algoritmo ineficiente no Google?
    3. Como a escolha do algoritmo afeta a experiência do usuário?
    4. Quais são os trade-offs entre simplicidade e eficiência?
