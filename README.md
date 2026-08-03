# Sistema de Admissão Online — Agás & Eugas

Formulário web + painel administrativo para colher fichas de admissão de novos funcionários da Agás Gases e Eugas.

## Como funciona

1. Ana Paula abre o **painel admin** (`admin.html`) e clica em **"＋ Nova ficha"**
2. Seleciona a empresa (Agás ou Eugas), opcionalmente informa nome e telefone do candidato
3. O sistema gera um **link único** — Ana Paula copia e envia pelo WhatsApp do candidato
4. Candidato abre o link no celular → preenche o formulário passo a passo (8 passos)
5. Cada passo é **salvo automaticamente**, então ele pode fechar e voltar depois
6. No último passo, o candidato **assina digitalmente** (com o dedo) a Declaração de Etnia e a ficha
7. Ao enviar, os dados ficam disponíveis no **painel admin** para Ana Paula visualizar

---

## Setup — 5 passos

### 1️⃣ Criar o projeto Firebase

1. Acesse [console.firebase.google.com](https://console.firebase.google.com)
2. Clique em **"Adicionar projeto"**
3. Nome sugerido: `admissao-agas-eugas` (ou reaproveite `checklist-clientes-f9167` se preferir centralizar)
4. Google Analytics: **pode desativar** (não precisa)
5. Depois de criado, clique no ícone **`</>`** (Web) para registrar um app web
6. Nome do app: `Admissão Online`
7. **Não marque** "Configurar Firebase Hosting"
8. Copie o objeto `firebaseConfig` que aparece na tela — vai ficar algo assim:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "admissao-agas-eugas.firebaseapp.com",
  projectId: "admissao-agas-eugas",
  storageBucket: "admissao-agas-eugas.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123..."
};
```

### 2️⃣ Ativar o Firestore

1. No menu lateral do Firebase, clique em **Build → Firestore Database**
2. Clique em **"Criar banco de dados"**
3. Escolha **"Iniciar no modo de produção"**
4. Localização: **`southamerica-east1`** (São Paulo — mesma que você usa nos outros projetos)
5. Aguarde criar

### 3️⃣ Configurar as regras de segurança do Firestore

1. Ainda no Firestore, vá na aba **"Regras"** (Rules)
2. Substitua o conteúdo por isso:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Fichas: qualquer um pode ler/escrever, mas só na coleção "fichas"
    // (segurança básica — proteção contra abuso vem pelo ID único, que é secreto como um link)
    match /fichas/{fichaId} {
      allow read, write: if true;
    }
    // Nega tudo o resto
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

3. Clique em **"Publicar"**

> **Nota:** Essas regras permitem qualquer pessoa que saiba o ID da ficha ler/escrever nela. Como o ID é gerado aleatoriamente e só quem tem o link consegue acessar, isso é seguro para o uso pretendido. Para maior segurança no futuro, podemos adicionar autenticação (você entra com senha para acessar o admin).

### 4️⃣ Colar a config nos dois arquivos

Abra `index.html` e `admin.html` e localize o bloco:

```javascript
const firebaseConfig = {
  // apiKey: "SUA_API_KEY",
  // …
};
```

**Descomente as linhas** e substitua pelos valores que você copiou no passo 1. Faça isso nos **dois arquivos** (index.html e admin.html — os valores são os mesmos).

### 5️⃣ Publicar no GitHub Pages

1. No GitHub, crie um novo repositório chamado `admissao-online` (ou nome que preferir) na sua conta `Aninhasalome`
2. Faça upload dos três arquivos: `index.html`, `admin.html`, `README.md`
3. Vá em **Settings → Pages**
4. Em "Source", selecione a branch `main` e a pasta `/ (root)`
5. Salve. Em alguns segundos o site fica disponível em:
   - Formulário: `https://aninhasalome.github.io/admissao-online/`
   - Painel: `https://aninhasalome.github.io/admissao-online/admin.html`

---

## Como usar no dia a dia

### Enviar ficha para um candidato:
1. Abra `admin.html`
2. Clique em **"＋ Nova ficha"**
3. Escolha Agás ou Eugas
4. Informe nome e telefone (opcional, mas ajuda a rastrear depois)
5. Clique em **"Gerar link"**
6. Copie o link OU clique em **"Abrir WhatsApp"** (envia direto se você preencheu o telefone)

### Acompanhar as fichas:
- Os quadrinhos no topo mostram quantas estão **pendentes** (link enviado mas não iniciado), **rascunho** (candidato começou a preencher) ou **enviada** (concluída)
- Clique em qualquer linha da tabela para ver os detalhes
- Use os filtros pra buscar por empresa, status ou nome

### Se algo der errado:
- Se um candidato reclamar que perdeu o link, você pode ir na ficha dele no painel e clicar em **"Copiar link"** — o mesmo link continua funcionando
- Se você precisar cancelar uma ficha, por enquanto ela fica no painel; posso adicionar botão de "arquivar" nas próximas fases

---

## Roadmap — próximas fases

Fase 1 (esta):
- ✅ Formulário mobile-first, wizard de 8 passos
- ✅ Assinatura digital (Declaração de Etnia + Assinatura Final)
- ✅ Painel admin com gestão de fichas
- ✅ Firebase Firestore + fallback pra localStorage

Fase 2 — Geração de PDF:
- Botão "Baixar PDF preenchido" no detalhe da ficha
- PDF sai no mesmo padrão da nossa ficha (Agás ou Eugas) já com todos os dados dentro
- Pronto pra imprimir, colher assinatura física e arquivar

Fase 3 — Notificações:
- E-mail automático pra Ana Paula quando uma ficha é enviada (via EmailJS)
- PDF anexado no e-mail

Fase 4 — Melhorias de UX:
- Auto-preenchimento de endereço por CEP (via ViaCEP)
- Máscaras nos campos de CPF/celular/CEP
- Instalação como PWA no celular
- Botão "Arquivar" no admin

---

## Suporte

Qualquer coisa que não funcionar, é só me chamar que ajusto.
