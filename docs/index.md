# TEF IP

Aceite pagamentos com qualquer adquirente usando uma única API HTTP local. Instale no terminal e comece a processar.

---

## Como funciona

```mermaid
flowchart LR
    PDV["Seu sistema<br>(qualquer linguagem)"]
    tefip["TEF IP<br>IP Local"]
    HW["Adquirente"]

    PDV -- "HTTP + Basic Auth" --> tefip
    tefip -- "SDK do adquirente" --> HW
    HW -- "aprovação / erro" --> tefip
    tefip -- "JSON" --> PDV
```

Seu sistema faz chamadas HTTP para o TEF IP. O TEF IP se comunica com o hardware do adquirente e retorna o resultado em JSON, sem nenhuma SDK proprietária no seu lado.

!!! tip "Sem maquininha? Use o emulador!"
    O TEF IP inclui um **modo emulador** que simula o hardware do adquirente localmente.
    Você pode desenvolver e testar toda a integração sem nenhum terminal físico.
    [Clique aqui para saber como usar o emulador](emulator.md)

Veja abaixo um pagamento sendo processado no emulador:

![GIF do terminal processando pagamento manual](assets/gif/emulador-terminal-pagamento-manual.gif){ style="display: block; margin: 0 auto;" }

---

## O que você pode fazer

- **Pagamentos** — Crédito, débito, PIX, dinheiro, voucher, cartão-presente; parcelamento pelo lojista ou pela emissora.
- **Vendas** — Monte um carrinho com itens e adicione múltiplas formas de pagamento; finalize com uma chamada.
- **Estornos** — Consulte e reverta transações por `referenceId`.
- **Display** — Exiba textos, imagens, carrosséis ou QR codes na tela do terminal em tempo real.
- **Perguntas** — Colete dados do cliente direto no terminal: CPF/CNPJ, texto livre, lista de opções, e-mail, CEP e mais.
- **Impressão** — Imprima imagens, comprovantes personalizados, layouts ACBr ou cupons fiscais (XML/DANFE).
- **Logs e alertas** — Consulte logs, acompanhe eventos em tempo real e dispare notificações locais ao operador.
- **Status** — Monitore saúde, tempo de atividade e reinicie o app remotamente.

---

## Integração rápida

Qualquer cliente HTTP funciona. Veja um exemplo completo, um PIX de R$ 50,00, nas linguagens mais comuns:

=== "cURL"

    ```bash
    curl -u admin:1234 \
         -H "Content-Type: application/json" \
         -X POST http://localhost:9050/transaction \
         -d '{"tPag":"17","amount":50.00,"referenceId":"pedido-001"}'
    ```

=== "Dart"

    ```dart
    import 'package:dart_tefip/dart_tefip.dart';

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
    <?php
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
    req.body = { 'tPag' => '17', amount: 50.00, referenceId: 'pedido-001' }.to_json
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    ```

---

## Adquirentes suportados

Cada build do TEF IP é compilado para um adquirente específico.

<div class="grid cards">
  <ul>
   <li>
        <a href="https://www.stone.com.br" target="_blank" style="display: block;">
            <strong>Stone</strong>
        </a>
    </li>
    <li>
        <a href="https://www.getnet.com.br" target="_blank" style="display: block;">
            <strong>Getnet</strong>
        </a>
    </li>
    <li>
        <a href="https://www.userede.com.br" target="_blank" style="display: block;">
            <strong>Rede</strong>
        </a>
    </li>
    <li>
        <a href="https://pagseguro.uol.com.br" target="_blank" style="display: block;">
            <strong>PagSeguro</strong>
        </a>
    </li>
  </ul>
</div>

---

## SDKs disponíveis

| Linguagem | Pacote | Status |
|-----------|--------|--------|
| Dart / Flutter | [`dart_tefip`](https://pub.dev/packages/dart_tefip) | Disponível |
| JavaScript | — | Sem pacote oficial no momento |
| PHP | — | Sem pacote oficial no momento |
| Ruby | — | Sem pacote oficial no momento |

Qualquer cliente HTTP funciona diretamente; os SDKs são conveniência, não requisito.

---

## Próximos Passos

Novo por aqui? Comece pelo guia:

**[Primeiros Passos →](getting-started.md)**

Instale o TEF IP, verifique a conexão e faça sua primeira requisição em menos de 10 minutos.
