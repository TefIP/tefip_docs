# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**TEF IP** is a Flutter app that runs an embedded [shelf](https://pub.dev/packages/shelf)-based HTTP server. POS/PDV systems send HTTP requests to it; TEF IP routes them through payment hardware (Stone, Getnet, Rede) and returns results.

`tefip_docs` is TEF IP's public API documentation site, built with MkDocs Material.

## App Flow

```
POS/PDV System
  → dart_tefip SDK (ou curl / qualquer cliente HTTP)
  → TEF IP HTTP Server (Basic Auth obrigatório)
  → Middleware: auth → cors → json → busy → notification → error handler
  → Resources: ask | display | sale | print | status | transaction | swagger
  → Hardware: Stone, Getnet, Rede
  → Resposta ao POS/PDV
```

## API Endpoint Groups

| Grupo        | Endpoints                                                                                                                                      |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| `ask`        | POST /ask · POST /ask/form · POST /ask/cancel                                                                                                  |
| `display`    | POST /display/image · POST /display/text · POST /display/carousel · POST /display/clear                                                        |
| `sale`       | POST /sale · POST /sale/item · PATCH /sale/item/{id} · DELETE /sale/item/{id} · POST /sale/payment · DELETE /sale/payment/{id} · POST /sale/finalize · POST /sale/cancel |
| `print`      | POST /print/image · POST /print/text · POST /print/xml                                                                                         |
| `status`     | GET /status · GET /info · POST /restart                                                                                                        |
| `transaction`| POST /transaction · GET /transaction/{referenceId} · POST /transaction/{referenceId}/reversal                                                  |
| `swagger`    | GET /docs · GET /openapi.bundle.yaml                                                                                                           |

## Integration Tab Template

Every endpoint page must include an **Integration Examples** section using `pymdownx.tabbed`. All five tabs must always be present with **exactly** these names (case-sensitive — controls `content.tabs.link` sync across pages):

```markdown
=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:8080/endpoint \
         -d '{"key": "value"}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    final client = TefipClient(host: 'localhost', port: 8080, password: '1234');
    final result = await client.endpoint(/* params */);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:8080/endpoint', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ key: 'value' }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:8080/endpoint');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode(['key' => 'value']));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:8080/endpoint')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = { key: 'value' }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```
```

> **Indentação:** o conteúdo dentro de cada `=== "Tab"` deve usar **4 espaços**. Misturar tabs e espaços quebra a renderização.

## Authoring Rules

1. **Estrutura de página de endpoint:** intro curta em prosa → tabela/bloco JSON de request e response → seção `## Integration Examples` com o tab template acima.
2. **Autenticação:** adicione um admonition antes do primeiro endpoint de cada página:
   ```markdown
   !!! warning "Autenticação"
       Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação).
   ```
3. **Nomes de tab consistentes:** use exatamente `cURL`, `Dart`, `JavaScript`, `PHP`, `Ruby` em todo o site para que o `content.tabs.link` sincronize corretamente.
4. **Idioma:** toda a documentação é escrita em **português do Brasil**.

## MkDocs Material Notes

Extensões e features ativas (não adicione nem remova sem atualizar `mkdocs.yml`):

| Recurso | Sintaxe |
|---------|---------|
| `pymdownx.tabbed` (`alternate_style: true`) | `=== "Tab"` |
| `pymdownx.superfences` | blocos de código dentro de tabs; Mermaid suportado nativamente pelo tema Material |
| `pymdownx.highlight` | highlight de código com número de linhas |
| `admonition` + `pymdownx.details` | `!!! note`, `!!! warning`, `??? tip` |
| `tables` | tabelas GFM |
| `attr_list` | `{ .classe }` em elementos |
| `content.tabs.link` | sincronização de tabs entre páginas |
| `navigation.tabs` | navegação por abas no topo |

## Common Commands

```bash
# Instalar MkDocs Material (inclui MkDocs)
pip install mkdocs-material

# Servir localmente com live reload
mkdocs serve

# Build do site estático
mkdocs build

# Deploy para GitHub Pages
mkdocs gh-deploy
```

## Structure

```
mkdocs.yml
docs/
  index.md              homepage
  getting-started.md    instalação e primeira requisição
  assets/               imagens, GIFs, diagramas
  api/
    ask.md
    display.md
    sale.md
    print.md
    status.md
    transaction.md
    swagger.md
```

## Media Assets

When creating or editing documentation that would benefit from an image, GIF, screenshot, or diagram, **do not skip it silently**. Instead, insert a Markdown placeholder so the user knows exactly where to add the file:

```markdown
![TODO: adicionar <descrição do conteúdo>](docs/assets/<tipo>-<descricao>.<ext>)
```

Examples:
- `![TODO: adicionar GIF mostrando o fluxo de login](docs/assets/gif-fluxo-login.gif)`
- `![TODO: adicionar screenshot da tela inicial](docs/assets/screenshot-home.png)`
- `![TODO: adicionar diagrama de arquitetura](docs/assets/diagrama-arquitetura.png)`

Store all media under `docs/assets/`. After the placeholder, add a blockquote note explaining what the asset should show:

> **Ação necessária:** adicione o arquivo `docs/assets/<tipo>-<descricao>.<ext>` aqui.
