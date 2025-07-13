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
    n = len(texto) # Tamanho do texto = 11
    m = len(padrao) # Tamanho do padrão = 3
```

**Verificação de tamanho**

Verifica se o tamanho do padrão é maior que o texto. Caso seja, irá retornar **1**.

```python
if m > n:
    return -1
```

** 
```python
```
```python
```
```python
```
```python
```
```python
```
