# Perguntas (Ask)

Endpoints para exibir perguntas interativas na tela do terminal e coletar respostas do cliente — CPF, e-mail, texto livre, listas de opções e muito mais.

!!! warning "Autenticação"
    Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação).

---

## POST /ask

Exibe uma única pergunta na tela do terminal e aguarda a resposta do cliente.

**Corpo da requisição**

```json
{
  "parameters": {
    "buttonText": "Confirmar",
    "showCancelButton": false,
    "buttonCancelText": "Cancelar",
    "showSuccessMessage": false,
    "successMessage": null,
    "successMessageInterval": 3000,
    "confirmAnswer": false
  },
  "question": {
    "id": 0,
    "question": "Informe seu CPF",
    "type": "CPF",
    "required": false,
    "minLength": 0,
    "maxLength": 255,
    "defaultValue": null,
    "mask": null,
    "regex": null,
    "errorMessage": null,
    "options": null
  }
}
```

**Campos de `parameters`**

| Campo | Tipo | Padrão | Descrição |
|-------|------|--------|-----------|
| `buttonText` | string | `"Confirmar"` | Texto do botão de confirmação |
| `showCancelButton` | bool | `false` | Exibe botão de cancelamento |
| `buttonCancelText` | string | `"Cancelar"` | Texto do botão de cancelamento |
| `showSuccessMessage` | bool | `false` | Exibe tela de sucesso após confirmação |
| `successMessage` | string | `null` | Mensagem de sucesso personalizada |
| `successMessageInterval` | int | `3000` | Duração (ms) da tela de sucesso |
| `confirmAnswer` | bool | `false` | Exige confirmação antes de submeter a resposta |

**Campos de `question`**

| Campo | Tipo | Padrão | Descrição |
|-------|------|--------|-----------|
| `id` | int | `0` | Identificador da pergunta |
| `question` | string | `null` | Texto exibido na tela (se `null`, usa prompt padrão do tipo) |
| `type` | string | `"TEXT"` | Tipo de entrada (ver tabela abaixo) |
| `required` | bool | `false` | Resposta obrigatória |
| `minLength` | int | `0` | Comprimento mínimo da resposta |
| `maxLength` | int | `255` | Comprimento máximo da resposta |
| `defaultValue` | string | `null` | Valor pré-preenchido no campo |
| `mask` | string | `null` | Máscara de entrada (ex.: `"###.###.###-##"`) |
| `regex` | string | `null` | Regex de validação personalizado |
| `errorMessage` | string | `null` | Mensagem de erro quando a validação falha |
| `options` | array | `null` | Opções selecionáveis (obrigatório para `LIST` e `BUTTON`) |

**Tipos de entrada (`type`)**

| Valor | Descrição |
|-------|-----------|
| `"TEXT"` | Texto livre |
| `"NUMBER"` | Somente números |
| `"PHONE"` | Número de telefone |
| `"CPF"` | CPF (com validação) |
| `"CNPJ"` | CNPJ (com validação) |
| `"CPFORCNPJ"` | CPF ou CNPJ |
| `"EMAIL"` | E-mail (com validação) |
| `"CEP"` | CEP (com validação) |
| `"DATE"` | Data |
| `"TIME"` | Hora |
| `"MONEY"` | Valor monetário |
| `"REGEX"` | Validação por regex personalizado |
| `"LIST"` | Lista de opções (requer `options`) |
| `"BUTTON"` | Botões de opção (requer `options`) |

**Formato de `options`** (obrigatório para `LIST` e `BUTTON`)

