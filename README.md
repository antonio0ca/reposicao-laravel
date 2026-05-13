# Reposição — Programação Web com Laravel

---

## Como rodar o sistema de autenticação

### Pré-requisitos

- PHP 8.2+
- Composer
- Node.js + npm
- Um banco de dados (SQLite, MySQL ou PostgreSQL)

### Passo a passo

**1. Entrar na pasta do projeto**
```bash
cd src
```

**2. Instalar dependências PHP**
```bash
composer install
```

**3. Criar o arquivo de configuração**
```bash
cp .env.example .env
php artisan key:generate
```

**4. Configurar o banco de dados**

No arquivo `.env`, defina a conexão. Para SQLite (mais simples):
```env
DB_CONNECTION=sqlite
```
Depois crie o arquivo do banco:
```bash
touch database/database.sqlite
```

Para MySQL, altere no `.env`:
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=nome_do_banco
DB_USERNAME=seu_usuario
DB_PASSWORD=sua_senha
```

**5. Rodar as migrations (cria as tabelas)**
```bash
php artisan migrate
```

**6. Instalar dependências front-end e compilar assets**
```bash
npm install
npm run build
```

**7. Iniciar o servidor**
```bash
php artisan serve
```

Acesse em: `http://localhost:8000`

---

### Rotas disponíveis

| Rota | Método | Descrição | Acesso |
|---|---|---|---|
| `/` | GET | Página inicial | Público |
| `/register` | GET / POST | Cadastro de usuário | Somente visitantes |
| `/login` | GET / POST | Login | Somente visitantes |
| `/dashboard` | GET | Painel do usuário | Autenticado |
| `/profile` | GET / PATCH / DELETE | Editar perfil | Autenticado |
| `/logout` | POST | Encerrar sessão | Autenticado |

---

### Estrutura de arquivos relevante

```
src/
├── app/
│   ├── Http/Controllers/Auth/    ← Controllers de autenticação
│   └── Models/User.php           ← Model do usuário
├── database/migrations/          ← Migration da tabela users
├── resources/views/
│   ├── auth/                     ← Telas de login e cadastro
│   └── dashboard.blade.php       ← Painel pós-login
└── routes/
    ├── web.php                   ← Rotas principais
    └── auth.php                  ← Rotas de autenticação
```

---

## Atividade 1 — Conceitos Web e Laravel

**O que é programação no lado servidor?**

Programação no lado servidor é o conjunto de processos executados no servidor web, e não no navegador do usuário. Quando um cliente faz uma requisição, o servidor processa a lógica da aplicação, acessa o banco de dados, aplica regras de negócio e retorna uma resposta. Linguagens como PHP e frameworks como Laravel operam nesse modelo. Isso garante segurança, pois o código e os dados sensíveis nunca são expostos diretamente ao usuário final.

**O que é request e response?**

Request é a mensagem enviada pelo cliente ao servidor, contendo o método HTTP (GET, POST, PUT, DELETE), URL, cabeçalhos e corpo com dados. Response é a mensagem que o servidor retorna após processar o request, contendo um código de status HTTP (200, 404, 500 etc.), cabeçalhos e o corpo da resposta (HTML, JSON, etc.). Esses dois elementos formam o ciclo básico de comunicação da web.

**O que são sessões (session)?**

Sessões são mecanismos que permitem armazenar informações do usuário no servidor entre diferentes requisições HTTP. Como o protocolo HTTP é stateless, cada requisição é independente. As sessões resolvem isso criando um identificador único (session ID) enviado ao cliente via cookie. A cada nova requisição, o cliente envia esse ID e o servidor recupera os dados correspondentes, como informações de autenticação e carrinho de compras.

**O que são cookies?**

Cookies são pequenos arquivos de texto armazenados no navegador do usuário pelo servidor. Servem para persistir informações entre requisições, como preferências do usuário, tokens de autenticação e identificadores de sessão. Cada cookie possui atributos como nome, valor, data de expiração, domínio e flags de segurança (HttpOnly, Secure). Ao contrário das sessões que ficam no servidor, os cookies ficam no cliente, por isso dados sensíveis não devem ser armazenados diretamente neles sem criptografia.

**O que já consegui implementar em Laravel até agora?**

Implementei um sistema básico de cadastro e autenticação de usuários utilizando Laravel com o pacote Breeze. O sistema inclui: tela de registro com validação de dados (nome, e-mail e senha), tela de login com autenticação via sessão, proteção de rotas com middleware `auth` para que apenas usuários autenticados acessem determinadas páginas, e logout que encerra a sessão. O projeto utiliza Blade como template engine, migrations para criação da tabela de usuários e as convenções MVC do Laravel.

---

## Atividade 2 — SOLID e sua relação com o Laravel

**S — Single Responsibility Principle**

Cada classe deve ter apenas uma responsabilidade. No Laravel: Controllers recebem requisições e delegam; a lógica de negócio vai em Services; Models cuidam apenas do acesso a dados.

**O — Open/Closed Principle**

Classes devem ser abertas para extensão, fechadas para modificação. No Laravel: Middlewares permitem adicionar comportamentos (autenticação, throttle) sem modificar o núcleo. Policies estendem autorização sem alterar controllers.

**L — Liskov Substitution Principle**

Subclasses devem poder substituir suas classes pai sem quebrar o comportamento. No Laravel: implementações de interfaces (Cache, Filesystem) podem ser trocadas (Redis, S3) sem alterar o código que as consome.

**I — Interface Segregation Principle**

Interfaces específicas são melhores que uma interface geral. No Laravel: o framework define interfaces granulares como `Authenticatable`, `Authorizable` e `CanResetPassword`. A classe User implementa apenas o que precisa.

**D — Dependency Inversion Principle**

Módulos de alto nível não devem depender de módulos de baixo nível; ambos devem depender de abstrações. No Laravel: o Service Container injeta dependências automaticamente via type-hinting. Controllers dependem de interfaces, não de implementações concretas.

---

## Atividade 3 — MVC no Laravel e Comparação com Outros Padrões

**MVC no Laravel**

O Laravel segue o padrão MVC nativamente:

- **Model** — representa os dados e a lógica de acesso ao banco via Eloquent ORM
- **View** — templates Blade que renderizam o HTML; não possuem lógica de negócio
- **Controller** — recebe requisições HTTP, chama os Models e retorna a View ou JSON

**Separação de camadas: Controller, Service e Model**

Em projetos maiores, é comum adicionar a camada Service:

- **Controller** — recebe a requisição e valida dados de entrada
- **Service** — contém a lógica de negócio (regras, cálculos, orquestrações)
- **Model** — responsável exclusivamente pelo acesso e persistência de dados

Essa separação evita controllers inchados e torna o código mais testável e reutilizável.

**Comparação entre padrões**

| Padrão | Onde a lógica fica | Característica principal |
|---|---|---|
| MVC | Controller | View pode consultar o Model diretamente |
| MVP | Presenter | View é passiva; Presenter controla tudo |
| Arquitetura em Camadas | Camada de negócio | Comunicação estrita entre camadas adjacentes |

- **MVC** — Controller medeia Model e View. Usado em Laravel, Rails, Django.
- **MVP** — View é passiva e só exibe dados. O Presenter contém toda a lógica de apresentação. Mais comum em desktop e Android.
- **Arquitetura em Camadas** — divide a aplicação em camadas horizontais (Apresentação, Negócio, Dados, Infraestrutura). Mais rígida, comum em sistemas enterprise.

O Laravel implementa MVC de forma flexível e pode ser estendido com Services e Repositories para se aproximar de uma arquitetura em camadas.
