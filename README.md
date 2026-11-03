# Guia de Projetos Práticos: Spring Boot API REST com Microsoft SQL Server (1 Entidade)

**Duração Total:** 8 Horas (4 encontros de 2 horas)  
**Nível:** Ensino Médio / Técnico  
**Foco:** Backend Completo em Java com Spring Boot e Microsoft SQL Server (DDL SQL, Entity, Repository, Controller, Validações e Regras de Negócio)

---

## 📅 Cronograma de Entregas por Aula (4 Semanas / 2h por Encontro)

O cronograma foi planejado para que compreendam todo o fluxo de engenharia de software no backend, iniciando pela **modelagem relacional em SQL Server** (escrita e execução de scripts DDL manuais) até a integração com Spring Data JPA, regras de negócio e testes via cliente HTTP.

```mermaid
gantt
    title Cronograma de Execução: Spring Boot + SQL Server (8 Horas Totais)
    dateFormat  DD/MM
    section Sprint Backend
    Script SQL Server, Conexão e JPA       :done, 09/11, 1d
    Controller REST e Operações Básicas    :active, 16/11, 1d
    Regras de Negócio e Validações:crit, 23/11, 1d
    Tratamento de Exceções & Testes Finais :crit, 30/11, 1d
```

### Aula 1 — 09/11: Script SQL Server, Conexão e Mapeamento JPA
* **Objetivo:** Criar a tabela no Microsoft SQL Server via script DDL, configurar o Spring Boot para conectar ao SQL Server e mapear a `@Entity` correspondente.
* **Entregáveis:**
  * **Script DDL SQL Server (`schema.sql`):** Criação da tabela com tipos nativos do SQL Server (`BIGINT IDENTITY(1,1)`, `VARCHAR`, `DATETIME2`, `BIT`, `DECIMAL`), chaves primárias e restrições `NOT NULL`.
  * Criação do projeto Spring Boot com as dependências: `Spring Web`, `Spring Data JPA`, `MS SQL Server Driver` (`com.microsoft.sqlserver:mssql-jdbc`), `Lombok` e `Validation`.
  * Configuração do `application.properties` com URL de conexão JDBC do SQL Server (`jdbc:sqlserver://...`), usuário, senha e `spring.jpa.hibernate.ddl-auto=validate` (garantindo que o script criado seja respeitado).
  * Classe `@Entity` mapeada com `@Table`, `@Id`, `@GeneratedValue(strategy = GenerationType.IDENTITY)` e anotações `@Column`.
  * Interface `Repository` estendendo `JpaRepository<Entidade, Long>`.
  * Aplicação iniciando com conexão estabelecida e validada com o SQL Server.

### Aula 2 — 16/11: Camada Web e Operações CRUD Básicas
* **Objetivo:** Implementar o `@RestController` e expor as operações fundamentais de leitura, inserção e remoção integradas ao SQL Server.
* **Entregáveis:**
  * Classe `@RestController` com anotação `@RequestMapping("/api/...")`.
  * Endpoint `GET` para listagem completa retornando `ResponseEntity<List<Entidade>>`.
  * Endpoint `GET /{id}` para busca individual com verificação de existência (`Optional` ou `orElseThrow`).
  * Endpoint `POST` para criação inicial do registro (`HttpStatus.CREATED`) persistindo no SQL Server.
  * Endpoint `DELETE /{id}` com retorno `HttpStatus.NO_CONTENT`.
  * Coleção de requisições criada e validada no Postman, Insomnia ou Bruno.

### Aula 3 — 23/11: Regras de Negócio
* **Objetivo:** Implementação da regra central do tema.
* **Entregáveis:**
  * Implementação da **Regra de Negócio** (cálculos automáticos, validações lógicas e transições de status).
  * Endpoint específico exposto no Controller (`PATCH` ou `POST` de ação) invocando a regra de negócio.

