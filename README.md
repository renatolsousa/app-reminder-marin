
-----

# 🚀 API de Gestão de Usuários e Lembretes em NestJS

Este projeto consiste no desenvolvimento de uma API RESTful completa para gestão de usuários e lembretes, utilizando as melhores práticas e ferramentas do ecossistema NestJS, PostgreSQL, JWT e Docker.

-----

## 🎯 Objetivo

O principal objetivo desta API é fornecer as seguintes funcionalidades:

  * **Cadastro de Usuários**: Registro de novos usuários com nome e e-mail (utilizado como login).
  * **Autenticação**: Sistema de autenticação seguro via JSON Web Tokens (JWT).
  * **Gestão de Lembretes**: Capacidade para usuários autenticados criarem, listarem, editarem, marcarem como feitos e deletarem seus lembretes.

-----

## 🛠️ Requisitos Técnicos

### Stack Tecnológica

A API foi desenvolvida utilizando a seguinte stack:

  * **NestJS**: Framework Node.js progressivo e de código aberto para a construção de aplicações server-side escaláveis.
  * **PostgreSQL**: Sistema de gerenciamento de banco de dados relacional robusto e extensível.
  * **JWT (JSON Web Tokens)**: Padrão aberto e seguro para autenticação e troca de informações de forma compacta e auto-contida.
  * **Docker**: Plataforma para desenvolver, enviar e executar aplicações em containers isolados.
  * **ORM (Object-Relational Mapping)**: Escolha flexível entre Prisma ou TypeORM para interação com o banco de dados.

-----

## 💻 Instalação e Configuração

Siga os passos abaixo para configurar e executar o projeto em seu ambiente local.

### Instalação das Dependências do Projeto

Clone o repositório e instale as dependências:

```bash
# Iniciar o projeto NestJS (se for um novo projeto)
nest new nome-do-projeto

# Entrar na pasta do projeto
cd nome-do-projeto

# Instalar pacotes essenciais
npm install @nestjs/jwt @nestjs/passport passport passport-jwt bcryptjs class-validator class-transformer

# Instalar ORM e driver PostgreSQL
npm install @nestjs/typeorm typeorm pg
# ou (se preferir Prisma)
npm install @nestjs/prisma @prisma/client

# Instalar dependências de desenvolvimento
npm install --save-dev @types/bcryptjs
```

### Configuração do Docker

Crie um arquivo `docker-compose.yml` na raiz do seu projeto para configurar os serviços da aplicação e do banco de dados:

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - '3000:3000'
    volumes:
      - ./src:/app/src
    command: npm run start:dev
    depends_on:
      - db

  db:
    image: postgres:13
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: nestdb
    ports:
      - '5432:5432'
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

Crie também um `Dockerfile` na raiz do projeto:

```dockerfile
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "start:prod"]
```

Após criar os arquivos, execute o seguinte comando no terminal para subir os serviços do Docker:

```bash
docker-compose up -d
```

### Configuração do Banco de Dados na Aplicação

No arquivo `src/app.module.ts`, configure a conexão com o banco de dados. Um exemplo utilizando `TypeOrmModule.forRoot`:

```typescript
TypeOrmModule.forRoot({
  type: 'postgres',
  host: 'localhost',
  port: 5432,
  username: 'postgres',
  password: 'postgres',
  database: 'nestdb',
  autoLoadModels: true,
  synchronize: true,
}),
// Ou use variáveis de ambiente para maior segurança: process.env.DB_HOST, etc.
```

-----

## ⚙️ Funcionalidades do Sistema

### 1\. Autenticação de Usuário

#### Cadastro

  * **Endpoint**: `POST /api/auth/register`
  * **Campos**: `name`, `email`, `password`
  * **Validações**:
      * E-mail deve ser único.
      * Senha deve ter no mínimo 6 caracteres e ser armazenada de forma encriptada usando `bcryptjs`.
      * `name` deve ser obrigatório.

#### Login

  * **Endpoint**: `POST /api/auth/login`
  * **Campos**: `email`, `password`
  * **Retorno**: Na propriedade `access_token`, um JWT válido.

#### Proteção de Rotas

Todas as rotas sob `/api/*` que exibirem ou manipularem informações do usuário requerem autenticação com JWT. O token deve ser enviado no cabeçalho `Authorization: Bearer <token>`.

### 2\. Gestão de Lembretes

Todas as rotas de lembretes devem ser protegidas por autenticação JWT.

#### Criar Lembrete

  * **Endpoint**: `POST /api/reminders`
  * **Campos**: `title`, `description`, `dueDate`
  * **Detalhes**: O lembrete será associado automaticamente ao usuário autenticado.

#### Listar Todos os Lembretes

  * **Endpoint**: `GET /api/reminders`

#### Marcar Lembrete como Feito

  * **Endpoint**: `PATCH /api/reminders/:id/complete`
  * **Detalhes**: Atualiza o status do lembrete para concluído.

#### Editar Lembrete

  * **Endpoint**: `PUT /api/reminders/:id`
  * **Detalhes**: Permite alterar o título, descrição e data de vencimento de um lembrete existente.

#### Deletar Lembrete

  * **Endpoint**: `DELETE /api/reminders/:id`

-----

## 📝 Observações Finais

  * As rotas protegidas devem exigir o token JWT no cabeçalho **`Authorization: Bearer <token>`**.
  * Implementar validações de entrada robustas usando a biblioteca **`class-validator`**.
  * Garantir que as senhas sejam **hasheadas** utilizando `bcryptjs` na criação do usuário.
  * Os dados de usuário e lembretes devem estar **relacionados via relacionamento ORM** (ex: relacionamento OneToMany onde um usuário tem muitos lembretes).

-----

Espero que esta formatação seja útil para o seu `README.md`\! Se precisar de mais algum ajuste ou tiver alguma dúvida, é só me dizer.