```json
[
  { "id": 1, "name": "Sim", "value": "sim" },
  { "id": 2, "name": "Não", "value": "nao" }
]
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | int | Identificador único da opção |
| `name` | string | Texto exibido na tela |
| `value` | string | Valor retornado quando a opção é selecionada |

**Resposta — 200**

```json
{
  "id": 0,
  "value": "12345678900"
}
```

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | int | Identificador da pergunta respondida |
| `value` | string | Resposta fornecida pelo cliente |

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/ask \
         -d '{
           "parameters": { "buttonText": "Confirmar" },
           "question": { "type": "CPF", "question": "Informe seu CPF" }
         }'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final answer = await TefIP.instance.ask.post(
      questionRequest: AskSingleQuestionRequestModel(
        parameters: AskParametersModel(buttonText: 'Confirmar'),
        question: AskQuestionModel(
          question: 'Informe seu CPF',
          type: TefIPQuestionType.cpf,
        ),
      ),
    );
    print(answer.value);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/ask', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        parameters: { buttonText: 'Confirmar' },
        question: { type: 'CPF', question: 'Informe seu CPF' },
      }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/ask');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        'parameters' => ['buttonText' => 'Confirmar'],
        'question'   => ['type' => 'CPF', 'question' => 'Informe seu CPF'],
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

    uri = URI('http://localhost:9050/ask')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = {
      parameters: { buttonText: 'Confirmar' },
      question: { type: 'CPF', question: 'Informe seu CPF' },
    }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /ask/form

Exibe um formulário com múltiplas perguntas em sequência. O cliente responde cada uma antes de passar para a próxima.

!!! tip "IDs automáticos"
    Se todas as perguntas tiverem `id: 0`, o TEF IP atribui IDs sequenciais automaticamente (0, 1, 2…). Se quiser IDs personalizados, cada pergunta deve ter um `id` único — duplicatas retornam `400`.

**Corpo da requisição**

```json
{
  "parameters": {
    "buttonText": "Próximo",
    "showCancelButton": true,
    "buttonCancelText": "Cancelar"
  },
  "questions": [
    {
      "id": 1,
      "question": "Nome completo",
      "type": "TEXT",
      "required": true
    },
    {
      "id": 2,
      "question": "CPF",
      "type": "CPF",
      "required": true
    },
    {
      "id": 3,
      "question": "Como prefere pagar?",
      "type": "LIST",
      "options": [
        { "id": 1, "name": "Crédito", "value": "credit" },
        { "id": 2, "name": "Débito",  "value": "debit"  },
        { "id": 3, "name": "PIX",     "value": "pix"    }
      ]
    }
  ]
}
```

**Resposta — 200**

Array com a resposta de cada pergunta, na mesma ordem.

```json
[
  { "id": 1, "value": "João Silva" },
  { "id": 2, "value": "12345678900" },
  { "id": 3, "value": "pix" }
]
```

**Resposta — 400**

```json
{ "code": 400, "message": "Nenhuma pergunta recebida" }
```
```json
{ "code": 400, "message": "Existem perguntas com IDs duplicados no formulário" }
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/ask/form \
         -d '{
           "parameters": { "buttonText": "Próximo" },
           "questions": [
             { "id": 1, "question": "Nome", "type": "TEXT" },
             { "id": 2, "question": "CPF",  "type": "CPF"  }
           ]
         }'
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    final answers = await TefIP.instance.askForm.post(
      form: AskFormRequestModel(
        parameters: AskParametersModel(buttonText: 'Próximo'),
        questions: [
          AskQuestionModel(id: 1, question: 'Nome', type: TefIPQuestionType.text),
          AskQuestionModel(id: 2, question: 'CPF',  type: TefIPQuestionType.cpf),
        ],
      ),
    );
    for (final a in answers) {
      print('${a.id}: ${a.value}');
    }
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/ask/form', {
      method: 'POST',
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        parameters: { buttonText: 'Próximo' },
        questions: [
          { id: 1, question: 'Nome', type: 'TEXT' },
          { id: 2, question: 'CPF',  type: 'CPF'  },
        ],
      }),
    });
    const data = await res.json();
    ```

=== "PHP"

    ```php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/ask/form');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode([
        'parameters' => ['buttonText' => 'Próximo'],
        'questions'  => [
            ['id' => 1, 'question' => 'Nome', 'type' => 'TEXT'],
            ['id' => 2, 'question' => 'CPF',  'type' => 'CPF' ],
        ],
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

    uri = URI('http://localhost:9050/ask/form')
    req = Net::HTTP::Post.new(uri, 'Content-Type' => 'application/json')
    req.basic_auth('admin', '1234')
    req.body = {
      parameters: { buttonText: 'Próximo' },
      questions: [
        { id: 1, question: 'Nome', type: 'TEXT' },
        { id: 2, question: 'CPF',  type: 'CPF'  },
      ],
    }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## POST /ask/cancel

Cancela a pergunta ou formulário em exibição no momento.

Não há corpo na requisição.

**Resposta — 200**

```json
{
  "message": "Pergunta cancelada com sucesso"
}
```

### Exemplos de integração

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -X POST http://localhost:9050/ask/cancel
    ```

=== "Dart"

    ```dart
    // pub.dev/packages/dart_tefip
    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';
    await TefIP.instance.askCancel.post();
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/ask/cancel', {
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
    $ch = curl_init('http://localhost:9050/ask/cancel');
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

    uri = URI('http://localhost:9050/ask/cancel')
    req = Net::HTTP::Post.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```
