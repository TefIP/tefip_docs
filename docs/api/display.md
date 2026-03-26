# Display

Endpoints para controlar a tela do terminal — exibir imagens, textos formatados, carrosséis e limpar o display.

!!! warning "Autenticação"
    Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação).

---

## POST /display/image

Exibe uma imagem em tela cheia na tela do terminal.

**Corpo da requisição**

Bytes binários da imagem, enviados diretamente no corpo da requisição.

| Header | Valor |
|--------|-------|
| `Content-Type` | `application/octet-stream` |

**Resposta — 200**

```json
{ "message": "Imagem exibida com sucesso" }
```

**Resposta — 400** (nenhuma imagem enviada)

```json
{ "code": 400, "message": "Nenhuma imagem enviada" }
```

**Resposta — 500** (erro ao exibir)

```json
{ "code": 500, "message": "Erro ao exibir imagem" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/octet-stream" \
         -X POST http://localhost:9050/display/image \
         --data-binary @imagem.png
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    import 'dart:io';

    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final imageData = await File('imagem.png').readAsBytes();
    await TefIP.instance.displayImage.post(imageData: imageData);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const imageBytes = await fetch('/imagem.png').then(r => r.arrayBuffer());
    const res = await fetch('http://localhost:9050/display/image', {
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
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $imageData = file_get_contents('imagem.png');
    $ch = curl_init('http://localhost:9050/display/image');
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

    image_data = File.binread('imagem.png')
    uri = URI('http://localhost:9050/display/image')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/octet-stream')
    req.basic_auth('admin', '1234')
    req.body = image_data
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /display/text

Exibe conteúdo de texto formatado na tela do terminal. O conteúdo é renderizado como um layout de comprovante usando o formato do `printer_gateway`.

**Corpo da requisição**

```json
{
  "content": [
    { "text": { "value": "Bem-vindo!", "size": 24, "bold": true, "align": "center" } },
    { "line": { "divider": true } },
    { "text": { "value": "Aguarde o atendimento.", "size": 16, "align": "center" } }
  ],
  "backgroundColor": "#FFFFFF",
  "showCloseButton": true
}
```

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|:-----------:|-----------|
| `content` | array | Sim | Instruções de layout (ver formato abaixo) |
| `backgroundColor` | string | Sim | Cor de fundo em hex (ex.: `"#FFFFFF"`) |
| `showCloseButton` | bool | Não | Exibe botão para fechar a tela (padrão: `true`) |

**Formato de `content`**

Cada item do array é um objeto com uma chave identificando o tipo de elemento:

| Tipo | Exemplo |
|------|---------|
| Texto | `{ "text": { "value": "Olá", "size": 18, "bold": false, "align": "left" } }` |
| Divisor | `{ "line": { "divider": true } }` |

**Resposta — 200**

```json
{ "message": "Texto exibido com sucesso" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/display/text \
         -d '{
           "content": [
             { "text": { "value": "Bem-vindo!", "size": 24, "bold": true, "align": "center" } }
           ],
           "backgroundColor": "#FFFFFF",
           "showCloseButton": true
         }'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.displayText.post(
      displayTextRequest: DisplayTextRequestModel(
        content: [
          {'text': {'value': 'Bem-vindo!', 'size': 24, 'bold': true, 'align': 'center'}},
        ],
        backgroundColor: '#FFFFFF',
        showCloseButton: true,
      ),
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/display/text', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        content: [
          { text: { value: 'Bem-vindo!', size: 24, bold: true, align: 'center' } },
        ],
        backgroundColor: '#FFFFFF',
        showCloseButton: true,
      }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/display/text');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        'content' => [
            ['text' => ['value' => 'Bem-vindo!', 'size' => 24, 'bold' => true, 'align' => 'center']],
        ],
        'backgroundColor' => '#FFFFFF',
        'showCloseButton' => true,
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

    uri = URI('http://localhost:9050/display/text')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = {
      content: [
        { text: { value: 'Bem-vindo!', size: 24, bold: true, align: 'center' } },
      ],
      backgroundColor: '#FFFFFF',
      showCloseButton: true,
    }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /display/carousel

