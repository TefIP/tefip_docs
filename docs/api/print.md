# Impressão

Endpoints para imprimir imagens, comprovantes formatados e cupons fiscais (XML/DANFE) na impressora do terminal.

!!! warning "Autenticação"
    Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação).

!!! info "Bloqueio durante impressão"
    Todos os endpoints de impressão definem `isBusy = true` enquanto a operação está em andamento. Novas requisições que verificam o estado ocupado serão rejeitadas com `503`.

---

## Impressão Fiscal (DANFE/XML)

O TEF IP inclui um parser avançado para documentos fiscais eletrônicos. Ao invés de o seu sistema formatar o comprovante manualmente, você pode enviar o XML original da nota e o terminal cuidará da renderização.

### Funcionamento do Parser

O servidor processa o XML e mapeia campos como:
*   **Dados do Emitente**: Nome, CNPJ, Inscrição Estadual, Endereço.
*   **Dados do Destinatário**: Nome/Razão Social, CPF/CNPJ.
*   **Itens**: Descrição, quantidade, valor unitário e total.
*   **Totais**: BC ICMS, Valor ICMS, Valor Total da Nota.
*   **Informações de Pagamento**: Formas de pagamento utilizadas.
*   **Protocolo de Autorização**: Número, data e hora da autorização.

### Requisitos do XML

Para que a impressão ocorra sem erros, o XML deve:
1.  Seguir o padrão nacional de NF-e/NFC-e (versão 4.00).
2.  Estar completo e conter as tags de protocolo de autorização (`<protNFe>` ou `<protCTe>`).
3.  Ser enviado com o header `Content-Type: text/xml`.

!!! tip "Customização"
    Se precisar de um layout muito específico ou marcas próprias que não constam no XML, utilize o endpoint `POST /print/text` para montar o comprovante linha por linha.

---

## POST /print/image

Imprime uma imagem diretamente na impressora do terminal.

**Corpo da requisição**

Bytes binários da imagem, enviados diretamente no corpo da requisição.

| Header | Valor |
|--------|-------|
| `Content-Type` | `application/octet-stream` |

**Resposta — 200**

```json
{ "message": "Impressão realizada com sucesso" }
```

**Resposta — 400** (nenhuma imagem enviada)

```json
{ "code": 400, "message": "Nenhuma imagem enviada" }
```

**Resposta — 500** (erro na impressão)

```json
{ "code": 500, "message": "Erro ao imprimir" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/octet-stream" \
         -X POST http://localhost:9050/print/image \
         --data-binary @comprovante.png
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip — configure uma vez; demais exemplos nesta página omitem esta etapa
    import 'dart:io';

    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final imageData = await File('comprovante.png').readAsBytes();
    await TefIP.instance.printImage.post(imageData: imageData);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const imageBytes = await fetch('/comprovante.png').then(r => r.arrayBuffer());
    const res = await fetch('http://localhost:9050/print/image', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/octet-stream',
      },
      body: imageBytes,
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    <?php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $imageData = file_get_contents('comprovante.png');
    $ch = curl_init('http://localhost:9050/print/image');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/octet-stream']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $imageData);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    image_data = File.binread('comprovante.png')
    uri = URI('http://localhost:9050/print/image')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/octet-stream')
    req.basic_auth('admin', '1234')
    req.body = image_data
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /print/text

Imprime um comprovante com formatação personalizada. O corpo é um array JSON de instruções de impressão.

**Corpo da requisição**

```json
[
  { "text": { "value": "COMPROVANTE", "size": 20, "bold": true,  "align": "center" } },
  { "line": { "divider": true } },
  { "text": { "value": "Produto: Coca-Cola",  "size": 14, "bold": false, "align": "left" } },
  { "text": { "value": "Valor:   R$ 5,00",    "size": 14, "bold": false, "align": "left" } },
  { "line": { "divider": true } },
  { "text": { "value": "Obrigado pela compra!", "size": 14, "bold": false, "align": "center" } }
]
```

Cada item do array define um elemento de impressão pela sua chave de tipo:

| Tipo | Exemplo |
|------|---------|
| Texto | `{ "text": { "value": "...", "size": 16, "bold": false, "align": "left" } }` |
| Divisor | `{ "line": { "divider": true } }` |

**Resposta — 200**

```json
{ "message": "Impressão realizada com sucesso" }
```

**Resposta — 400** (nenhum conteúdo enviado)

```json
{ "code": 500, "message": "Nenhum texto enviado" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/print/text \
         -d '[
           {"text": {"value": "COMPROVANTE", "size": 20, "bold": true, "align": "center"}},
           {"line": {"divider": true}},
           {"text": {"value": "Obrigado!", "size": 14, "bold": false, "align": "center"}}
         ]'
    ```

=== "Dart"

    ```dart
    await TefIP.instance.printText.post(
      text: [
        {'text': {'value': 'COMPROVANTE', 'size': 20, 'bold': true,  'align': 'center'}},
        {'line': {'divider': true}},
        {'text': {'value': 'Obrigado!',   'size': 14, 'bold': false, 'align': 'center'}},
      ],
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/print/text', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify([
        { text: { value: 'COMPROVANTE', size: 20, bold: true,  align: 'center' } },
        { line: { divider: true } },
        { text: { value: 'Obrigado!',   size: 14, bold: false, align: 'center' } },
      ]),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    <?php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/print/text');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        ['text' => ['value' => 'COMPROVANTE', 'size' => 20, 'bold' => true,  'align' => 'center']],
        ['line' => ['divider' => true]],
        ['text' => ['value' => 'Obrigado!',   'size' => 14, 'bold' => false, 'align' => 'center']],
    ]));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/print/text')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = [
      { text: { value: 'COMPROVANTE', size: 20, bold: true,  align: 'center' } },
      { line: { divider: true } },
      { text: { value: 'Obrigado!',   size: 14, bold: false, align: 'center' } },
    ].to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /print/xml

