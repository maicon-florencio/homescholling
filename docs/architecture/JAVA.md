# Diretrizes de Código Java Moderno (Java 21 a Java 25)

> **Regra Geral para o Agente:** Este projeto utiliza **Java 25**. Código legado ou idiomático de versões anteriores (Java 8, 11, 17) que possua equivalentes modernos **não deve ser utilizado**, mesmo que seja totalmente compatível. Priorize sempre APIs idiomáticas recentes, legibilidade, concisão e segurança de tipos.

---

## 1. Coleções e Sequências (Sequenced Collections - Java 21+)

Acesse o primeiro ou último elemento de coleções/listas ordenadas e inverta-as utilizando as novas interfaces `SequencedCollection`.

* ❌ **NÃO FAÇA (Legado):**
  ```java
  var primeiro = minhaLista.get(0);
  var ultimo = minhaLista.get(minhaLista.size() - 1);
  var primeiroSet = meuSortedSet.iterator().next();
  ```
* ✅ **FAÇA (Java Moderno):**
  ```java
  var primeiro = minhaLista.getFirst();
  var ultimo = minhaLista.getLast();
  var primeiroSet = meuSortedSet.getFirst();
  ```

---

## 2. Pattern Matching e Switch Expressions (Java 17 - 21+)

Sempre utilize `switch` como expressão retornável e aproveite a desestruturação de tipos (*pattern matching*).

* ❌ **NÃO FAÇA (Legado):**
  ```java
  String resultado;
  if (obj instanceof String) {
      String s = (String) obj;
      resultado = s.toLowerCase();
  } else if (obj instanceof Integer) {
      Integer i = (Integer) obj;
      resultado = "Número: " + i;
  } else {
      resultado = "Desconhecido";
  }
  ```
* ✅ **FAÇA (Java Moderno):**
  ```java
  String resultado = switch (obj) {
      case String s -> s.toLowerCase();
      case Integer i -> "Número: " + i;
      case null, default -> "Desconhecido";
  };
  ```

---

## 3. Registros e Desestruturação (Record Patterns - Java 21+)

Ao lidar com DTOs ou classes imutáveis de dados, utilize `record`. No `switch` ou `if`, desestruture o registro diretamente no padrão.

* ❌ **NÃO FAÇA (Legado):**
  ```java
  if (obj instanceof Ponto) {
      Ponto p = (Ponto) obj;
      System.out.println("X: " + p.x() + ", Y: " + p.y());
  }
  ```
* ✅ **FAÇA (Java Moderno):**
  ```java
  if (obj instanceof Ponto(int x, int y)) {
      System.out.println("X: " + x + ", Y: " + y);
  }
  ```

---

## 4. Concorrência e Threads Virtuais (Virtual Threads - Java 21+)

Para operações I/O-intensive, prefira Threads Virtuais (`Executors.newVirtualThreadPerTaskExecutor()`) em vez de pools de threads tradicionais pesados.

* ❌ **NÃO FAÇA (Legado):**
  ```java
  ExecutorService executor = Executors.newFixedThreadPool(100);
  ```
* ✅ **FAÇA (Java Moderno):**
  ```java
  try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
      executor.submit(() -> processarRequisicao());
  }
  ```

---

## 5. Instanciação Imutável de Coleções (Java 9 - 19+)

Instancie coleções imutáveis usando métodos fabris de conveniência `List.of()`, `Set.of()`, `Map.of()`. Para coleções em memória intermediárias em Streams, prefira `.toList()`.

* ❌ **NÃO FAÇA (Legado):**
  ```java
  List<String> lista = Arrays.asList("A", "B");
  List<String> filtrados = stream.collect(Collectors.toList());
  ```
* ✅ **FAÇA (Java Moderno):**
  ```java
  List<String> lista = List.of("A", "B");
  List<String> filtrados = stream.toList();
  ```

---

## 6. String Templates e Text Blocks (Java 15 - 21+)

Utilize *Text Blocks* (`"""`) para textos multilinha (como JSONs, SQLs, HTMLs).

* ❌ **NÃO FAÇA (Legado):**
  ```java
  String json = "{\n" +
                "  \"nome\": \"Java\",\n" +
                "  \"versao\": 25\n" +
                "}";
  ```
* ✅ **FAÇA (Java Moderno):**
  ```java
  String json = """
      {
        "nome": "Java",
        "versao": 25
      }
      """;
  ```

---

## 7. Flexibilidade em Construtores (`Statements before super()` - Java 22+)

É permitido executar código de validação ou preparação antes de chamar `super(...)` ou `this(...)`, desde que não acesse `this` antes do construtor pai terminar.

* ✅ **FAÇA (Java Moderno):**
  ```java
  public class MinhaClasse extends SubClasse {
      public MinhaClasse(int valor) {
          if (valor < 0) {
              throw new IllegalArgumentException("Valor inválido");
          }
          super(valor);
      }
  }
  ```

---

## Dicas Práticas de Configuração no `AGENTS.md`

Para garantir a aderência do LLM ao arquivo acima, insira no seu arquivo raiz `AGENTS.md` o seguinte trecho de contexto:

```markdown
## Regras de Linguagem e Versão
- **Linguagem Principal:** Java 25.
- **Padrões Obrigatórios:** Consulte o arquivo `./JAVA_CONVENTIONS.md` antes de escrever ou refatorar qualquer código.
- **Proibição de Código Antigo:** É estritamente proibido o uso de idioms pré-Java 21 (como `list.get(0)`, `Collectors.toList()`, `fixedThreadPool` para I/O, cast manual pós-`instanceof`, etc.).
```