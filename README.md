# Health Hub

A modular Spring Boot platform that unifies personal health tracking, workout planning, and medication management for both individuals and care teams.

## Table of Contents

1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Feature Highlights](#feature-highlights)
4. [Tech Stack](#tech-stack)
5. [Getting Started](#getting-started)
6. [Configuration](#configuration)
7. [Database & Migrations](#database--migrations)
8. [Testing](#testing)
9. [Roadmap](#roadmap)

## Overview

Health Hub consolida dados clínicos e hábitos saudáveis em um único backend REST. A plataforma foi pensada para escalar em ambientes corporativos, proporcionando observabilidade, governança de dados e integração simples com canais web ou mobile.

## Architecture

- **Backend:** API Java/Spring Boot 4 com camadas bem definidas (domain, repositories, adapters REST).
- **Persistence:** PostgreSQL 15+ gerenciado via Flyway.
- **Packaging:** Aplicação `jar` pronta para containerização e deploy em qualquer orquestrador.
- **Observabilidade (roadmap):** pontos de extensão preparados para métricas Micrometer/Prometheus e tracing.

```
src/
  main/java/com/healthhub/api
    ApiApplication.java
    domain/user
    repositories
  main/resources
    application.properties
    db/migration
```

## Feature Highlights

- Registro de sinais vitais (pressão, frequência cardíaca, glicemia, peso).
- Fichas de treino com metas e acompanhamento de progresso.
- Controle de medicamentos com lembretes e histórico.
- Curadoria de conteúdo de saúde para educação contínua.

## Tech Stack

| Camada            | Tecnologia                         |
|-------------------|------------------------------------|
| Linguagem         | Java 25 (compatível 21+)           |
| Framework Web     | Spring Boot 4 (Web MVC)            |
| ORM               | Spring Data JPA / Hibernate 7      |
| Banco de dados    | PostgreSQL                         |
| Migrações         | Flyway                            |
| Build/Dependency  | Maven Wrapper (`mvnw`)             |

## Getting Started

### 1. Pré-requisitos

- Java 21 ou superior instalado e no `PATH`.
- Maven Wrapper já incluso; não é necessário Maven global.
- PostgreSQL disponível localmente (via Homebrew, Docker ou provisionado pela sua cloud).

### 2. Clonar e preparar o projeto

```bash
git clone <repo-url> api
cd api
./mvnw clean verify
```

### 3. Provisionar o banco

```bash
# instale o PostgreSQL (exemplo Homebrew)
brew install postgresql && brew services start postgresql

# criar usuário e banco compatíveis com application.properties
psql postgres <<'SQL'
DO $$
BEGIN
   IF NOT EXISTS (SELECT FROM pg_roles WHERE rolname = 'schulzdimitri') THEN
      CREATE ROLE schulzdimitri LOGIN PASSWORD 'password';
   END IF;
END$$;

CREATE DATABASE healthhub OWNER schulzdimitri;
GRANT ALL PRIVILEGES ON DATABASE healthhub TO schulzdimitri;
SQL
```

### 4. Executar a API

```bash
./mvnw spring-boot:run
```

A aplicação inicia em `http://localhost:8080` e executa automaticamente as migrations Flyway no banco configurado.

## Configuration

Variáveis padrão (arquivo `src/main/resources/application.properties`):

```properties
spring.application.name=api
spring.datasource.url=jdbc:postgresql://localhost:5432/healthhub
spring.datasource.username=schulzdimitri
spring.datasource.password=password
```

Para ambientes corporativos, recomenda-se sobrescrever via variáveis de ambiente ou profiles (`application-prod.properties`).

## Database & Migrations

### Database Entity-Relationship (DER)

<img width="3528" height="3524" alt="image" src="https://github.com/user-attachments/assets/c568d801-a55f-446e-a92c-01531f9df81e" />


- Scripts vivem em `src/main/resources/db/migration` seguindo o padrão `V<versão>__<descrição>.sql`.
- `V1__create-users-table.sql` cria a tabela `users` e habilita a extensão `pgcrypto`.
- Execute `./mvnw flyway:info` para auditar o estado das migrations.

## Testing

```bash
./mvnw test
```

Os testes de contexto garantem que o container Spring inicializa, conecta ao Postgres e aplica as migrations.

## Roadmap

- [ ] Expor endpoints públicos para CRUD de usuários e perfis de saúde.
- [ ] Adicionar autenticação OAuth2/OpenID Connect.
- [ ] Instrumentar métricas e tracing distribuído.
- [ ] Publicar imagens Docker oficiais.

