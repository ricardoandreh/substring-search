# Algoritmo de Busca de Rabin-Karp

## Definição

O **Algoritmo de Rabin-Karp** é uma técnica eficiente para encontrar substrings dentro de uma string. Ele utiliza hashing para comparar rapidamente os hashes das subsequências do texto com o hash do padrão, em vez de comparar cada caractere individualmente.

## Contexto histórico

O Rabin-Karp foi desenvolvido por Michael O. Rabin e Richard M. Karp em 1987, onde foram uns dos pioneiros a usar a técnica de hashing para buscar padrões, o qual permitiu a busca de multiplos padrões.

## Base teórica

Primeiro definimos uma função de dispersão, algoritmo que transforma os dados de entrada em um índice. Depois, é calculado o valor de hash do padrão que desejamos encontrar. Após determinar o hash que queremos encontrar, iniciamos a busca dentro do texto, dentro de uma substring, um pequeno pedaço do texto por vez. Se os valores do hash substring correspoderem, uma nova comparação é feita para verificar a correspondência, pois substrings diferentes podem gerar o mesmo valor de hash, onde chamamos de colisão. Para ganhos utilizamos a técnica de rolling hash para haja um ganho de eficiência no algoritmo.

## Rolling hash

O **Rolling hash** rata-se de uma técnica que permite calcular o hash de todas as substrings de tamanho fixo de forma eficiente. Invés de calcularmos o valor do hash novamente,o **Rolling hash** atualiza o valor da hash da substring anterior, subtraindo o valor do primeiro caracter e somando o valor do primeiro caracter, acessando o novo valor de uma nova substring. No contexto **Rabin-Karp**, significa remover o caracter mais à esquerda e adicionar o valor do caracter que entra na nova substring.

## Funcionamento

1. **Geração de Hash**: O algoritmo gera um "hash" para o padrão e para cada subsequência do texto que tem o mesmo comprimento do padrão.
2. **Comparação de Hashes**: Os hashes são comparados. Se houver uma correspondência, o algoritmo faz uma comparação detalhada dos caracteres para garantir que houve uma verdadeira correspondência.
3. **Deslocamento**: O hash da subsequência do texto é atualizado a cada movimento de janela, de forma a não precisar recalcular o hash para todos os caracteres da nova janela.

## Implementação

1. **Pré-processamento:** Calcula o valor do hash do padrão e inicializa o valor hash para as substrings do texto
2. **Busca:** Desliza a janela sobre o texto, atualiza o hash da substring atual e compara com o hash do padrão.

## Vantagens

- **Eficiência**: Na média, o algoritmo tem complexidade **O(n + m)**, onde `n` é o tamanho do texto e `m` é o tamanho do padrão.
- **Detecção rápida de possíveis correspondências**: Se houver poucas colisões de hashes, o algoritmo é muito rápido.

## Desvantagens

Embora ele traga ganhos de eficiência, ela pode variar dependendo da função hash que irá utilzar, oqual sua complexidade de **O(n + m)** passa a ser **O(n * m)** resultando em uma perda de eficiência e performance.

## Código em Python

```python
def rabin_karp(texto, padrao, base=256, mod=101):
    n = len(texto)
    m = len(padrao)
    
    if m > n: # Verifica se o padrão é mair que o texto
        return -1 

    hash_padro = 0  # Hash do padrão
    hash_texto = 0  # Hash da janela atual do texto
    h = 1  # Valor de "h" é base^(m-1) % mod
    
    # Calculando o valor de h (base^(m-1) % mod)
    for i in range(m - 1):
        h = (h * base) % mod

    # Calculando o hash do padrão e do texto para os primeiros m caracteres
    for i in range(m):
        hash_padro = (base * hash_padro + ord(padrao[i])) % mod
        hash_texto = (base * hash_texto + ord(texto[i])) % mod

    # Comparando os hashes e verificando a correspondência
    for i in range(n - m + 1):
        if hash_padro == hash_texto:
            if texto[i:i + m] == padrao:  # Verificação final por coincidência exata
                return i
        if i < n - m:
            # Atualiza o hash da janela de texto (desloca a janela)
            hash_texto = (base * (hash_texto - ord(texto[i]) * h) + ord(texto[i + m])) % mod
            if hash_texto < 0:
                hash_texto += mod

    return -1  # Não encontrou a correspondência
```

