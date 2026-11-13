# 📚 Projeto Biblioteca

Sistema web didático de biblioteca: cadastro de livros e usuários, empréstimo,
devolução e histórico — HTML + CSS + JavaScript puro no front-end, **Supabase**
(PostgreSQL + API) como back-end e **Vercel** para publicação.

Projeto da disciplina, construído seguindo o tutorial
`Tutorial-Supabase-Vercel.pdf`.

---

## Estrutura

```
proj-biblioteca/
├── index.html   # estrutura e formulários
├── style.css    # estilização
└── app.js       # lógica + conexão com o Supabase
```

Não há build: o site é 100% estático.

## Rodando localmente

Como é estático, basta servir a pasta:

```bash
# opção 1: VS Code + extensão Live Server (recomendado no tutorial)
# opção 2: python
python3 -m http.server 5500
```

Depois abra <http://localhost:5500>.

> Abrir o `index.html` direto pelo `file://` costuma funcionar, mas servir por
> HTTP evita bloqueios de CORS.

## Back-end (Supabase)

O `app.js` tem duas constantes no topo do arquivo:

```js
const SUPABASE_URL = "https://SEU-PROJETO.supabase.co";
const SUPABASE_ANON_KEY = "sb_publishable_...";
```

Elas vêm de **Project Settings → Data API** no painel do Supabase.

Para recriar o banco do zero:

1. Crie um projeto em <https://supabase.com/> (plano gratuito).
2. Abra **SQL Editor → New query** e execute o script da **Etapa 4** do
   tutorial (tabelas `usuarios`, `livros`, `emprestimos`).
3. Execute os `INSERT` de exemplo da **Etapa 5**.
4. Execute as policies de RLS da **Etapa 6**.
5. Copie a URL e a *publishable key* para o `app.js`.

### Tabelas

```
usuarios (id, nome, email, criado_em)
livros   (id, titulo, autor, ano, disponivel, criado_em)
emprestimos (id, livro_id, usuario_id, data_emprestimo, data_devolucao, devolvido)
```

Relações: um usuário tem vários empréstimos (1:N) e um livro tem vários
empréstimos (N:1).

### Sobre a segurança

O tutorial habilita RLS com policies permissivas (`USING (true)`), o que deixa
**qualquer pessoa com a URL e a chave pública ler, inserir e apagar registros**.
Isso é aceitável para um protótipo de sala de aula, mas **não** para produção:
lá seria necessário Supabase Auth e políticas por usuário.

A chave `publishable`/`anon` pode ficar no front-end. **Nunca** use a
`service_role` no navegador.

## Deploy na Vercel

1. Suba o código no GitHub.
2. Em <https://vercel.com/>, **Add New → Project** e importe o repositório.
3. Configuração: *Framework Preset* = **Other**, sem build command e sem
   output directory (a raiz já contém o `index.html`).
4. **Deploy**.

A partir daí todo push na branch `main` gera um deploy automático.

## Problemas comuns

| Sintoma | Causa provável |
|---|---|
| "Erro ao carregar livros/usuários/empréstimos" | URL ou chave do Supabase erradas, ou projeto do Supabase pausado/excluído |
| Tudo funciona local, mas não na Vercel | esqueceu de commitar a alteração do `app.js` |
| `permission denied for table ...` | RLS ativo sem policies criadas |

Se o projeto do Supabase for recriado, **a URL e a chave mudam** e o `app.js`
precisa ser atualizado — esse é o ponto mais frágil do projeto.
