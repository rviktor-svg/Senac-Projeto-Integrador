# Senac-Projeto-Integrador
# 📚 Book Exchange

Plataforma web para organizar bibliotecas pessoais e facilitar a troca de livros físicos entre leitores.

**Projeto Integrador V — Tecnologia em Análise e Desenvolvimento de Sistemas — Centro Universitário Senac**

## Equipe (Grupo 22)

- Felipe Casisini Batista
- Guilherme Duarte Da Costa
- Carlos Evangelista Reis
- Diogo Fernando Ribeiro
- Rogerio Jose
- Marcos Junior Silva
- Renan Oliveira Dos Santos
- Victor Rodrigues De Souza

Orientador: Prof. Adriano Kleber Milanez

## Funcionalidades

- **Conta de leitor:** cadastro e login com e-mail e senha (a senha é guardada criptografada com BCrypt).
- **Minha estante:** cadastro dos livros pessoais, com marcação de quais estão disponíveis para troca, e remoção.
- **Catálogo de troca:** lista pública dos livros disponíveis, com filtro por gênero.
- **Interesses:** o leitor demonstra interesse em um livro de outra pessoa, e o dono do livro aceita ou recusa.
- **Segurança:** cada leitor só altera os próprios livros e só responde aos interesses nos seus livros. O servidor identifica o leitor pela sessão do login, nunca por um ID enviado pela página.

## Estrutura do repositório

```
book-exchange/
├── database/          # Modelo físico do banco de dados (PostgreSQL)
│   └── modelo_fisico.sql
├── backend/           # API REST em Java + Spring Boot
├── frontend/          # Aplicação web em Angular
├── landing-page/      # Página de apresentação (HTML/CSS), publicada no GitHub Pages
└── docs/              # Relatório do Projeto Integrador e roteiro do vídeo
```

## Tecnologias utilizadas

| Camada         | Tecnologia                    |
|----------------|-------------------------------|
| Frontend       | Angular 17                    |
| Backend        | Java 17 + Spring Boot 3.2     |
| Banco de dados | PostgreSQL 15                 |
| Landing page   | HTML5 + CSS3 (GitHub Pages)   |

## Pré-requisitos

- [Java JDK](https://adoptium.net/) 17 ou superior
- [Maven](https://maven.apache.org/download.cgi) 3.9 ou superior
- [Node.js](https://nodejs.org/) 18.13 ou superior (recomendado: 20 LTS)
- [PostgreSQL](https://www.postgresql.org/download/) 15 ou superior

Para conferir as versões instaladas: `java -version`, `mvn -v`, `node -v` e `psql --version`.

> No Windows, se o `mvn` não for reconhecido no PowerShell, adicione a pasta `bin` do Maven às variáveis de ambiente (PATH) e abra um novo terminal.

## Como executar o projeto localmente

O sistema tem três partes, e você usa **dois terminais** abertos ao mesmo tempo: um para o backend e outro para o frontend.

### 1. Banco de dados

```bash
createdb -U postgres book_exchange
psql -U postgres -d book_exchange -f database/modelo_fisico.sql
```

O script cria as tabelas. Para testar, crie contas pela tela **Criar conta** do sistema. Os usuários de exemplo que o script possa conter não têm senha válida e não conseguem entrar.

### 2. Backend (porta 8080) — terminal 1

Edite `backend/src/main/resources/application.properties` com o usuário e a senha do seu PostgreSQL:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/book_exchange?stringtype=unspecified
spring.datasource.username=postgres
spring.datasource.password=SUA_SENHA_AQUI
```

> O trecho `?stringtype=unspecified` no final da URL é necessário porque o banco usa colunas do tipo ENUM. **Não envie sua senha real para o GitHub.**

Depois, rode:

```bash
cd backend
mvn spring-boot:run
```

Quando aparecer `Started BookExchangeApplication`, o backend está no ar. Deixe esse terminal aberto. O backend só serve dados (API); abrir `http://localhost:8080` no navegador mostra uma página de erro 404, e isso é normal.

### 3. Frontend (porta 4200) — terminal 2

```bash
cd frontend
npm install
npm start
```

O `npm install` só é necessário na primeira vez. Quando aparecer `Compiled successfully`, acesse:

**http://localhost:4200**

O frontend encaminha as chamadas `/api` para o backend por meio do arquivo `frontend/proxy.conf.json`. Por isso o backend precisa estar rodando na porta 8080.

### Roteiro rápido de teste

1. Crie uma conta e cadastre um livro marcado como **Disponível para troca**.
2. Clique em **Sair**, crie uma segunda conta e, no **Catálogo**, clique em **Tenho interesse** no livro da primeira.
3. Entre novamente na primeira conta, abra **Interesses** e clique em **Aceitar**.

## Resumo da API

| Método e rota | Descrição | Exige login |
|---|---|---|
| `POST /api/usuarios/cadastro` | Cria uma conta | Não |
| `POST /api/auth/login` | Entra com e-mail e senha | Não |
| `POST /api/auth/logout` | Sai da conta | Não |
| `GET /api/auth/me` | Mostra quem está logado | Sim |
| `GET /api/livros/catalogo?genero=` | Livros disponíveis para troca | Não |
| `GET /api/livros/estante` | Estante de quem está logado | Sim |
| `POST /api/livros` | Cadastra um livro na estante | Sim |
| `PUT /api/livros/{id}/disponibilidade?valor=` | Marca como `TROCA` ou `COLECAO` | Sim (dono do livro) |
| `DELETE /api/livros/{id}` | Remove um livro | Sim (dono do livro) |
| `POST /api/interesses` | Demonstra interesse em um livro | Sim |
| `GET /api/interesses/enviados` | Interesses que eu demonstrei | Sim |
| `GET /api/interesses/recebidos` | Interesses nos meus livros | Sim |
| `PUT /api/interesses/{id}/status?valor=` | Aceita (`ACEITO`) ou recusa (`RECUSADO`) | Sim (dono do livro) |

## Publicando a Landing Page no GitHub Pages

A landing page (`landing-page/index.html`) é apenas uma página de apresentação do projeto. O botão **Experimentar** abre `http://localhost:4200`, que só funciona para quem estiver com o projeto rodando no próprio computador.

O GitHub Pages só consegue publicar a raiz do repositório ou uma pasta chamada `docs`. Como a landing page está em `landing-page/`, a publicação é feita por um workflow do GitHub Actions:

1. Copie o arquivo `pages.yml` para `.github/workflows/pages.yml` no repositório.
2. No GitHub, abra **Settings → Pages** e, em **Source**, escolha **GitHub Actions**.
3. Faça um commit na branch `main` alterando algo em `landing-page/` (ou rode o workflow manualmente na aba **Actions**).
4. A página ficará disponível em `https://SEU-USUARIO.github.io/book-exchange/`.

## Vídeo demonstrativo

O vídeo de até 1 minuto, mostrando o funcionamento da aplicação, deve ser gravado com backend e frontend rodando e anexado na entrega do Blackboard. O roteiro sugerido está em `docs/roteiro-video.md`.

## Limitações conhecidas

- Ao aceitar um interesse, o sistema apenas altera a situação dele para "Aceito". A troca em si (tabela `trocas`) ainda não é registrada.
- Não há recuperação de senha nem limite de tentativas de login.
- As sessões ficam na memória do servidor, então reiniciar o backend desconecta todos os usuários.

## Licença

Uso exclusivamente acadêmico — Projeto Integrador, Centro Universitário
