# Plugins Vértice

Marketplace dos plugins da Vértice, mantido pela [Laguna AI](https://lagunaai.org). Este repositório serve os mesmos plugins para o **ChatGPT** (Business/Enterprise, Codex) e para o **Claude** (Claude Code).

| Plugin | O que faz | Servidor MCP |
| --- | --- | --- |
| `vertice-crm` | CRM da Vértice: contas, contatos, oportunidades, atividades, reuniões, documentos e tarefas. Escritas afetam só a sua carteira e pedem sua aprovação. | `https://lagunaai.org/api/mcp/crm` |
| `vertice-pulso` | Dados de uso do produto (contas, usuários, convites, eventos, integrações, logs e limites contratados) em SQL somente leitura. | `https://lagunaai.org/api/mcp/produto` |
| `vertice-drive` | Drive da Vértice: arquivos da empresa (somente leitura) e a sua pasta Meus arquivos, onde o assistente salva documentos (DOCX no modelo Vértice), planilhas e textos. | `https://lagunaai.org/api/mcp/drive` |

> **Credenciais:** os plugins não guardam token, senha nem chave. O acesso é feito por OAuth: na primeira vez que o plugin é usado, você entra com a sua conta Laguna e o ChatGPT/Claude guarda a autorização do seu lado. Nada de credencial deve ser commitado neste repositório (ele é público).

## Instalar no ChatGPT (admin de workspace Business/Enterprise)

Recomendado: os plugins se atualizam sozinhos a cada nova versão publicada aqui.

1. Em **Workspace settings > Plugins**, clique em **Add > Import marketplace**.
2. Cole a URL do repositório: `https://github.com/Laguna-ia/vertice-plugins`. Deixe o campo de branch vazio (usa `main`).
3. Os plugins **Vértice CRM**, **Vértice Pulso** e **Vértice Drive** aparecem no diretório de plugins do workspace. O ChatGPT sincroniza o marketplace uma vez por dia; para puxar uma versão nova na hora, use **Sync now**.
4. Se o workspace já tinha os plugins instalados por zip, remova essas instalações antigas para não ficar com dois "Vértice CRM" / "Vértice Pulso".
5. Cada pessoa, ao usar o plugin pela primeira vez, faz login com a conta Laguna.

No Codex CLI: `codex plugin marketplace add Laguna-ia/vertice-plugins`.

## Instalar no Claude (Claude Code)

```text
/plugin marketplace add Laguna-ia/vertice-plugins
/plugin install vertice-crm@laguna
/plugin install vertice-pulso@laguna
/plugin install vertice-drive@laguna
```

Pelo terminal: `claude plugin marketplace add Laguna-ia/vertice-plugins` e `claude plugin install vertice-crm@laguna`. Para receber versões novas: `/plugin marketplace update laguna`. No primeiro uso, autorize o servidor MCP (login Laguna) em `/mcp`.

## Instalar por zip

Para quem não é admin do workspace ou usa ChatGPT Plus/Pro: baixe `vertice-crm.zip`, `vertice-pulso.zip` e `vertice-drive.zip` na página de [Releases](https://github.com/Laguna-ia/vertice-plugins/releases/latest) ou em [lagunaai.org/plataforma/empresa/plugins](https://lagunaai.org/plataforma/empresa/plugins) e siga as instruções de lá. Instalações por zip não se atualizam sozinhas.

## Estrutura

```text
.agents/plugins/marketplace.json     marketplace do ChatGPT/Codex
.claude-plugin/marketplace.json      marketplace do Claude
plugins/<nome>/plugin.json           manifest Agent Plugins 1.0 (ChatGPT), com extensions.com.openai
plugins/<nome>/mcp.json              servidor MCP (streamable-http)
plugins/<nome>/skills/<nome>/SKILL.md
plugins/<nome>/assets/               ícone e logo
plugins/<nome>/.claude-plugin/plugin.json   manifest do Claude (MCP inline, type "http")
scripts/build-zips.sh                gera dist/<nome>.zip (mesmo layout dos zips do site)
scripts/check-versions.sh            confere se as versões dos manifests batem
```

O nome de cada plugin é o mesmo na pasta, no `plugin.json`, no `.claude-plugin/plugin.json` e nos dois marketplaces.

## Lançar uma versão

1. Faça as mudanças em `plugins/<nome>/` (skill, manifest, MCP). Se mudar a URL do MCP, mude em `mcp.json` **e** em `.claude-plugin/plugin.json`.
2. Suba a versão (semver) em **todos** os manifests: `plugins/*/plugin.json`, `plugins/*/.claude-plugin/plugin.json` e `.claude-plugin/marketplace.json`.
3. Rode `./scripts/check-versions.sh` e `./scripts/build-zips.sh`; se tiver o Claude Code, `claude plugin validate .`.
4. Commit em `main`, depois crie e envie a tag:

   ```bash
   git tag v1.2.0
   git push origin main v1.2.0
   ```

5. O workflow `release` confere as versões (inclusive contra a tag), gera os zips e publica o GitHub Release com os zips de `dist/`. O ChatGPT pega a versão nova no sync diário (ou em **Sync now**). Se o app ainda servir os zips em `lagunaai.org`, atualize-os lá também.
