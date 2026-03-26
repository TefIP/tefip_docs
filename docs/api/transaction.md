# Transações

Endpoints para processar pagamentos, consultar histórico e realizar estornos.

!!! warning "Autenticação"
    Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação).

---

## POST /transaction

Inicia um pagamento no terminal. O TEF IP aguarda o app estar em primeiro plano por até **15 segundos** antes de processar — se o app estiver minimizado, o endpoint retorna `503`.

!!! info "App em segundo plano"
    Ao receber uma transação, o TEF IP verifica se o app está em foreground (`isActive = true`). Se não estiver, aguarda até **15 segundos** — nesse tempo o push notification enviado ao dispositivo alerta o operador para abrir o app. Se o app não voltar, retorna `503`.
    Usando o SDK Dart, o timeout padrão é **sem limite** (adequado para pagamentos). Para definir: `TefIP.requestsTimeOut = const Duration(minutes: 2);`

**Corpo da requisição**

```json
{
  "tPag": "17",
  "amount": 50.00,
  "referenceId": "pedido-001",
  "installments": 1,
  "installmentType": "single",
  "details": {}
}
```

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|:-----------:|-----------|
| `tPag` | string | Sim | Tipo de pagamento (ver tabela abaixo) |
| `amount` | number | Sim | Valor da transação |
| `referenceId` | string | Não | Identificador externo para conciliação |
| `installments` | int | Não | Número de parcelas (padrão: `1`) |
| `installmentType` | string | Não | Modalidade de parcelamento (padrão: `"single"`) |
| `details` | object | Não | Metadados adicionais da transação |

**Valores de `tPag`**

| Valor | Descrição |
|-------|-----------|
| `"03"` | Crédito |
| `"04"` | Débito |
| `"17"` | PIX |
| `"99"` | Desconhecido |

**Valores de `installmentType`**

| Valor | Descrição |
|-------|-----------|
| `"single"` | Pagamento à vista (padrão) |
| `"seller"` | Parcelado pelo lojista (sem juros ao comprador) |
| `"buyer"` | Parcelado pelo comprador (juros ao comprador) |

**Resposta — 200**

```json
{
  "nsu": "123456",
  "message": "Transação aprovada",
  "details": {}
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `nsu` | string | Número sequencial único gerado pelo adquirente |
| `message` | string | Mensagem de status da transação |
| `details` | object | Dados adicionais retornados pelo adquirente |

**Resposta — 503** (app em segundo plano)

```json
{
  "code": 503,
  "message": "Aplicativo em segundo plano. Abra o app para concluir o pagamento."
}
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/transaction \
         -d '{"tPag":"17","amount":50.00,"referenceId":"pedido-001"}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final result = await TefIP.instance.transaction.post(
      transactionRequest: TransactionRequestModel(
        type: TefIPTransactionType.pix,
        amount: 50.00,
        referenceId: 'pedido-001',
      ),
    );
    print(result.nsu);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/transaction', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ tPag: '17', amount: 50.00, referenceId: 'pedido-001' }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/transaction');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        'tPag' => '17',
        'amount' => 50.00,
        'referenceId' => 'pedido-001',
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

    uri = URI('http://localhost:9050/transaction')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = { tPag: '17', amount: 50.00, referenceId: 'pedido-001' }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## GET /transaction

Lista todas as transações registradas no terminal.

**Resposta — 200**

Array de transações, cada uma com a mesma estrutura do retorno de `POST /transaction`.

```json
[
  {
    "nsu": "123456",
    "message": "Transação aprovada",
    "details": {}
  }
]
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         http://localhost:9050/transaction
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final transactions = await TefIP.instance.transaction.getAll();
    for (final t in transactions) {
      print(t.nsu);
    }
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/transaction', {
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/transaction');
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

    uri = URI('http://localhost:9050/transaction')
    req = Net::HTTP::Get.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## GET /transaction/{referenceId}

Busca uma transação específica pelo identificador externo informado no momento do pagamento.

**Parâmetros de rota**

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `referenceId` | string | Identificador externo da transação |

**Resposta — 200**

```json
{
  "nsu": "123456",
  "message": "Transação aprovada",
  "details": {}
}
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         http://localhost:9050/transaction/pedido-001
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final transaction = await TefIP.instance.transaction.get(referenceId: 'pedido-001');
    print(transaction.nsu);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/transaction/pedido-001', {
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $referenceId = 'pedido-001';
    $ch = curl_init("http://localhost:9050/transaction/{$referenceId}");
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

    reference_id = 'pedido-001'
    uri = URI("http://localhost:9050/transaction/#{reference_id}")
    req = Net::HTTP::Get.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /transaction/{referenceId}/reversal

Realiza o estorno de uma transação pelo seu `referenceId`. Assim como o pagamento, aguarda o app estar em primeiro plano por até **15 segundos**.

**Parâmetros de rota**

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `referenceId` | string | Identificador externo da transação a estornar |

Não há corpo na requisição.

**Resposta — 200**

Mesma estrutura de `POST /transaction`.

```json
{
  "nsu": "123456",
  "message": "Estorno aprovado",
  "details": {}
}
```

**Resposta — 503** (app em segundo plano)

```json
{
  "code": 503,
  "message": "Aplicativo em segundo plano. Abra o app para concluir o estorno."
}
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -X POST http://localhost:9050/transaction/pedido-001/reversal
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final result = await TefIP.instance.reversal.post(referenceId: 'pedido-001');
    print(result.message);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/transaction/pedido-001/reversal', {
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
    $referenceId = 'pedido-001';
    $ch = curl_init("http://localhost:9050/transaction/{$referenceId}/reversal");
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

    reference_id = 'pedido-001'
    uri = URI("http://localhost:9050/transaction/#{reference_id}/reversal")
    req = Net::HTTP::Post.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```