### Aula 4 — 30/11: Validações, Tratamento de Erros e Homologação
* **Objetivo:** Implementar validações e finalizar os testes integrados.
* **Entregáveis:**
  * Validações de entrada na requisição (`NotNull`, `NotBlank`, `Min`, `Max`, etc).
  * Retorno de status `400 Bad Request` ou `409 Conflict`.
  * Bateria de testes com cenários de sucesso (status 200/201) e cenários de falha da regra de negócio (status 400/404/409).

---

### 1. Configuração do `application.properties`
```properties
# Conexão com Microsoft SQL Server
spring.datasource.url=jdbc:sqlserver://localhost:1433;databaseName=EscolaDB;encrypt=true;trustServerCertificate=true
spring.datasource.username=sa
spring.datasource.password=SuaSenhaForte123!
spring.datasource.driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver

# Hibernate / JPA
# "validate" valida se o script DDL executado no SQL Server confere exatamente com as anotações Java
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.SQLServerDialect
```

---

## 🏛️ Arquitetura de Camadas Esperada no Projeto

```mermaid
graph TD
    subgraph cliente["Testes de API (Postman / Bruno / Insomnia)"]
        REQ["JSON Request / Endpoints HTTP"]
    end

    subgraph spring["Arquitetura em Camadas (Java)"]
        C["@RestController (Rotas, Regras de Negócio e Códigos HTTP)"]
        R["@Repository (Spring Data JPA)"]
        E["@Entity (Mapeamento Objeto-Relacional)"]
    end

    subgraph banco["Banco de Dados Relacional"]
        SCRIPT["📜 Script DDL (CREATE TABLE)"] -.-> MSSQL[("Microsoft SQL Server")]
        R <-->|"Conexão JDBC (Porta 1433)"| MSSQL
    end

    REQ <-->|HTTP 200, 201, 400, 404| C
    C --> R
    R -.-> E
```

---

## 🚀 O Projeto Prático com Script SQL Server e Regras de Negócio

---

### 5. Mural de Apoio Psicológico e Escuta ("Desabafos Anônimos")
* **Contexto:** Canal anônimo para a equipe pedagógica acolher queixas, relatos de conflitos ou sobrecarga escolar com triagem.
* **Entidade:** `MensagemAcolhimento`
  * Atributos: `id` (Long), `assunto` (String), `conteudo` (String), `turmaOrigem` (String), `grauUrgencia` (String: `BAIXA`, `MEDIA`, `ALTA`), `respondida` (Boolean), `dataEnvio` (LocalDate)
* **Endpoints:**
  * `GET /api/acolhimento` — Lista mensagens da coordenação.
  * `GET /api/acolhimento/{id}` — Consulta relato individual.
  * `POST /api/acolhimento` — **Regra de Negócio:** Analisa termos críticos para definir o `grauUrgencia`.
  * `PUT /api/acolhimento/{id}` — Modificação da urgência pelo pedagogo.
  * `DELETE /api/acolhimento/{id}` — Arquiva a mensagem.
  * `PATCH /api/acolhimento/{id}/atender` — Marca a mensagem como respondida.
* **Pseudocódigo da Regra de Negócio:**
```text
FUNCAO cadastrarMensagem(mensagem):
    mensagem.dataEnvio = DATA_ATUAL
    mensagem.respondida = FALSO
    texto = MINUSCULAS(mensagem.conteudo)

    SE texto CONTEM "urgente" OU texto CONTEM "socorro" OU texto CONTEM "crise" ENTAO:
        mensagem.grauUrgencia = "ALTA"
    SENAO_SE texto CONTEM "ansiedade" OU texto CONTEM "triste" OU texto CONTEM "conflito" ENTAO:
        mensagem.grauUrgencia = "MEDIA"
    SENAO:
        mensagem.grauUrgencia = "BAIXA"
    FIM_SE

    RETORNAR salvar(mensagem)
FIM_FUNCAO
```

---
