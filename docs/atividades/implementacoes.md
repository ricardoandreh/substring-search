# implementação Rabin-Karp

##
```java
// Rabin-Karp algorithm in Java

public class RabinKarp {
  public final static int d = 10;

  static void search(String pattern, String txt, int q) {
    int m = pattern.length();
    int n = txt.length();
    int i, j;
    int p = 0;
    int t = 0;
    int h = 1;

    for (i = 0; i < m - 1; i++)
      h = (h * d) % q;

    // Calculate hash value for pattern and text
    for (i = 0; i < m; i++) {
      p = (d * p + pattern.charAt(i)) % q;
      t = (d * t + txt.charAt(i)) % q;
    }

    // Find the match
    for (i = 0; i <= n - m; i++) {
      if (p == t) {
        for (j = 0; j < m; j++) {
          if (txt.charAt(i + j) != pattern.charAt(j))
            break;
        }

        if (j == m)
          System.out.println("Pattern is found at position: " + (i + 1));
      }

      if (i < n - m) {
        t = (d * (t - txt.charAt(i) * h) + txt.charAt(i + m)) % q;
        if (t < 0)
          t = (t + q);
      }
    }
  }

  public static void main(String[] args) {
    String txt = "ABCCDDAEFG";
    String pattern = "CDD";
    int q = 13;
    search(pattern, txt, q);
  }
}

```

```python
// Rabin-Karp algorithm in python
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

```