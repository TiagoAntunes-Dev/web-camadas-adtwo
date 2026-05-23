# ADO 2 · Hospedagem da API REST Node.js

<p align="center">
  <img src="https://img.shields.io/badge/Node.js-43853D?style=for-the-badge&logo=node.js&logoColor=white" />
  <img src="https://img.shields.io/badge/Express.js-404D59?style=for-the-badge&logo=express&logoColor=white" />
  <img src="https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white" />
  <img src="https://img.shields.io/badge/Render-46E3B7?style=for-the-badge&logo=render&logoColor=white" />
</p>

**Disciplina:** Aplicações Web em Camadas  
**Projeto:** Product CRUD API — deploy em produção  
**Aluno:** Tiago Antunes Paz de Oliveira  
**URL pública:** https://crud-api-products.onrender.com  
**Repositório da API:** https://github.com/TiagoAntunes-Dev/crud-api-products

---

## Índice
1. [Como a API rodava localmente](#1-como-a-api-rodava-localmente)
2. [Opções de hospedagem pesquisadas](#2-opções-de-hospedagem-pesquisadas)
3. [Deploy no Render — passo a passo](#3-deploy-no-render--passo-a-passo)
4. [Como a API se conecta ao banco hospedado](#4-como-a-api-se-conecta-ao-banco-hospedado)

---

## 1. Como a API rodava localmente

### Iniciando o servidor

Durante o desenvolvimento eu usava o `nodemon` pelo script `dev` do `package.json`:

```bash
npm run dev
# executa: nodemon index.js
```

O `nodemon` monitora os arquivos e reinicia o servidor automaticamente a cada salvamento — útil no desenvolvimento, mas desnecessário em produção onde o código não muda. Em produção o comando correto é:

```bash
npm start
# executa: node index.js
```

Por isso o `package.json` tem os dois scripts separados:

```json
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js"
}
```

### O que estava no `.env` local

O arquivo `.env` local continha estas variáveis (valores omitidos por segurança):

```env
PORT=4000
MONGO_URI=mongodb+srv://[usuario]:[senha]@[cluster].mongodb.net/[banco]
```

O `.env` nunca vai ao GitHub — está listado no `.gitignore`. Isso significa que ao hospedar a API, essas variáveis precisam ser configuradas diretamente na plataforma de hospedagem.

### Terminal com o servidor rodando localmente

> 📸 [INSIRA AQUI: print do terminal com npm run dev mostrando ✅ Connected to Database! e Server is running on port 4000]

---

## 2. Opções de Hospedagem Pesquisadas

### 🔹 Render

**O que oferece no plano gratuito:**  
O Render oferece 750 horas de execução por mês para Web Services no plano gratuito. O serviço entra em modo sleep após 15 minutos sem receber nenhuma requisição — quando isso acontece, a próxima chamada pode demorar até 50 segundos para responder enquanto o servidor "acorda". Não cobra pelo tempo dormindo, apenas pelo tempo ativo.

**Suporta Node.js com Express?**  
Sim. O Render detecta automaticamente projetos Node.js pelo `package.json`, roda `npm install` para instalar dependências e depois usa o script `start` para iniciar o servidor.

**Por que escolhi:**  
Escolhi o Render pela facilidade de integração com o GitHub e pela clareza do processo. Em poucos minutos o repositório já estava conectado, as variáveis de ambiente configuradas e o deploy rodando. Os logs em tempo real durante o deploy também ajudam muito a entender o que está acontecendo em cada etapa.

---

### 🔹 Railway

**O que oferece no plano gratuito:**  
O Railway oferece $5 de crédito por mês no plano gratuito. Diferente do Render, o serviço não dorme por inatividade — fica sempre acordado enquanto houver crédito. O problema é que quando os $5 acabam, o serviço é suspenso automaticamente até o próximo mês.

**Suporta Node.js com Express?**  
Sim. O Railway tem suporte nativo a Node.js e detecta o tipo do projeto automaticamente pelo `package.json`.

**Por que descartei:**  
O modelo de créditos mensais gera incerteza para projetos de estudos. Se o projeto receber mais acesso que o esperado, o serviço para no meio do mês sem aviso. Preferi uma opção com limite previsível por horas ao invés de por crédito financeiro.

---

### 🔹 Fly.io

**O que oferece no plano gratuito:**  
O Fly.io oferece 3 máquinas virtuais pequenas (shared-cpu-1x, 256MB RAM) e não tem sleep por inatividade. É uma plataforma mais próxima de um servidor real — você tem controle total sobre a máquina virtual.

**Suporta Node.js com Express?**  
Sim, mas exige mais configuração. O deploy é feito via linha de comando com a CLI `flyctl`, e geralmente é necessário criar um `Dockerfile` ou deixar a CLI detectar o tipo de projeto.

**Por que descartei:**  
A complexidade de configuração é maior que o necessário para este projeto. O Fly.io exige familiaridade com conceitos de containers e infraestrutura que ainda não tenho. Ficou como opção para quando eu precisar de mais controle sobre o ambiente de produção.

---

## 3. Deploy no Render — Passo a Passo

### 3.1 Preparação do repositório

Antes do deploy, verifiquei que o `package.json` tinha o script `start` correto — é ele que o Render usa para iniciar o servidor em produção:

```json
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js"
}
```

O código estava em um repositório público no GitHub: `TiagoAntunes-Dev/crud-api-products`.

> 📸 [INSIRA AQUI: print do repositório no GitHub mostrando os arquivos da API]

### 3.2 Criando o Web Service no Render

1. Acessei [render.com](https://render.com) e fiz login com minha conta do GitHub
2. Cliquei em **New + → Web Service**
3. Conectei o repositório `crud-api-products`
4. Preenchi as configurações do serviço:

| Campo | Valor |
|---|---|
| Name | `crud-api-products` |
| Language | Node |
| Branch | `develop` |
| Build Command | `npm install` |
| Start Command | `node index.js` |
| Instance Type | Free |

> 📸 [INSIRA AQUI: print do formulário de configuração preenchido no Render]

### 3.3 Configurando as variáveis de ambiente

Na seção **Environment Variables** do formulário, adicionei as mesmas variáveis que estavam no `.env` local:

| Chave | O que é |
|---|---|
| `MONGO_URI` | A connection string do MongoDB Atlas (configurado no ADO 1) |
| `PORT` | 4000 |

> 📸 [INSIRA AQUI: print da seção Environment Variables com as chaves visíveis]

### 3.4 Deploy concluído

Após clicar em **Create Web Service**, o Render executou automaticamente:

```
==> Running 'npm install'
==> Build successful 🎉
==> Running 'node index.js'
✅ Connected to Database!
Server is running on port 4000
==> Your service is live 🎉
==> Available at your primary URL https://crud-api-products.onrender.com
```

<p align="center">
  <img src="INSIRA-PRINT-LOGS-DEPLOY" alt="Logs do deploy no Render" />
</p>

O painel do Render mostrou o serviço com status **Live**:

<p align="center">
  <img src="INSIRA-PRINT-PAINEL-LIVE" alt="Painel do Render com status Live" />
</p>

### 3.5 Requisição para a URL pública

Com o deploy concluído, acessei a URL pública no navegador:

```
https://crud-api-products.onrender.com/api/products
```

A API retornou os produtos cadastrados no MongoDB Atlas — os mesmos Pancake, Donut e Cannoli do banco remoto:

<p align="center">
  <img src="INSIRA-PRINT-RESPOSTA-URL-PUBLICA" alt="Resposta da API na URL pública" />
</p>

---

## 4. Como a API se Conecta ao Banco Hospedado

### Como o `MONGO_URI` na plataforma aponta para o Atlas

A variável `MONGO_URI` configurada no Render é exatamente a mesma connection string do MongoDB Atlas que estava no `.env` local. O trecho de conexão no `index.js` é:

```javascript
mongoose.connect(process.env.MONGO_URI)
    .then(() => {
        console.log('✅ Connected to Database!');
        app.listen(PORT, () => {
            console.log(`Server is running on port ${PORT}`);
        });
    })
```

Esse código não sabe se está rodando na minha máquina ou num servidor na Virgínia — ele só lê `process.env.MONGO_URI`. O valor dessa variável aponta para o cluster no MongoDB Atlas, então a API hospedada no Render conecta no mesmo banco da ADO 1.

### Por que as variáveis na plataforma substituem o `.env` local

O arquivo `.env` nunca chegou ao servidor do Render — ele está no `.gitignore`, nunca foi para o GitHub e o Render nunca teve acesso a ele. As variáveis configuradas no painel do Render são injetadas diretamente no processo Node.js antes de ele iniciar, como se o Render "escrevesse" essas variáveis na memória do servidor antes de rodar `node index.js`.

A lógica é sempre a mesma: o código lê de `process.env`. Quem preenche esse `process.env` muda conforme o ambiente — localmente é o pacote `dotenv` lendo o `.env`, em produção é a plataforma injetando diretamente.

### O que aconteceria se o `MONGO_URI` estivesse errado

Se o `MONGO_URI` tivesse qualquer erro — senha errada, nome do cluster incorreto, IP bloqueado no Atlas — o servidor iniciaria mas a conexão com o banco falharia. O bloco `.catch` do `index.js` capturaria o erro:

```javascript
.catch((error) => {
    console.error('Database connection error:', error);
});
```

O servidor não subiria, o Render marcaria o deploy como falho e os logs mostrariam o erro. A API não responderia em nenhuma rota — qualquer requisição retornaria erro 502 (Bad Gateway) porque o processo Node.js não estaria rodando.

### Erros encontrados e como resolvi

**Erro: "spin down with inactivity"**  
Logo após o deploy, o Render exibiu um aviso informando que o plano gratuito hiberna após inatividade, podendo causar delay de 50 segundos ou mais na primeira requisição depois de um período sem uso. Isso não é um erro que precisei resolver — é uma limitação do plano gratuito que documentei para entender o comportamento da API em produção. A solução seria fazer um upgrade para um plano pago, o que não é necessário para um projeto de estudos.

**Observação sobre a branch:**  
O repositório estava na branch `develop` ao invés de `main`. O Render conectou normalmente à branch `develop` — qualquer push nessa branch dispara um novo deploy automaticamente.

---

## Estrutura do Projeto

```
crud-api-products/
├── controllers/
│   └── product.controller.js   # Lógica das operações CRUD
├── models/
│   └── product.model.js        # Schema Mongoose
├── routes/
│   └── product.routes.js       # Definição das rotas HTTP
├── .env                        ← NÃO vai ao GitHub
├── .gitignore                  ← inclui .env e node_modules
├── index.js                    ← ponto de entrada; o Render roda este arquivo
└── package.json                ← contém o script "start" que o Render usa
```

