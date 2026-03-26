# Venda

Endpoints para gerenciar vendas: abrir um carrinho, adicionar itens e pagamentos, e finalizar ou cancelar a venda.

!!! warning "Autenticação"
    Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação).

!!! info "Fluxo de venda"
    Uma venda segue a sequência: **iniciar** → **adicionar itens** → **adicionar pagamentos** → **finalizar** (ou **cancelar**). Apenas uma venda pode estar ativa por vez.

---

## POST /sale

Inicia uma nova venda. Retorna `409` se já existir uma venda ativa.

**Corpo da requisição**

```json
{
  "customerDocument": "123.456.789-00",
  "customerName": "João Silva",
  "sellerName": "Maria",
  "additionalInfo": "Balcão 3"
}
```

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|:-----------:|-----------|
| `customerDocument` | string | Não | CPF ou CNPJ do cliente |
| `customerName` | string | Não | Nome do cliente exibido no terminal |
| `sellerName` | string | Não | Nome do vendedor exibido no terminal |
| `additionalInfo` | string | Não | Informação adicional exibida no terminal |

**Resposta — 200**

```json
{ "message": "Venda iniciada com sucesso" }
```

**Resposta — 409** (já existe uma venda ativa)

```json
{ "code": 409, "message": "Já existe uma venda ativa!" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/sale \
         -d '{"customerName":"João Silva","sellerName":"Maria"}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.sale.post(
      request: SaleStartRequestModel(
        customerName: 'João Silva',
        sellerName: 'Maria',
      ),
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ customerName: 'João Silva', sellerName: 'Maria' }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        'customerName' => 'João Silva',
        'sellerName'   => 'Maria',
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

    uri = URI('http://localhost:9050/sale')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = { customerName: 'João Silva', sellerName: 'Maria' }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## PATCH /sale

Atualiza os dados da venda ativa (cliente, vendedor, informações adicionais). Usa o mesmo corpo que `POST /sale`.

**Resposta — 200**

```json
{ "message": "Venda atualizada com sucesso" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X PATCH http://localhost:9050/sale \
         -d '{"customerName":"Maria Souza"}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.sale.patch(
      request: SaleStartRequestModel(customerName: 'Maria Souza'),
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale', {
      method: 'PATCH',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ customerName: 'Maria Souza' }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'PATCH');
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode(['customerName' => 'Maria Souza']));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/sale')
    req = Net::HTTP::Patch.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = { customerName: 'Maria Souza' }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /sale/item

Adiciona um item ao carrinho da venda ativa.

**Corpo da requisição**

```json
{
  "id": "item-001",
  "code": "7891234567890",
  "description": "Coca-Cola 350ml",
  "canceled": false,
  "quantity": 2.0,
  "unitPrice": 5.00,
  "discount": 0.50,
  "addition": null,
  "total": 9.50,
  "additionalInfo": null
}
```

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|:-----------:|-----------|
| `id` | string | Não | Identificador externo do item |
| `code` | string | Sim | Código do produto (ex.: EAN/código de barras) |
| `description` | string | Sim | Descrição exibida no terminal |
| `canceled` | bool | Não | Item marcado como cancelado (padrão: `false`) |
| `quantity` | number | Sim | Quantidade |
| `unitPrice` | number | Sim | Preço unitário |
| `discount` | number | Não | Desconto aplicado ao item |
| `addition` | number | Não | Acréscimo aplicado ao item |
| `total` | number | Sim | Valor total do item |
| `additionalInfo` | string | Não | Informação adicional |

**Resposta — 200**

```json
{ "message": "Item adicionado com sucesso", "itemId": "item-001" }
```

**Resposta — 400** (item duplicado)

