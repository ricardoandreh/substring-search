# Algoritmo de Busca de Rabin-Karp

O **Algoritmo de Rabin-Karp** é uma técnica eficiente para encontrar substrings dentro de uma string. Ele utiliza hashing para comparar rapidamente os hashes das subsequências do texto com o hash do padrão, em vez de comparar cada caractere individualmente.

## Funcionamento

1. **Geração de Hash**: O algoritmo gera um "hash" para o padrão e para cada subsequência do texto que tem o mesmo comprimento do padrão.
2. **Comparação de Hashes**: Os hashes são comparados. Se houver uma correspondência, o algoritmo faz uma comparação detalhada dos caracteres para garantir que houve uma verdadeira correspondência.
3. **Deslocamento**: O hash da subsequência do texto é atualizado a cada movimento de janela, de forma a não precisar recalcular o hash para todos os caracteres da nova janela.

## Vantagens

- **Eficiência**: Na média, o algoritmo tem complexidade **O(n + m)**, onde `n` é o tamanho do texto e `m` é o tamanho do padrão.
- **Detecção rápida de possíveis correspondências**: Se houver poucas colisões de hashes, o algoritmo é muito rápido.

## Código em Python

```python
def rabin_karp(texto, padrao, base=256, mod=101):
    n = len(texto)
    m = len(padrao)
    
    if m > n:
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
