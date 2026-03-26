<a name="readme-top"></a>

# TEF IP Docs

[![Documentação](https://img.shields.io/badge/docs-tefip.github.io%2Ftefip__docs-blue)](https://tefip.github.io/tefip_docs/)
[![MkDocs Material](https://img.shields.io/badge/MkDocs-Material-526CFE?logo=materialformkdocs)](https://squidfunk.github.io/mkdocs-material/)

Site de documentação pública da API do [TEF IP](https://github.com/TefIP) — um app Flutter que roda um servidor HTTP embarcado para integrar terminais de pagamento (Stone, Getnet, Rede) a sistemas de PDV/POS.

---

## Conteúdo da documentação

| Página | Descrição |
|--------|-----------|
| [Início](https://tefip.github.io/tefip_docs/) | Visão geral do TEF IP e como ele funciona |
| [Primeiros passos](https://tefip.github.io/tefip_docs/getting-started/) | Instalação, configuração e primeira requisição |
| [API — Perguntas](https://tefip.github.io/tefip_docs/api/ask/) | Exibir perguntas interativas e coletar respostas no terminal |
| [API — Display](https://tefip.github.io/tefip_docs/api/display/) | Exibir imagens, texto e carrossel na tela do terminal |
| [API — Venda](https://tefip.github.io/tefip_docs/api/sale/) | Gerenciar carrinho de venda (itens, pagamentos, finalização) |
| [API — Impressão](https://tefip.github.io/tefip_docs/api/print/) | Imprimir imagens, comprovantes formatados e cupons fiscais |
| [API — Status](https://tefip.github.io/tefip_docs/api/status/) | Consultar status, informações do app e reiniciar o servidor |
| [API — Transações](https://tefip.github.io/tefip_docs/api/transaction/) | Processar pagamentos, consultar histórico e realizar estornos |
| [API — Swagger](https://tefip.github.io/tefip_docs/api/swagger/) | Documentação OpenAPI interativa disponível no próprio terminal |

Todos os exemplos de integração estão disponíveis em **cURL, Dart, JavaScript, PHP e Ruby**.

---

## Pré-requisitos

- Python 3.9+
- pip

---

## Instalação

```bash
pip install mkdocs-material
```

---

## Desenvolvimento local

```bash
mkdocs serve
# → http://127.0.0.1:8000
```

---

## Build

```bash
mkdocs build
# gera o site estático em site/
```

---

## Estrutura

```
mkdocs.yml                  configuração do MkDocs Material
docs/
  index.md                  página inicial
  getting-started.md        instalação e primeira requisição
  emulator.md               emulador local para testes sem hardware
  assets/                   imagens, GIFs e diagramas
  api/
    ask.md                  POST /ask · /ask/form · /ask/cancel
    display.md              POST /display/image · text · carousel · clear · pop
    sale.md                 POST/PATCH /sale · item · payment · finalize · cancel
    print.md                POST /print/image · text · xml
    status.md               GET /status · /info · POST /restart
    transaction.md          POST/GET /transaction · reversal
    swagger.md              GET /docs · /openapi.bundle.yaml
```

---

## Como contribuir

1. Clone o repositório e instale as dependências (`pip install mkdocs-material`).
2. Rode `mkdocs serve` para visualizar as mudanças em tempo real.
3. Edite ou crie arquivos em `docs/`. Veja `CLAUDE.md` para convenções de estilo e o template de tabs de integração.
4. Rode `mkdocs build` para verificar se o build passa sem erros antes de abrir um PR.

---

## Contato

* GitHub: [TEF IP](https://github.com/TefIP)
* Site: [https://www.djsystem.com.br](https://www.djsystem.com.br)

---

## Mantido por

<p align="center">
  <a href="https://www.djsystem.com.br/">
    <img src="https://avatars.githubusercontent.com/u/85188542?s=200&v=4" />
    <p align="center">Desenvolvido e mantido por DJSYSTEM.</p>
  </a>
</p>

<p align="right">(<a href="#readme-top">Voltar ao topo</a>)</p>