Imprime um cupom fiscal a partir de um XML de NF-e/DANFE. O TEF IP faz o parse do XML e renderiza automaticamente o layout do cupom fiscal.

**Corpo da requisição**

String com o conteúdo do XML da NF-e.

| Header | Valor |
|--------|-------|
| `Content-Type` | `text/xml` |

```xml
<?xml version="1.0" encoding="UTF-8"?>
<nfeProc xmlns="http://www.portalfiscal.inf.br/nfe" versao="4.00">
  ...
</nfeProc>
```

**Resposta — 200**

```json
{ "message": "Impressão realizada com sucesso" }
```

**Resposta — 400** (nenhum XML enviado)

```json
{ "code": 403, "message": "Nenhum XML enviado" }
```

**Resposta — 500** (erro na impressão)

```json
{ "code": 500, "message": "Erro ao imprimir" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: text/xml" \
         -X POST http://localhost:9050/print/xml \
         --data-binary @nota-fiscal.xml
    ```

=== "Dart"

    ```dart
    import 'dart:io';

    final xml = await File('nota-fiscal.xml').readAsString();
    await TefIP.instance.printXml.post(xml: xml);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const xml = await fetch('/nota-fiscal.xml').then(r => r.text());
    const res = await fetch('http://localhost:9050/print/xml', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'text/xml',
      },
      body: xml,
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    <?php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $xml = file_get_contents('nota-fiscal.xml');
    $ch = curl_init('http://localhost:9050/print/xml');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: text/xml']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $xml);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    xml = File.read('nota-fiscal.xml')
    uri = URI('http://localhost:9050/print/xml')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'text/xml')
    req.basic_auth('admin', '1234')
    req.body = xml
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /print/acbr

Imprime um layout descrito no formato de **tags ACBr** — texto puro enviado diretamente no corpo da requisição (sem JSON). Útil para reaproveitar layouts de PDV já escritos nesse padrão.

**Corpo da requisição**

Texto puro com as tags ACBr.

| Header | Valor |
|--------|-------|
| `Content-Type` | `text/plain` |

**Tags suportadas**

| Categoria | Tags |
|-----------|------|
| Estilo | `<n>` negrito · `<i>` itálico · `<s>` sublinhado · `<in>` invertido · `<e>` expandido · `<c>` condensado · `<a>` altura dupla |
| Alinhamento | `</ce>` centro · `</ae>` esquerda · `</ad>` direita |
| Estrutura | `</zera>` reset · `</fn>` fonte normal · `</linha_simples>` e `</linha_dupla>` divisórias · `<qrcode>...</qrcode>` QR Code |

!!! note "Tags ignoradas"
    `</corte_total>`, `</corte>` e `</logo>` são aceitas, mas não produzem efeito no terminal.

**Resposta — 200**

```json
{ "message": "Impressão realizada com sucesso" }
```

**Resposta — 400** (nenhum conteúdo enviado)

```json
{ "code": 400, "message": "Nenhum conteúdo enviado" }
```

**Resposta — 500** (erro na impressão)

```json
{ "code": 500, "message": "Erro ao imprimir" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: text/plain" \
         -X POST http://localhost:9050/print/acbr \
         --data-binary $'</ce><n>TEF IP</n>\n</ae>Obrigado pela compra!\n</linha_simples>'
    ```

=== "Dart"

    ```dart
    // TODO: o SDK Dart ainda não expõe método para ACBr — usando http diretamente
    import 'package:http/http.dart' as http;

    final layout = '</ce><n>TEF IP</n>\n</ae>Obrigado pela compra!\n</linha_simples>';
    await http.post(
      Uri.parse('http://localhost:9050/print/acbr'),
      headers: {
        'Authorization': 'Basic ${base64Encode(utf8.encode('admin:1234'))}',
        'Content-Type': 'text/plain',
      },
      body: layout,
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const layout = '</ce><n>TEF IP</n>\n</ae>Obrigado pela compra!\n</linha_simples>';
    const res = await fetch('http://localhost:9050/print/acbr', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'text/plain',
      },
      body: layout,
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    <?php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $layout = "</ce><n>TEF IP</n>\n</ae>Obrigado pela compra!\n</linha_simples>";
    $ch = curl_init('http://localhost:9050/print/acbr');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: text/plain']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $layout);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    layout = "</ce><n>TEF IP</n>\n</ae>Obrigado pela compra!\n</linha_simples>"
    uri = URI('http://localhost:9050/print/acbr')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'text/plain')
    req.basic_auth('admin', '1234')
    req.body = layout
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```