```json
{ "code": 400, "message": "Item já existente na venda!" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/sale/item \
         -d '{"code":"7891234567890","description":"Coca-Cola 350ml","quantity":2,"unitPrice":5.00,"total":9.50}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final result = await TefIP.instance.saleItem.post(
      item: SaleItemModel(
        code: '7891234567890',
        description: 'Coca-Cola 350ml',
        quantity: 2,
        unitPrice: 5.00,
        total: 9.50,
      ),
    );
    print(result.itemId);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale/item', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        code: '7891234567890',
        description: 'Coca-Cola 350ml',
        quantity: 2,
        unitPrice: 5.00,
        total: 9.50,
      }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale/item');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        'code'        => '7891234567890',
        'description' => 'Coca-Cola 350ml',
        'quantity'    => 2,
        'unitPrice'   => 5.00,
        'total'       => 9.50,
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

    uri = URI('http://localhost:9050/sale/item')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = {
      code: '7891234567890', description: 'Coca-Cola 350ml',
      quantity: 2, unitPrice: 5.00, total: 9.50,
    }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## PATCH /sale/item/{itemId}

Atualiza os dados de um item já adicionado à venda. O `id` no corpo é ignorado — o identificador vem do parâmetro de rota.

**Parâmetros de rota**

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `itemId` | string | Identificador do item a atualizar |

Corpo igual ao de `POST /sale/item`.

**Resposta — 200**

```json
{ "message": "Item atualizado com sucesso", "itemId": "item-001" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X PATCH http://localhost:9050/sale/item/item-001 \
         -d '{"code":"7891234567890","description":"Coca-Cola 350ml","quantity":3,"unitPrice":5.00,"total":14.50}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.saleItem.patch(
      itemId: 'item-001',
      item: SaleItemModel(
        code: '7891234567890',
        description: 'Coca-Cola 350ml',
        quantity: 3,
        unitPrice: 5.00,
        total: 14.50,
      ),
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale/item/item-001', {
      method: 'PATCH',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ code: '7891234567890', description: 'Coca-Cola 350ml', quantity: 3, unitPrice: 5.00, total: 14.50 }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale/item/item-001');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'PATCH');
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        'code' => '7891234567890', 'description' => 'Coca-Cola 350ml',
        'quantity' => 3, 'unitPrice' => 5.00, 'total' => 14.50,
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

    uri = URI('http://localhost:9050/sale/item/item-001')
    req = Net::HTTP::Patch.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = { code: '7891234567890', description: 'Coca-Cola 350ml', quantity: 3, unitPrice: 5.00, total: 14.50 }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## DELETE /sale/item/{itemId}

Remove um item **permanentemente** do carrinho da venda ativa.

!!! tip "Cancel vs Delete"
    - **DELETE** `/sale/item/{itemId}` — remove o item do carrinho; não aparece mais na venda
    - **POST** `/sale/item/{itemId}/cancel` — mantém o item no carrinho mas o marca como cancelado (`canceled: true`); útil para manter o histórico de itens cancelados na nota fiscal ou comprovante

**Parâmetros de rota**

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `itemId` | string | Identificador do item a remover |

**Resposta — 200**

```json
{ "message": "Item removido com sucesso", "itemId": "item-001" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -X DELETE http://localhost:9050/sale/item/item-001
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.saleItem.delete(itemId: 'item-001');
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale/item/item-001', {
      method: 'DELETE',
      headers: { 'Authorization': 'Basic ' + btoa('admin:1234') },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale/item/item-001');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'DELETE');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/sale/item/item-001')
    req = Net::HTTP::Delete.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /sale/item/{itemId}/cancel

Marca um item como cancelado sem removê-lo do carrinho. Útil para manter o histórico da venda.

**Parâmetros de rota**

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `itemId` | string | Identificador do item a cancelar |

**Resposta — 200**

```json
{ "message": "Item cancelado com sucesso", "itemId": "item-001" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -X POST http://localhost:9050/sale/item/item-001/cancel
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.saleItem.cancel(itemId: 'item-001');
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale/item/item-001/cancel', {
      method: 'POST',
      headers: { 'Authorization': 'Basic ' + btoa('admin:1234') },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale/item/item-001/cancel');
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

    uri = URI('http://localhost:9050/sale/item/item-001/cancel')
    req = Net::HTTP::Post.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /sale/payment

Adiciona uma forma de pagamento à venda ativa.

**Corpo da requisição**

```json
{
  "id": "pgto-001",
  "tPag": "pix",
  "description": null,
  "value": 50.00,
  "additionalInfo": null
}
```

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|:-----------:|-----------|
| `id` | string | Não | Identificador externo do pagamento |
| `tPag` | string | Não | Tipo de pagamento (ver tabela abaixo) |
| `description` | string | Não | Descrição exibida no terminal |
| `value` | number | Sim | Valor do pagamento |
| `additionalInfo` | string | Não | Informação adicional |

**Valores de `tPag`**

| Valor | Descrição |
|-------|-----------|
| `"credit"` | Crédito |
| `"debit"` | Débito |
| `"pix"` | PIX |
| `"money"` | Dinheiro |
| `"voucher"` | Voucher/ticket |
| `"gift"` | Cartão-presente |
| `"veroWallet"` | Carteira digital Vero |
| `"adm"` | Operação administrativa |
| `"cancel"` | Cancelamento de pagamento |
| `"cancelDigitalWallet"` | Cancelamento de carteira digital |
| `"unknown"` | Desconhecido |

**Resposta — 200**

```json
{ "message": "Pagamento adicionado com sucesso", "paymentId": "pgto-001" }
```

**Resposta — 400** (pagamento duplicado)

```json
{ "code": 400, "message": "Pagamento já existente na venda!" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/sale/payment \
         -d '{"tPag":"pix","value":50.00}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final result = await TefIP.instance.salePayment.post(
      payment: SalePaymentModel(
        type: TefIPSalePaymentType.pix,
        value: 50.00,
      ),
    );
    print(result.paymentId);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale/payment', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ tPag: 'pix', value: 50.00 }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale/payment');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode(['tPag' => 'pix', 'value' => 50.00]));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/sale/payment')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = { tPag: 'pix', value: 50.00 }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## PATCH /sale/payment/{paymentId}

Atualiza os dados de um pagamento já adicionado à venda.

**Parâmetros de rota**

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `paymentId` | string | Identificador do pagamento a atualizar |

Corpo igual ao de `POST /sale/payment`.

**Resposta — 200**

```json
{ "message": "Pagamento atualizado com sucesso", "paymentId": "pgto-001" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X PATCH http://localhost:9050/sale/payment/pgto-001 \
         -d '{"tPag":"credit","value":50.00}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.salePayment.patch(
      paymentId: 'pgto-001',
      payment: SalePaymentModel(
        type: TefIPSalePaymentType.credit,
        value: 50.00,
      ),
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale/payment/pgto-001', {
      method: 'PATCH',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ tPag: 'credit', value: 50.00 }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale/payment/pgto-001');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'PATCH');
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode(['tPag' => 'credit', 'value' => 50.00]));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/sale/payment/pgto-001')
    req = Net::HTTP::Patch.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = { tPag: 'credit', value: 50.00 }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## DELETE /sale/payment/{paymentId}