Exibe um carrossel de imagens na tela do terminal, alternando automaticamente em intervalos configuráveis.

**Corpo da requisição**

```json
{
  "images": [
    "<base64 da imagem 1>",
    "<base64 da imagem 2>"
  ],
  "intervalMs": 3000,
  "transition": "fade",
  "backgroundColor": "#000000",
  "showCloseButton": false
}
```

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|:-----------:|-----------|
| `images` | array de string | Sim | Imagens em Base64 (pelo menos uma) |
| `intervalMs` | int | Não | Intervalo entre imagens em ms (padrão: `3000`) |
| `transition` | string | Não | Animação de transição (padrão: `"fade"`) |
| `backgroundColor` | string | Sim | Cor de fundo em hex |
| `showCloseButton` | bool | Não | Exibe botão para fechar (padrão: `false`) |

**Valores de `transition`**

| Valor | Descrição |
|-------|-----------|
| `"fade"` | Transição por dissolução (padrão) |
| `"slide"` | Transição por deslizamento |
| `"zoom"` | Transição por zoom |

**Resposta — 200**

```json
{ "message": "Carousel exibido com sucesso" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    # Converta as imagens para Base64 antes de enviar
    IMG1=$(base64 -w 0 imagem1.png)
    IMG2=$(base64 -w 0 imagem2.png)

    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/display/carousel \
         -d "{\"images\":[\"$IMG1\",\"$IMG2\"],\"intervalMs\":3000,\"transition\":\"fade\",\"backgroundColor\":\"#000000\"}"
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    import 'dart:io';

    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final img1 = await File('imagem1.png').readAsBytes();
    final img2 = await File('imagem2.png').readAsBytes();
    await TefIP.instance.displayCarousel.post(
      displayCarouselRequest: DisplayCarouselRequestModel(
        images: [img1, img2],
        intervalMs: 3000,
        transition: TefIPCarouselTransition.fade,
        backgroundColor: '#000000',
      ),
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    async function fileToBase64(file) {
      return new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = () => resolve(reader.result.split(',')[1]);
        reader.readAsDataURL(file);
      });
    }

    const img1 = await fileToBase64(imagemFile1);
    const img2 = await fileToBase64(imagemFile2);

    const res = await fetch('http://localhost:9050/display/carousel', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        images: [img1, img2],
        intervalMs: 3000,
        transition: 'fade',
        backgroundColor: '#000000',
      }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $img1 = base64_encode(file_get_contents('imagem1.png'));
    $img2 = base64_encode(file_get_contents('imagem2.png'));

    $ch = curl_init('http://localhost:9050/display/carousel');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        'images'          => [$img1, $img2],
        'intervalMs'      => 3000,
        'transition'      => 'fade',
        'backgroundColor' => '#000000',
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
    require 'base64'

    img1 = Base64.strict_encode64(File.binread('imagem1.png'))
    img2 = Base64.strict_encode64(File.binread('imagem2.png'))

    uri = URI('http://localhost:9050/display/carousel')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = {
      images: [img1, img2],
      intervalMs: 3000,
      transition: 'fade',
      backgroundColor: '#000000',
    }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /display/clear

Remove qualquer conteúdo exibido na tela do terminal e retorna ao estado padrão.

Não há corpo na requisição.

**Resposta — 200**

```json
{ "message": "Display limpo com sucesso" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -X POST http://localhost:9050/display/clear
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.displayClear.post();
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/display/clear', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/display/clear');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/display/clear')
    req = Net::HTTP::Post.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /display/pop

Fecha a sobreposição atualmente exibida na tela, voltando à tela anterior sem limpar o estado completo.

Não há corpo na requisição.

**Resposta — 200**

```json
{ "message": "Display fechado com sucesso" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -X POST http://localhost:9050/display/pop
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.displayPop.post();
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/display/pop', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/display/pop');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/display/pop')
    req = Net::HTTP::Post.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```
