# SpringBoot-beginner
Um repositório que explica passos iniciais em spring boot

# Spring Core
![Captura de tela de 2025-04-09 10-36-48](https://github.com/user-attachments/assets/a8d524d7-9c29-4925-a58d-c9a1feba13f0)

---

## 🔁 1. **Spring Beans**

### O que são?

- **Beans** são **objetos gerenciados pelo contêiner do Spring**.
    
- São criados, configurados e injetados automaticamente pelo framework.
    

### Exemplo:


```java
@Component 
public class MeuServico {     
	public void executar() {         
		System.out.println("Executando...");     
	}
}
```

### Como o Spring sabe disso?

Com anotações como `@Component`, `@Service`, `@Repository`, etc., o Spring escaneia as classes e registra os Beans.

---

## 🔧 2. **Spring Core**

### O que faz?

- Contém o **Container IoC**, que gerencia o ciclo de vida dos beans.
    
- Implementa o **principio de Inversão de Controle**: o desenvolvedor não precisa instanciar objetos manualmente, o Spring faz isso.
    

### Principais classes:

- `BeanFactory` (interface mais simples)
    
- `ApplicationContext` (mais poderosa, usada na prática)
    

---

## 🧠 3. **Spring Context**

### O que é?

- O **`ApplicationContext`** é uma **extensão de `BeanFactory`**, usada para gerenciar beans e outros recursos como:
    
    - Mensagens internacionais
        
    - Eventos de aplicação
        
    - Carregamento de arquivos de configuração
        

> Em um projeto Spring Boot, o `ApplicationContext` é iniciado automaticamente quando você roda a aplicação (`@SpringBootApplication` cuida disso).

---

## 🧙‍♂️ 4. **SpEL (Spring Expression Language)**

### O que é?

- É uma **linguagem de expressão** usada para fazer **avaliações dinâmicas** de valores.
    
- Muito útil para configurar beans, condições, filtros, etc.
    

### Exemplo:

java

CopiarEditar

`@Value("#{2 * T(Math).PI}") private double resultado;`

Ou pegando propriedades:

java

CopiarEditar

`@Value("#{systemProperties['user.home']}") private String diretorioUsuario;`

---

## 🔄 Como tudo se conecta?

```text

@SpringBootApplication
        ↓
Spring Boot inicia o ApplicationContext
        ↓
ApplicationContext escaneia e instancia os Beans (@Component, @Service, etc.)
        ↓
Esses Beans podem usar SpEL para configurações dinâmicas
        ↓
Tudo isso é possível graças ao Spring Core (IoC/DI container)

```

# Data Access
O módulo de **Spring Data Access** fornece formas de se conectar com **bancos de dados relacionais, bancos NoSQL, mensageria**, e até serialização de XML/JSON. Ele cuida de:

- **Conexão com o banco**
    
- **Execução de queries**
    
- **Mapeamento de objetos**
    
- **Transações**
    
- **Mensageria**
![Captura de tela de 2025-04-09 10-47-13](https://github.com/user-attachments/assets/f3b632f9-64bb-4ea0-a56a-a7df0279681c)


## 1. 🔌 **JDBC (Java Database Connectivity)**

### O que é?

- É a API nativa do Java para se conectar com **bancos relacionais** (MySQL, PostgreSQL, etc.).
    
- No Spring, o `JdbcTemplate` facilita o uso do JDBC tradicional (sem precisar ficar abrindo/conectando/fechando na mão).
### Exemplo:

```java
@Autowired
JdbcTemplate jdbcTemplate;

public List<Produto> listar() {
    return jdbcTemplate.query("SELECT * FROM produtos",
        (rs, rowNum) -> new Produto(rs.getInt("id"), rs.getString("nome")));
}

```

## 2. 🧱 **ORM (Object-Relational Mapping)**

### O que é?

- ORM permite mapear classes Java para tabelas do banco de dados.
    
- O **Spring Boot usa JPA + Hibernate** por padrão como solução ORM.
    
- Você trabalha com **entidades Java**, e o framework converte pra SQL.
    

### Exemplo com Spring Data JPA:
```java
@Entity
public class Produto {
    @Id
    @GeneratedValue
    private Long id;
    private String nome;
}

@Repository
public interface ProdutoRepository extends JpaRepository<Produto, Long> {}

```

Com isso, você já pode fazer:

`produtoRepository.findAll();` 
`produtoRepository.save(novoProduto);`

## 3. 🔄 **O/X Mapping (O/XM)**

### O que é?

- **O/XM = Object/XML Mapping**
    
- Usado para **serializar e desserializar objetos em XML**.
    
- Útil em integrações legadas ou APIs que usam XML.
    

### Spring fornece:

- Suporte com JAXB, Castor, XStream, etc.
    
- Pode usar `Marshaller` e `Unmarshaller`.
    

### Exemplo:

`@JaxbRootElement 
public class Cliente {     
	private String nome;     
	// getters e setters 
}`

## 4. 💼 **Transactions**

### O que são?

- Transações garantem que um conjunto de operações no banco seja **atômico** (tudo ou nada).
    
- No Spring, você usa `@Transactional` para garantir isso.
    

### Exemplo:
```java
@Service
public class CompraService {
    @Transactional
    public void comprar(Long produtoId, Long clienteId) {
        estoqueService.retirar(produtoId);
        pagamentoService.cobrar(clienteId);
    }
}

```

## 5. 📬 **JMS (Java Messaging Service)**

### O que é?

- JMS é uma API para comunicação assíncrona por **mensagens** (por exemplo, com ActiveMQ, RabbitMQ).
    
- Spring oferece integração via `JmsTemplate` e anotação `@JmsListener`.
    

### Exemplo:

**Enviando mensagem:**
```java
@Autowired
private JmsTemplate jmsTemplate;

public void enviarMensagem() {
    jmsTemplate.convertAndSend("filaPedidos", "Pedido recebido");
}

```

**Recebendo:**

```java
@JmsListener(destination = "filaPedidos")
public void receberMensagem(String conteudo) {
    System.out.println("Recebido: " + conteudo);
}

```

## 📊 Como tudo se conecta:

| Conceito   | Função                               | Exemplo                       | Integração no Spring           |
| ---------- | ------------------------------------ | ----------------------------- | ------------------------------ |
| JDBC       | Acesso direto ao banco com SQL       | `JdbcTemplate`                | `spring-boot-starter-jdbc`     |
| ORM        | Mapeamento objeto-relacional         | `JPA/Hibernate`               | `spring-boot-starter-data-jpa` |
| OXM        | Serializar objetos em XML            | `JAXB`                        | `spring-boot-starter-oxm`      |
| Transações | Controlar mudanças atômicas no banco | `@Transactional`              | `spring-tx`                    |
| JMS        | Mensageria assíncrona                | `JmsTemplate`, `@JmsListener` | `spring-boot-starter-activemq` |

# Repository Pattern
![Captura de tela de 2025-04-09 11-04-14](https://github.com/user-attachments/assets/6c008d24-3fbc-44cb-9a69-1ad707554e1f)


## 📦 O que é o **Repository Pattern**?

O **Repository Pattern** é um padrão de projeto que isola a **lógica de acesso a dados** do restante da aplicação. Ele é composto por uma estrutura em camadas:

```css
[ Controller ] → [ Service ] → [ Repository ] → [ Banco de Dados ]
```

Cada camada tem uma **responsabilidade única** (princípio SRP — Single Responsibility Principle).

## 🧩 As partes que compõem o padrão

### 1. ✅ **Controller**

#### 📌 Função:

- Lida com as **requisições HTTP**.
    
- Recebe os dados da requisição, valida, e chama o **Service**.
    
- Retorna a resposta HTTP (JSON, status code, etc.).
    

#### 🧠 Regra: o Controller **nunca acessa o Repository diretamente**.

#### 📁 Exemplo:
```java
@RestController
@RequestMapping("/produtos")
public class ProdutoController {

    @Autowired
    private ProdutoService produtoService;

    @GetMapping
    public List<Produto> listar() {
        return produtoService.listarProdutos();
    }

    @PostMapping
    public Produto salvar(@RequestBody Produto produto) {
        return produtoService.salvarProduto(produto);
    }
}

```
### 2. ⚙️ **Service**

#### 📌 Função:

- Contém a **lógica de negócio** da aplicação.
    
- Processa os dados, faz validações, e chama o **Repository**.
    
- É onde ficam as regras específicas do domínio (ex: "um produto não pode ser salvo com preço negativo").
    

#### 🧠 Regras:

- Pode chamar um ou mais Repositories.
    
- Pode ser reutilizado por vários Controllers.
    

#### 📁 Exemplo:
```java
@Service
public class ProdutoService {

    @Autowired
    private ProdutoRepository produtoRepository;

    public List<Produto> listarProdutos() {
        return produtoRepository.findAll();
    }

    public Produto salvarProduto(Produto produto) {
        if (produto.getPreco() < 0) {
            throw new IllegalArgumentException("Preço inválido");
        }
        return produtoRepository.save(produto);
    }
}

```

### 3. 🗃️ **Repository**

#### 📌 Função:

- Lida com o **acesso ao banco de dados**.
    
- Fornece métodos prontos para salvar, buscar, atualizar e deletar dados.
    
- Com Spring Data JPA, você **não precisa implementar nada manualmente**.
    

#### 📁 Exemplo:
```java
@Repository
public interface ProdutoRepository extends JpaRepository<Produto, Long> {
    // O Spring cria os métodos automaticamente
    List<Produto> findByNomeContaining(String nome);
}
```

## 📐 Arquitetura visual

```text
+------------------+          +--------------------+        +------------------------+
|   Controller     |  --->    |     Service        |  --->  |      Repository        |
| - Lida com HTTP  |          | - Lógica de negócio|        | - Acesso a dados       |
+------------------+          +--------------------+        +------------------------+
        ↑                            ↑                               ↑
     Frontend                  Regras, validações              JPA, Hibernate, SQL

```

# Models
Em Spring Boot, os **models** (também chamados de entidades ou domain objects) representam objetos do mundo real que são mapeados para tabelas no banco de dados, utilizando anotações como `@Entity`. Cada atributo da classe representa uma coluna da tabela, como `id`, `nome`, `preço`, etc. Essas classes são essenciais para a camada de persistência da aplicação.

Para evitar repetição de código (como criação manual de getters, setters, construtores e métodos como `toString`, `equals` e `hashCode`), usamos a biblioteca **Lombok**. Com ela, basta anotar a classe com `@Data` para que todos esses métodos sejam gerados automaticamente. As anotações `@NoArgsConstructor` e `@AllArgsConstructor` criam, respectivamente, um construtor sem parâmetros e um construtor com todos os atributos da classe. Isso torna o código mais limpo, reduz o boilerplate e melhora a produtividade no desenvolvimento.

No nosso projeto, temos os seguintes exemplos de uso:

### 🧾 Model: `Review`

```java
package com.pokemonreview.api.models;  
  
  
import jakarta.persistence.Entity;  
import jakarta.persistence.GeneratedValue;  
import jakarta.persistence.GenerationType;  
import jakarta.persistence.Id;  
import lombok.AllArgsConstructor;  
import lombok.Data;  
import lombok.NoArgsConstructor;  
  
@Data  
@AllArgsConstructor  
@NoArgsConstructor  
@Entity  
public class Review {  
    @Id  
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    private int id;  
    private String title;  
    private String content;  
    private int start;  
}
```
**Explicações:**

- `@Entity`: indica que essa classe é uma entidade JPA, e será mapeada para uma tabela no banco.
    
- `@Id`: identifica o campo `id` como chave primária.
    
- `@GeneratedValue(strategy = GenerationType.IDENTITY)`: define que o valor do ID será gerado automaticamente pelo banco.
    
- `@Data`: gera automaticamente os métodos `get`, `set`, `toString`, `equals`, e `hashCode`.
    
- `@NoArgsConstructor` e `@AllArgsConstructor`: criam os construtores padrão e completo, facilitando a criação e manipulação dos objetos.

### 🧾 Model: `Pokemon`

```java

package com.pokemonreview.api.models;  
  
  
import jakarta.persistence.Entity;  
import jakarta.persistence.GeneratedValue;  
import jakarta.persistence.GenerationType;  
import jakarta.persistence.Id;  
import lombok.AllArgsConstructor;  
import lombok.Data;  
import lombok.NoArgsConstructor;  
  
@Data  
@AllArgsConstructor  
@NoArgsConstructor  
@Entity  
public class Pokemon {  
    @Id  
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    private int id;  
    private String name;  
    private String type;  
  
}
```
**Explicações:**

- Assim como em `Review`, o `Pokemon` é uma entidade mapeada para uma tabela.
    
- Os campos `id`, `name` e `type` serão colunas da tabela `pokemon`.
    
- Todas as anotações de Lombok (`@Data`, `@NoArgsConstructor`, `@AllArgsConstructor`) são usadas da mesma forma, garantindo a geração automática dos métodos e construtores
```
### Controller
### 🎮 Papel do Controller

O `Controller` em Spring Boot é responsável por **receber as requisições HTTP** da aplicação (por exemplo, do navegador, frontend ou de outro sistema) e **retornar respostas**. Ele funciona como a **porta de entrada da API**.

No padrão MVC ou Repository Pattern, o `Controller` **não deve conter lógica de negócio complexa**, apenas **coordenar a chamada das outras camadas** (como `Service` e `Repository`) e devolver os resultados ao cliente.

---

### 🔍 Analisando o código enviado

```java
@RestController
@RequestMapping("/api/")
public class PokemonController {
---
@RestController: define que esta classe é um controller REST que vai responder com JSON.

@RequestMapping("/api/"): define o caminho base da rota da API.

```java
@GetMapping("pokemon")
public ResponseEntity<List<Pokemon>> getPokemons() {
    List<Pokemon> pokemons = new ArrayList<>();
    pokemons.add(new Pokemon(1, "Squirtle", "Water"));
    pokemons.add(new Pokemon(2, "Pikachu", "Electric"));
    pokemons.add(new Pokemon(3, "Charmander", "Fire"));
    return ResponseEntity.ok(pokemons);
}

```



- `@GetMapping("pokemon")`: mapeia requisições HTTP GET feitas para `/api/pokemon`.

### Pacote `repository` no Repository Pattern

No padrão Repository (Repository Pattern), a aplicação é dividida em **camadas com responsabilidades claras**. Uma dessas camadas é a **camada de acesso a dados**, responsável por lidar com a comunicação com o banco de dados. Essa camada normalmente é colocada dentro de um pacote chamado `repository`.

**Motivo do nome:**  
Colocar essa interface dentro do pacote `repository` ajuda a **organizar** e **separar as responsabilidades** da aplicação. Assim, sabemos que tudo ali tem a ver com a persistência de dados (salvar, buscar, deletar, atualizar no banco).

---

### 🔍 Explicando o código

```java
package com.pokemonreview.api.repository;

import com.pokemonreview.api.models.Pokemon;
import org.springframework.data.jpa.repository.JpaRepository;

public interface PokemonRepository extends JpaRepository<Pokemon, Integer> {
}

```

### Repository

#### 👉 O que está acontecendo aqui:

- `PokemonRepository` é uma **interface**, não uma classe. Isso é possível porque o Spring Data JPA gera automaticamente a implementação dessa interface para você.
    
- `extends JpaRepository<Pokemon, Integer>`:
    
    - Diz que essa interface vai **gerenciar a entidade `Pokemon`**.
        
    - O segundo parâmetro `Integer` é o **tipo do ID** da entidade (no caso, o `id` do `Pokemon`).
        

Com isso, o Spring já te dá de forma automática métodos prontos como:

- `findAll()` — listar todos os Pokémons
    
- `findById(id)` — buscar por ID
    
- `save(pokemon)` — salvar ou atualizar
    
- `deleteById(id)` — deletar por ID
    
- Dentro do método:
    
    - É criada uma lista de objetos `Pokemon` **manualmente** (mock).
        
    - Essa lista é retornada com o status 200 (OK) usando `ResponseEntity.ok(...)`.
