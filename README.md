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
