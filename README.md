# DSCatalog

API REST desenvolvida em **Java** com **Spring Boot**, para gerenciamento de um catálogo de produtos, com autenticação e autorização via **OAuth2 + JWT**.

Projeto desenvolvido durante o curso [Java Spring Expert](https://devsuperior.club/courses/6), da [Dev Superior](https://devsuperior.club/).

## 📋 Sobre o projeto

O DSCatalog é uma API para gestão de produtos e categorias, contemplando as principais práticas de mercado para o desenvolvimento de uma aplicação backend profissional: arquitetura em camadas, tratamento de exceções centralizado, validações customizadas, testes automatizados (unitários e de integração) e um servidor de autorização OAuth2 próprio.

## 🚀 Tecnologias utilizadas

- Java
- Spring Boot
- Spring Data JPA / Hibernate
- Spring Security + OAuth2 Authorization Server
- JWT (JSON Web Token)
- Bean Validation
- H2 Database
- JUnit 5 e Mockito
- Maven

## 🗂️ Domínio da aplicação

- **Category**: categorias dos produtos
- **Product**: produtos do catálogo, associados a uma ou mais categorias (`@ManyToMany`)
- **User**: usuários do sistema, associados a uma ou mais roles (`@ManyToMany`)
- **Role**: papéis de acesso (`ROLE_ADMIN`, `ROLE_OPERATOR`)

## 🏗️ Arquitetura

O projeto segue uma arquitetura em camadas:

- **Resources**: controllers REST
- **Services**: regras de negócio
- **Repositories**: acesso a dados (Spring Data JPA)
- **DTOs**: objetos de transferência de dados, isolando as entidades da camada de exposição da API
- **Exceptions**: tratamento centralizado de erros via `@ControllerAdvice`

### Tratamento de exceções

| Exceção | Status HTTP | Descrição |
|---|---|---|
| `ResourceNotFoundException` | 404 | Recurso não encontrado |
| `DatabaseException` | 400 | Violação de integridade referencial |
| `MethodArgumentNotValidException` | 422 | Erros de validação de campos, com detalhamento por campo |

### Validações

Além das validações padrão do Bean Validation (`@NotBlank`, `@Size`, `@Positive`, `@Email`, `@PastOrPresent`), o projeto conta com validadores customizados:

- `@UserInsertValid`: impede o cadastro de um usuário com e-mail já existente
- `@UserUpdateValid`: impede a atualização de um usuário para um e-mail já usado por outro usuário

## 🔐 Segurança

A autenticação é feita via **OAuth2**, com um servidor de autorização próprio e um grant type `password` customizado (implementado do zero, já que essa modalidade foi descontinuada no fluxo padrão do Spring Security):

- **Authorization Server**: emite tokens JWT assinados com chave RSA
- **Resource Server**: valida os tokens JWT recebidos nas requisições
- **Grant customizado**: autentica usuário e senha diretamente contra o banco de dados, embutindo as roles do usuário nas claims do token

### Autorização por rota

| Recurso | Leitura | Escrita (insert/update/delete) |
|---|---|---|
| `/categories` | Pública | `ROLE_ADMIN` ou `ROLE_OPERATOR` |
| `/products` | Pública | `ROLE_ADMIN` ou `ROLE_OPERATOR` |
| `/users` | `ROLE_ADMIN` | `ROLE_ADMIN` |

## 🧪 Testes

O projeto conta com testes em diferentes camadas:

- **Testes unitários** (Mockito): regras de negócio dos services isoladas do banco de dados
- **Testes de repositório** (`@DataJpaTest`): validação das queries e do mapeamento JPA
- **Testes de controller** (`@WebMvcTest`): validação da camada web isolada, com segurança desabilitada
- **Testes de integração** (`@SpringBootTest`): fluxo completo, incluindo obtenção de um token JWT real para testar rotas protegidas

## ▶️ Como executar

```bash
git clone https://github.com/obrenoxs/dscatalog.git
cd dscatalog
./mvnw spring-boot:run
```

A aplicação sobe com o perfil de testes ativo, utilizando o banco de dados H2 em memória, populado automaticamente com dados de exemplo (categorias, produtos e usuários).

Console do H2 disponível em `/h2-console`.

## 📌 Endpoints principais

| Método | Endpoint | Descrição |
|---|---|---|
| GET | `/categories` | Lista categorias (paginado) |
| GET | `/categories/{id}` | Busca categoria por id |
| POST | `/categories` | Cria categoria |
| PUT | `/categories/{id}` | Atualiza categoria |
| DELETE | `/categories/{id}` | Remove categoria |
| GET | `/products` | Lista produtos (paginado) |
| GET | `/products/{id}` | Busca produto por id |
| POST | `/products` | Cria produto |
| PUT | `/products/{id}` | Atualiza produto |
| DELETE | `/products/{id}` | Remove produto |
| GET | `/users` | Lista usuários (paginado) — requer `ROLE_ADMIN` |
| GET | `/users/{id}` | Busca usuário por id — requer `ROLE_ADMIN` |
| POST | `/users` | Cria usuário — requer `ROLE_ADMIN` |
| PUT | `/users/{id}` | Atualiza usuário — requer `ROLE_ADMIN` |
| DELETE | `/users/{id}` | Remove usuário — requer `ROLE_ADMIN` |
| POST | `/oauth2/token` | Autenticação (grant type `password`), retorna o access token JWT |

## 👤 Autor

Desenvolvido por [Breno Oliveira de Souza](https://github.com/obrenoxs) durante o curso Java Spring Expert, da Dev Superior.
