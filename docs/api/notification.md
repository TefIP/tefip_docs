# Notificações

Endpoint para disparar uma notificação local no dispositivo que executa o TEF IP. É útil para chamar o operador de volta ao app ou sinalizar um evento importante no fluxo de pagamento.

!!! warning "Autenticação"
    Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação).

!!! tip "Comportamento no Android"
    Em dispositivos Android, o TEF IP acorda a tela antes de exibir a notificação.

---

## POST /notification

Envia uma notificação local com título e mensagem.

**Corpo da requisição**

```json
{
  "title": "Novo pagamento",
  "message": "Restaure o aplicativo para processar"
}
```

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `title` | string | Sim | Título da notificação |
| `message` | string | Sim | Corpo da notificação |

**Resposta — 200**

```json
{
  "message": "Notificação enviada com sucesso"
}
```

**Resposta — 400**

```json
{
  "code": 400,
  "message": "Corpo da requisição inválido"
}
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/notification \
         -d '{"title":"Novo pagamento","message":"Restaure o aplicativo para processar"}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip — configure uma vez; demais exemplos nesta página omitem esta etapa
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.notification.post(
      request: NotificationRequestModel(
        title: 'Novo pagamento',
        message: 'Restaure o aplicativo para processar',
      ),
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/notification', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        title: 'Novo pagamento',
        message: 'Restaure o aplicativo para processar',
      }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    <?php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/notification');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        'title' => 'Novo pagamento',
        'message' => 'Restaure o aplicativo para processar',
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

    uri = URI('http://localhost:9050/notification')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = {
      title: 'Novo pagamento',
      message: 'Restaure o aplicativo para processar',
    }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```