## Exemplo

**Parametros a serem utilizados:**
- **texto** = "abracadabra"
- **padrão** = "cad"
- **base** = 256 **(padrão ASCII)**
- **mod** = 101 **(número primo pequeno para controle de colisão)**

```python
def rabin_karp(texto, padrao, base=256, mod=101):
    n = len(texto)   # Tamanho do texto = 11
    m = len(padrao)  # Tamanho do padrão = 3
```

**Verificação de tamanho**

Verifica se o tamanho do padrão é maior que o texto. Caso seja, irá retornar **1**.

```python
if m > n:  # Verifica se o padrão é mair que o texto
    return -1
```

**Armazenamento de valores hash**

Armazena os valores hash do padrão e o da substring do texto.

```python
hash_padro = 0  # Hash do padrão
hash_texto = 0  # Hash da janela atual do texto
h = 1           # h = base^(m-1) % mod
```
**Cálculo**

Usado depois para remover o caractere mais à esquerda da janela no rolling hash.

```python
for i in range(m - 1):
    h = (h * base) % mod
```

**Inicializa os hashes**

O **ord(char)** retorna o código ASCII de um caractere.

```python
for i in range(m):
    hash_padro = (base * hash_padro + ord(padrao[i])) % mod # Valor do padrão
    hash_texto = (base * hash_texto + ord(texto[i])) % mod  # Primeira janela do texto com o mesmo tamanho do padrão
```

**Percurso das janela**

Percorre todas as janelas possíveis do texto de tamanho m.

```python
for i in range(n - m + 1):
```

**Comparação de hashes**

Caso haja uma correspondência, compara os caracteres diretamente. Caso seja verdadeiro, retorna a sua posição.

```python
if hash_padro == hash_texto:
    if texto[i:i + m] == padrao: # Comparação real letra por letra
        return i
```

**Evitar perder performance**

Evita tentar calcular uma janela além do fim do texto.

```python
if i < n - m:
```

**Atualiza o hash da próxima janela (rolling hash)**

Remove o caractere da esquerda da janela, multiplica o resultado e adiciona o novo caracter que entrou. Aplica **% mod** para manter o número controlado

```python
hash_texto = (base * (hash_texto - ord(texto[i]) * h) + ord(texto[i + m])) % mod
```

**Corrige os Hashs negativos**

Esta linha **corrige o hash** caso ele tenha ficado negativo após o cálculo.

```python
if hash_texto < 0:
    hash_texto += mod

```
**Caso não encontre**

Caso termine a verificação e não encontre o padrão, retorna **-1**, indicando **"Não encontrado"**

```python
 return -1
```

## Saída esperada do exemplo

| Iteração | Janela   | Posição  | `hash_texto` | `hash_padro` | Hashs Iguais? | Verificação direta | Resultado         |
|----------|----------|----------|--------------|--------------|----------------|---------------------|-------------------|
| 0        | `"abr"`  | 0–2      | 37           | 63           | ❌             | —                   | Não encontrado    |
| 1        | `"bra"`  | 1–3      | 42           | 63           | ❌             | —                   | Não encontrado    |
| 2        | `"rac"`  | 2–4      | 96           | 63           | ❌             | —                   | Não encontrado    |
| 3        | `"aca"`  | 3–5      | 63           | 63           | ✅             | `"aca" != "cad"` ❌ | Falso positivo    |
| 4        | `"cad"`  | 4–6      | 63           | 63           | ✅             | `"cad" == "cad"` ✅ | ✅ Encontrado     |
| 5        | `"ada"`  | 5–7      | 87           | 63           | ❌             | —                   | Não encontrado    |
| 6        | `"dab"`  | 6–8      | 27           | 63           | ❌             | —                   | Não encontrado    |
| 7        | `"abr"`  | 7–9      | 37           | 63           | ❌             | —                   | Não encontrado    |
| 8        | `"bra"`  | 8–10     | 42           | 63           | ❌             | —                   | Não encontrado    |
