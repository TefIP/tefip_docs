# Status

Endpoints para monitorar a saúde do servidor TEF IP, consultar informações do dispositivo e reiniciar o aplicativo remotamente.

!!! warning "Autenticação"
    Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação).

---

## GET /status

Verifica se o servidor está no ar e retorna o tempo de atividade.

**Resposta**

```json
{
  "status": "ok",
  "uptimeSeconds": 144,
  "startedAt": "2026-01-28T16:20:53.223883"
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `status` | string | Sempre `"ok"` quando o servidor responde |
| `uptimeSeconds` | int | Segundos desde a inicialização do servidor |
| `startedAt` | string | Data/hora de início em formato ISO 8601 |

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         http://localhost:9050/status
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final status = await TefIP.instance.status.get();
    print(status.uptimeSeconds);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/status', {
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/status');
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

    uri = URI('http://localhost:9050/status')
    req = Net::HTTP::Get.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## GET /info

Retorna informações detalhadas sobre o aplicativo e o dispositivo, incluindo o estado atual do servidor.

**Resposta**

```json
{
  "appName": "TEF IP",
  "version": "1.2.0",
  "build": "42",
  "platform": "android",
  "locale": "pt_BR",
  "timeZone": "America/Sao_Paulo",
  "mode": "production",
  "isActive": true,
  "isBusy": false
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `appName` | string | Nome do aplicativo |
| `version` | string | Versão semântica |
| `build` | string | Número de build |
| `platform` | string | Plataforma do dispositivo (`android`, `windows`, etc.) |
| `locale` | string | Locale configurado no dispositivo |
| `timeZone` | string | Fuso horário do dispositivo |
| `mode` | string | Modo de operação (`production`, `emulator`) |
| `isActive` | bool | `true` quando o app está em primeiro plano |
| `isBusy` | bool | `true` quando há uma operação em andamento (pagamento, impressão) |

!!! tip "Monitorando isBusy"
    Use `isBusy` para saber se o terminal está livre antes de enviar uma nova operação. Requisições enviadas enquanto `isBusy` é `true` serão rejeitadas com `503`.

!!! info "Saiba mais"
    Veja [Conceitos → Flags isBusy e isActive](../conceitos.md#flag-isbusy) para entender como esses flags afetam o comportamento de transações e impressões.

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         http://localhost:9050/info
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final info = await TefIP.instance.info.get();
    print('${info.appName} v${info.version} — isBusy: ${info.isBusy}');
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/info', {
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/info');
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

    uri = URI('http://localhost:9050/info')
    req = Net::HTTP::Get.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /restart

Reinicia o aplicativo TEF IP remotamente. Disponível **apenas em dispositivos Android**.

!!! warning "Somente Android"
    Em plataformas não-móveis (Windows, emulador de desktop), este endpoint retorna `403`.

**Resposta — 200**

Mesma estrutura de `GET /status`, refletindo o estado após o reinício.

```json
{
  "status": "ok",
  "uptimeSeconds": 0,
  "startedAt": "2026-03-25T10:00:00.000000"
}
```

**Resposta — 403**

```json
{
  "code": 403,
  "message": "Reinicialização disponível apenas para dispositivos móveis"
}
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -X POST http://localhost:9050/restart
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final result = await TefIP.instance.restart.post();
    print(result.status);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/restart', {
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
    $ch = curl_init('http://localhost:9050/restart');
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

    uri = URI('http://localhost:9050/restart')
    req = Net::HTTP::Post.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```
