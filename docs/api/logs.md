# Logs

Endpoints para consultar, acompanhar em tempo real e exportar os logs do TEF IP. Eles ajudam a investigar falhas de integração, comportamento do servidor e eventos de roteamento HTTP.

!!! warning "Autenticação"
    Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação).

!!! tip "Quando usar cada endpoint"
    Use `GET /logs` para análise pontual, `GET /logs/stream` para monitoramento em tempo real e `GET /logs/zip/download` quando precisar anexar os registros a um chamado ou auditoria.

---

## GET /logs

Retorna uma lista de logs, com suporte a filtros por nível, origem, intervalo de datas e texto.

**Query parameters**

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `level` | string | Não | Nível do log: `fatal`, `error`, `warning`, `info`, `trace`, `path`, `debug` |
| `source` | string | Não | Origem do log: `app`, `router`, `http` |
| `dateFrom` | string | Não | Data/hora inicial em ISO 8601 |
| `dateTo` | string | Não | Data/hora final em ISO 8601 |
| `limit` | int | Não | Número máximo de registros retornados |
| `search` | string | Não | Busca parcial em `message` e `details` |

**Resposta**

```json
[
  {
    "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "level": "error",
    "source": "http",
    "message": "POST /transaction retornou 500",
    "details": "Exception: connection refused\n  at ...",
    "createdAt": "2024-06-15T14:30:00.000Z"
  }
]
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | string | Identificador único do log |
| `level` | string | Severidade do evento |
| `source` | string | Origem do log |
| `message` | string | Mensagem principal |
| `details` | string \| null | Detalhes adicionais, como stack trace ou payload |
| `createdAt` | string | Data/hora em ISO 8601 |

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         "http://localhost:9050/logs?level=error&source=http&limit=50&search=timeout"
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip — configure uma vez; demais exemplos nesta página omitem esta etapa
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final logs = await TefIP.instance.log.getAll(
      level: TefIPLogLevel.error,
      source: TefIPLogSource.http,
      limit: 50,
      search: 'timeout',
    );
    print(logs.first.message);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/logs?level=error&source=http&limit=50&search=timeout', {
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    <?php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/logs?level=error&source=http&limit=50&search=timeout');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/logs?level=error&source=http&limit=50&search=timeout')
    req = Net::HTTP::Get.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## GET /logs/zip/download

Exporta os logs filtrados como um arquivo ZIP. O arquivo retornado contém `tefip_logs.log` em texto plano.

**Query parameters**

Os mesmos filtros de `GET /logs` são aceitos, exceto `search`.

**Resposta — 200**

| Header | Valor |
|--------|-------|
| `Content-Type` | `application/zip` |
| `Content-Disposition` | `attachment; filename="tefip_logs.zip"` |

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -o tefip_logs.zip \
         "http://localhost:9050/logs/zip/download?level=error&limit=200"
    ```

=== "Dart"

    ```dart
    import 'dart:io';

    final zipBytes = await TefIP.instance.log.downloadZip(
      level: TefIPLogLevel.error,
      limit: 200,
    );
    await File('tefip_logs.zip').writeAsBytes(zipBytes);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/logs/zip/download?level=error&limit=200', {
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });
    const blob = await res.blob();
    ```

=== "PHP"

    ```php
    <?php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/logs/zip/download?level=error&limit=200');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $zipBytes = curl_exec($ch);
    curl_close($ch);
    file_put_contents('tefip_logs.zip', $zipBytes);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'

    uri = URI('http://localhost:9050/logs/zip/download?level=error&limit=200')
    req = Net::HTTP::Get.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    File.binwrite('tefip_logs.zip', res.body)
    ```

---

## GET /logs/stream

Abre uma conexão SSE (Server-Sent Events) e envia um novo evento para cada log gerado enquanto a conexão estiver aberta.

**Resposta**

```text
data: {"id":"abc","level":"info","source":"http","message":"POST /transaction 200","details":null,"createdAt":"2024-06-15T14:30:00.000Z"}
```

| Header | Valor |
|--------|-------|
| `Content-Type` | `text/event-stream` |
| `Cache-Control` | `no-cache` |
| `X-Accel-Buffering` | `no` |

### Exemplos de integração

=== "cURL"

    ```bash
    curl -N -u admin:1234 \
         http://localhost:9050/logs/stream
    ```

=== "Dart"

    ```dart
    TefIP.instance.log.stream().listen((log) {
      print('[${log.level.name}] ${log.message}');
    });
    ```

=== "JavaScript"

    ```js
    // TODO: EventSource não permite definir Authorization; para browser, prefira um proxy autenticado
    const res = await fetch('http://localhost:9050/logs/stream', {
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });

    const reader = res.body.getReader();
    const decoder = new TextDecoder();

    while (true) {
      const { done, value } = await reader.read();
      if (done) break;
      console.log(decoder.decode(value, { stream: true }));
    }
    ```

=== "PHP"

    ```php
    <?php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/logs/stream');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_WRITEFUNCTION, function ($curl, $chunk) {
        echo $chunk;
        return strlen($chunk);
    });
    curl_exec($ch);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'

    uri = URI('http://localhost:9050/logs/stream')
    req = Net::HTTP::Get.new(uri)
    req.basic_auth('admin', '1234')

    Net::HTTP.start(uri.hostname, uri.port) do |http|
      http.request(req) do |res|
        res.read_body do |chunk|
          puts chunk
        end
      end
    end
    ```