Remove uma forma de pagamento do carrinho da venda ativa.

**Parâmetros de rota**

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `paymentId` | string | Identificador do pagamento a remover |

**Resposta — 200**

```json
{ "message": "Pagamento removido com sucesso", "paymentId": "pgto-001" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -X DELETE http://localhost:9050/sale/payment/pgto-001
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.salePayment.delete(paymentId: 'pgto-001');
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale/payment/pgto-001', {
      method: 'DELETE',
      headers: { 'Authorization': 'Basic ' + btoa('admin:1234') },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale/payment/pgto-001');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'DELETE');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/sale/payment/pgto-001')
    req = Net::HTTP::Delete.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /sale/finalize

Finaliza a venda ativa. Todos os itens e pagamentos adicionados são consolidados.

**Corpo da requisição** *(opcional)*

```json
{
  "message": "Obrigado pela compra!",
  "showMessage": true,
  "showCloseButton": true,
  "buttonCloseText": "Fechar",
  "messageInterval": 3000
}
```

| Campo | Tipo | Padrão | Descrição |
|-------|------|--------|-----------|
| `message` | string | `null` | Mensagem exibida ao finalizar |
| `showMessage` | bool | `true` | Exibe a mensagem de finalização |
| `showCloseButton` | bool | `true` | Exibe botão para fechar a tela |
| `buttonCloseText` | string | `null` | Texto do botão fechar |
| `messageInterval` | int | `3000` | Duração (ms) da mensagem exibida |

**Resposta — 200**

```json
{ "message": "Venda finalizada com sucesso" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/sale/finalize \
         -d '{"message":"Obrigado pela compra!","showMessage":true}'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.saleFinalize.post(
      params: SaleActionRequestModel(message: 'Obrigado pela compra!'),
    );
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale/finalize', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ message: 'Obrigado pela compra!', showMessage: true }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale/finalize');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode(['message' => 'Obrigado pela compra!', 'showMessage' => true]));
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/sale/finalize')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = { message: 'Obrigado pela compra!', showMessage: true }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /sale/cancel

Cancela a venda ativa e limpa o carrinho.

**Corpo da requisição** *(opcional)*

Mesmo formato de `POST /sale/finalize`.

**Resposta — 200**

```json
{ "message": "Venda cancelada com sucesso" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -X POST http://localhost:9050/sale/cancel
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.saleCancel.post();
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/sale/cancel', {
      method: 'POST',
      headers: { 'Authorization': 'Basic ' + btoa('admin:1234') },
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/sale/cancel');
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

    uri = URI('http://localhost:9050/sale/cancel')
    req = Net::HTTP::Post.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```
