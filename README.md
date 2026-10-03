# Catalog — Introdução a Domain-Driven Design

Projeto desenvolvido durante o **Bootcamp CI&T – Java com IA (DIO)**, no módulo de introdução a DDD com Spring Boot.

A ideia aqui é entender bem as fronteiras entre **domínio**, **aplicação** e **infraestrutura** antes de colocar qualquer API web no meio. Por isso o projeto não tem controller: o ponto de entrada é um teste de integração.

## O que o projeto faz

Adiciona um livro ao catálogo a partir do ISBN:

1. Cria o value object `Isbn` (que já valida o número).
2. Executa o caso de uso `AddBookToCatalogUseCase`.
3. Busca os dados do livro na API pública da [Open Library](https://openlibrary.org) através da porta `BookSearchGateway`.
4. Monta a entidade de domínio `Book`.
5. Persiste via `BookRepository` (implementado com Spring Data JPA + PostgreSQL).

## Tecnologias

- Java 25
- Spring Boot 4
- Spring Data JPA + PostgreSQL
- Spring `RestClient`
- Apache Commons Validator (validação de ISBN)
- Lombok
- Testcontainers + JUnit 5
- Docker Compose

## Estrutura

```text
src/main/java/dio/catalog
├── domain/           -> Book, BookId, Isbn e o contrato BookRepository
├── application/      -> AddBookToCatalogUseCase e a porta BookSearchGateway
└── infrastructure/
    ├── integration/  -> cliente da Open Library (RestClient)
    └── persistence/  -> entidade JPA e implementação do repositório
```

## O que aprendi

- **Camadas do DDD**: o `domain` fala a linguagem do negócio e não conhece framework; o `application` orquestra o caso de uso; o `infrastructure` contém os adaptadores (HTTP externo, banco).
- **Class vs record**: `class` para entidades com identidade (`Book`), `record` para value objects imutáveis (`Isbn`, `BookId`).
- **Identificadores fortemente tipados**: `BookId` em vez de um `UUID` solto evita trocar um ID por outro sem perceber.
- **Repository pattern**: o contrato `BookRepository` fica no domínio, e a implementação com JPA (`JpaBookRepository`) fica na infraestrutura. Trocar o banco vira uma troca de adaptador.
- **Ports & adapters**: a aplicação depende de `BookSearchGateway`, não da Open Library. A API externa pode mudar sem afetar o caso de uso.
- **Testcontainers**: o teste sobe um PostgreSQL de verdade em container para validar a persistência.

## Como rodar

Pré-requisitos: Docker em execução (para o Testcontainers) e acesso à internet (para a Open Library).
O JDK 25 é baixado automaticamente pelo Gradle se não estiver instalado.

```bash
./gradlew test --tests "dio.catalog.application.AddBookToCatalogUseCaseTest"
```

Para subir a aplicação com o PostgreSQL do `compose.yml`:

```bash
./gradlew bootRun
```
