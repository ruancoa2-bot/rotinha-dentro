# Rotinha Dentro

Bot de rotas do WhatsApp com painel de clientes e administração, leitura de imagens,
preferências por bairro, validação de rotas, financeiro e backups criptografados.

O código do aplicativo fica em `bot-whatsapp-cliente-main/`.
O `Dockerfile` da raiz permite o deploy com o diretório raiz e o contexto Docker
padrões do Render. Também existe um Dockerfile próprio na pasta do aplicativo.

## Subir do zero no Render

[Criar serviço no Render](https://render.com/deploy?repo=https://github.com/ruancoa2-bot/rotinha-dentro)

O `render.yaml` da raiz prepara um **serviço pago de 8 CPUs / 32 GB**, disco de
**5 GB em `/data`**, Docker e deploy automático da branch `main`. Confira o preço
no Render antes de confirmar a criação. O dimensionamento inicial pode precisar
de ajuste conforme a quantidade de clientes e análises simultâneas.

No formulário, preencha somente as duas variáveis obrigatórias:

| Variável | O que preencher |
| --- | --- |
| `PANEL_USERS` | `seuemail@exemplo.com:SUA_SENHA:admin` |
| `PANEL_ADMIN_EMAILS` | `seuemail@exemplo.com` |

A senha não pode conter vírgula nem dois-pontos, pois eles separam os campos de
`PANEL_USERS`. O Render gera `PANEL_SESSION_SECRET` automaticamente nesse caminho.
Guarde esse valor para migrações futuras.

Quando aparecer **Live**, abra a URL do serviço e entre com o e-mail e a senha
configurados. A nova instalação começa vazia; os clientes, históricos e sessões
anteriores precisam ser restaurados por backup.

**[Passo a passo completo, instalação manual e recuperação de dados](bot-whatsapp-cliente-main/docs/NOVO_DEPLOY_RENDER.md)**

## Preparar a mudança de URL

1. No site atual, abra **Config → Arquivo portátil (iPhone / outro site)**, escolha uma senha de pelo menos 12 caracteres e clique em **Exportar para Arquivos**.
2. Guarde o arquivo `.botbackup` e a senha: eles serão usados no novo site.
3. Crie o serviço deste repositório no Render. Após aparecer **Live**, entre como administrador.
4. Na mesma seção de Config, selecione o arquivo, informe a senha, confira o resumo e importe. Confirme que clientes, bairros, financeiro e histórico foram recuperados.
5. Na troca definitiva, use um backup atualizado e pare o monitoramento no site antigo antes de ativar a mesma conta de WhatsApp no novo. O site atual pode continuar funcionando durante a preparação.

Publicar o repositório não copia o disco de produção nem troca a URL do site atual. O banco e as sessões entram pela restauração do backup. O R2 é opcional para essa migração por arquivo; se for usá-lo, configure as mesmas credenciais e a chave de criptografia na área **Environment** do Render.

## Rodar localmente

Use Node.js 24. Na pasta `bot-whatsapp-cliente-main`, execute:

```sh
npm ci
npm run build
npm start
```

O arquivo [`.env.example`](bot-whatsapp-cliente-main/.env.example) documenta as
variáveis. No Render, configure-as em **Environment**; localmente, exporte-as no
terminal ou use `node --env-file=.env dist/server.js` após criar seu `.env`.

Os dados de clientes e as credenciais de produção ficam no disco persistente e
nas variáveis do serviço. Eles não fazem parte da publicação do código.
