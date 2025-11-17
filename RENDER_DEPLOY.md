# 🚀 Guia de Deploy no Render

Este guia explica como fazer o deploy do seu projeto Strapi no Render com PostgreSQL.

## 📋 Pré-requisitos

1. Conta no [Render](https://render.com)
2. Repositório Git (GitHub, GitLab ou Bitbucket) com seu código

## 🔧 Configuração do Projeto

O projeto já está configurado com:
- ✅ Driver PostgreSQL (`pg`) instalado
- ✅ Configuração de banco ajustada para SSL (necessário no Render)
- ✅ Arquivo `render.yaml` para facilitar o deploy

## 📝 Variáveis de Ambiente Necessárias

O Strapi precisa das seguintes variáveis de ambiente. **Gere valores únicos e seguros** para cada uma:

```bash
# APP_KEYS (4 chaves separadas por vírgula)
APP_KEYS=chave1,chave2,chave3,chave4

# Secrets do Strapi
ADMIN_JWT_SECRET=sua_chave_secreta_aqui
API_TOKEN_SALT=seu_salt_aqui
TRANSFER_TOKEN_SALT=seu_transfer_salt_aqui
ENCRYPTION_KEY=sua_chave_criptografia_aqui
```

### Gerando valores aleatórios:

Você pode usar o comando abaixo para gerar valores aleatórios:

```bash
# Gerar uma chave aleatória (repita para cada variável)
openssl rand -base64 32
```

Ou use um gerador online de strings aleatórias.

## 🚀 Passos para Deploy

### Opção 1: Usando render.yaml (Recomendado)

1. **Faça commit e push do código:**
   ```bash
   git add .
   git commit -m "Configurar deploy no Render"
   git push
   ```

2. **Acesse o Dashboard do Render:**
   - Acesse [https://dashboard.render.com](https://dashboard.render.com)

3. **Conecte seu repositório:**
   - Clique em "New +"
   - Selecione "Blueprint"
   - Conecte seu repositório Git
   - O Render detectará automaticamente o arquivo `render.yaml`

4. **Revise e ajuste a configuração:**
   - O arquivo `render.yaml` já está configurado
   - Você pode ajustar o plano (free, starter, etc.) conforme necessário
   - Ajuste a região se necessário

5. **Configure as variáveis de ambiente:**
   - O Render vai gerar automaticamente algumas variáveis
   - **IMPORTANTE**: Adicione manualmente as variáveis do Strapi:
     - `APP_KEYS` (4 chaves separadas por vírgula)
     - `ADMIN_JWT_SECRET`
     - `API_TOKEN_SALT`
     - `TRANSFER_TOKEN_SALT`
     - `ENCRYPTION_KEY`
   - Defina `DATABASE_CLIENT=postgres`
   - O `DATABASE_URL` será fornecido automaticamente pelo Render

6. **Deploy:**
   - Clique em "Apply"
   - O Render criará o banco PostgreSQL e o serviço web automaticamente

### Opção 2: Deploy Manual

1. **Criar o banco PostgreSQL:**
   - No dashboard do Render, clique em "New +"
   - Selecione "PostgreSQL"
   - Configure:
     - Nome: `personal-blog-db`
     - Plano: Escolha conforme necessário
     - Clique em "Create Database"
   - Copie a "Internal Database URL" ou "External Database URL"

2. **Criar o serviço web:**
   - Clique em "New +"
   - Selecione "Web Service"
   - Conecte seu repositório Git
   - Configure:
     - **Name**: `personal-blog-strapi`
     - **Environment**: `Node`
     - **Build Command**: `npm install && npm run build`
     - **Start Command**: `npm start`
     - **Plan**: Escolha conforme necessário

3. **Adicionar variáveis de ambiente:**
   No serviço web, adicione estas variáveis:
   ```
   DATABASE_CLIENT=postgres
   DATABASE_URL=<URL do banco criado no passo 1>
   APP_KEYS=chave1,chave2,chave3,chave4
   ADMIN_JWT_SECRET=<sua_chave>
   API_TOKEN_SALT=<seu_salt>
   TRANSFER_TOKEN_SALT=<seu_transfer_salt>
   ENCRYPTION_KEY=<sua_chave_criptografia>
   NODE_ENV=production
   HOST=0.0.0.0
   ```

4. **Conectar o banco ao serviço:**
   - Na aba "Connections" do banco PostgreSQL
   - Selecione o serviço web criado
   - O Render injetará automaticamente a `DATABASE_URL`

5. **Deploy:**
   - Clique em "Create Web Service"
   - Aguarde o deploy completar

## ⚙️ Configurações Importantes

### SSL do PostgreSQL

O projeto está configurado para usar SSL automaticamente quando `DATABASE_URL` estiver presente (o que o Render faz automaticamente).

### Health Check

O Render configurado no `render.yaml` usa `/_health` como health check. O Strapi tem essa rota por padrão.

### Build e Start

- **Build Command**: `npm install && npm run build`
  - Instala dependências e compila o admin panel do Strapi
- **Start Command**: `npm start`
  - Inicia o Strapi em modo produção

## 🔍 Verificando o Deploy

Após o deploy:

1. **Acesse a URL do serviço:**
   - O Render fornecerá uma URL como: `https://personal-blog-strapi.onrender.com`

2. **Acesse o painel admin:**
   - `https://sua-url.onrender.com/admin`
   - Crie sua conta de administrador na primeira vez

3. **Verifique os logs:**
   - No dashboard do Render, clique no serviço
   - Acesse a aba "Logs" para verificar erros

## 🐛 Troubleshooting

### Erro de conexão com banco
- Verifique se `DATABASE_URL` está configurada corretamente
- Verifique se o banco está acessível pelo serviço web
- Confirme que `DATABASE_CLIENT=postgres` está definido

### Erro de variáveis de ambiente
- Certifique-se de que todas as variáveis obrigatórias estão configuradas
- Verifique se os valores são válidos (especialmente `APP_KEYS` deve ter 4 chaves)

### Build falhando
- Verifique os logs de build no Render
- Confirme que todas as dependências estão no `package.json`
- Verifique a versão do Node.js (deve ser entre 18 e 22)

### SSL Error
- O projeto já está configurado para usar SSL no PostgreSQL
- Se ainda tiver problemas, verifique a configuração em `config/database.ts`

## 📚 Recursos Úteis

- [Documentação do Render](https://render.com/docs)
- [Documentação do Strapi](https://docs.strapi.io)
- [Deploy Strapi no Render](https://docs.strapi.io/dev-docs/deployment/hosting-guides/render)

## 🔐 Segurança

⚠️ **IMPORTANTE**: Nunca commite o arquivo `.env` com valores reais. As variáveis de ambiente devem ser configuradas apenas no dashboard do Render.



