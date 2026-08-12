# GL Financeiro — Guia de Deploy

App de mentoria financeira (Diagnóstico, Orçamento Mensal, Mapa de Dívidas, Evolução), no mesmo padrão do LC Gestão: página única em HTML/JS, dados salvos no Firebase Realtime Database, deploy automático via Vercel a cada push no GitHub.

## Arquivos deste pacote

- `index.html` — aplicativo completo (login, cadastro e as 4 seções da mentoria)
- `database.rules.json` — regras de segurança do Firebase Realtime Database
- `LEIA-ME_Deploy.md` — este guia

## Passo 1 — Criar o projeto no Firebase

1. Acesse [console.firebase.google.com](https://console.firebase.google.com) e clique em **Adicionar projeto**.
2. Dê um nome (ex: `gl-financeiro`) e conclua a criação.

## Passo 2 — Ativar Authentication

1. No menu lateral, vá em **Build > Authentication**.
2. Clique em **Get started**.
3. Na aba **Sign-in method**, ative o provedor **E-mail/senha**.

## Passo 3 — Ativar o Realtime Database

1. No menu lateral, vá em **Build > Realtime Database**.
2. Clique em **Criar banco de dados**.
3. Escolha a localização (ex: `us-central1`) e inicie em **modo bloqueado** (as regras corretas serão aplicadas no Passo 5).

## Passo 4 — Obter o firebaseConfig e colar no index.html

1. No Firebase Console, clique no ícone de engrenagem > **Configurações do projeto**.
2. Em **Seus apps**, clique no ícone **</>** (Web) para registrar um app.
3. Dê um apelido (ex: `gl-financeiro-web`) e clique em **Registrar app**.
4. Copie o objeto `firebaseConfig` exibido.
5. Abra o arquivo `index.html` deste pacote, localize o bloco:

```javascript
const firebaseConfig = {
  apiKey: "SUA_API_KEY",
  authDomain: "SEU_PROJETO.firebaseapp.com",
  databaseURL: "https://SEU_PROJETO-default-rtdb.firebaseio.com",
  projectId: "SEU_PROJETO",
  storageBucket: "SEU_PROJETO.appspot.com",
  messagingSenderId: "SEU_SENDER_ID",
  appId: "SEU_APP_ID"
};
```

6. Substitua pelos valores reais copiados do Firebase e salve o arquivo.

> **Atenção:** confirme que o valor de `databaseURL` corresponde exatamente à URL do seu Realtime Database (visível na aba **Dados** do Realtime Database).

## Passo 5 — Aplicar as regras de segurança

1. No Realtime Database, vá na aba **Regras**.
2. Apague o conteúdo atual e cole o conteúdo do arquivo `database.rules.json` deste pacote.
3. Clique em **Publicar**.

Essas regras garantem que cada usuário só possa ler e escrever os próprios dados, em `users/{uid}/...`.

## Passo 6 — Enviar para o GitHub

```bash
git init
git add .
git commit -m "Deploy inicial do GL Financeiro"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/gl-financeiro.git
git push -u origin main
```

Se o repositório já existir, basta:

```bash
git add .
git commit -m "Atualização do GL Financeiro"
git push
```

## Passo 7 — Conectar à Vercel

1. Acesse [vercel.com](https://vercel.com) e faça login com sua conta GitHub.
2. Clique em **Add New > Project**.
3. Selecione o repositório `gl-financeiro`.
4. Como é um site estático (HTML puro), não é necessário configurar build command — clique em **Deploy**.
5. A cada novo `git push` na branch `main`, a Vercel fará o redeploy automaticamente.

## Passo 8 — Autorizar o domínio da Vercel no Firebase

1. No Firebase Console, vá em **Authentication > Settings > Authorized domains**.
2. Clique em **Add domain** e adicione a URL gerada pela Vercel (ex: `gl-financeiro.vercel.app`).

Sem esse passo, o login por e-mail/senha será bloqueado no domínio de produção.

## Estrutura de dados no Realtime Database

```
users/
  {uid}/
    perfil/          -> nome, email, criadoEm
    diagnostico/      -> renda, fixas, variaveis, reserva
    orcamento/
      {itemId}/        -> desc, categoria, tipo, valor
    dividas/
      {itemId}/        -> credor, valor, juros, parcela, vencimento
    evolucao/
      {AAAA-MM}/        -> mes, saldo, divida, reserva
```

## Testando localmente antes do deploy

Basta abrir o `index.html` diretamente no navegador, ou rodar um servidor local simples:

```bash
npx serve .
```

## Próximos passos sugeridos

- Criar uma conta de teste para validar o fluxo completo (cadastro, diagnóstico, orçamento, dívidas, evolução).
- Se for usar com múltiplos clientes de mentoria, considere adicionar um campo de "cliente/mentorado" vinculado ao `uid` para você acompanhar vários perfis a partir de uma conta administrativa (posso ajudar a implementar isso quando quiser evoluir o projeto).
