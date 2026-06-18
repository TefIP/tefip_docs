# SDK Dart

O [`dart_tefip`](https://pub.dev/packages/dart_tefip) é o SDK oficial para Dart e Flutter. Ele encapsula as chamadas HTTP do TEF IP com modelos tipados, serialização de payloads e exceções específicas para a API.

---

## Instalação

Adicione ao seu `pubspec.yaml`:

```yaml
dependencies:
  dart_tefip: ^<versão>
```

Depois execute:

```bash
dart pub get
# ou
flutter pub get
```

---

## Configuração

O SDK usa o padrão **singleton** com setters estáticos:

```dart
import 'package:dart_tefip/dart_tefip.dart';

TefIP.baseUrl = 'http://192.168.1.10:9050';
TefIP.username = 'admin';
TefIP.password = 'minha-senha';
```

Para desenvolvimento local com o emulador:

```dart
TefIP.baseUrl = 'http://localhost:9050';
```

!!! warning "TefIPClient não existe"
    Não existe nenhuma classe `TefIPClient` no SDK. Use sempre `TefIP.instance`.

---

## Chamando endpoints

Todos os endpoints ficam disponíveis via `TefIP.instance.<grupo>.<método>(...)`:

```dart
final result = await TefIP.instance.transaction.post(
  transactionRequest: TransactionRequestModel(
    type: TefIPTransactionType.pix,
    amount: 50.00,
    referenceId: 'pedido-001',
  ),
);

print(result.nsu);
print(result.txid); // PIX
print(result.cAut); // crédito/débito
```

---

## Catálogo de métodos

| Grupo | Métodos disponíveis |
|-------|---------------------|
| `transaction` | `getAll()`, `get(referenceId:)`, `post(transactionRequest:)` |
| `reversal` | `post(referenceId:)` |
| `status` | `get()` |
| `info` | `get()` |
| `restart` | `post()` |
| `sale` | `get()`, `post(request:)`, `patch(request:)`, `clear()` |
| `saleItem` | `post(item:)`, `patch(itemId:, item:)`, `delete(itemId:)`, `cancel(itemId:)`, `clear()` |
| `salePayment` | `post(payment:)`, `patch(paymentId:, payment:)`, `delete(paymentId:)`, `clear()` |
| `saleDiscount` | `post(discount:)`, `patch(discountId:, discount:)`, `delete(discountId:)`, `clear()` |
| `saleAddition` | `post(addition:)`, `patch(additionId:, addition:)`, `delete(additionId:)`, `clear()` |
| `saleFinalize` | `post()`, `post(params:)` |
| `saleCancel` | `post()`, `post(params:)` |
| `ask` | `post(questionRequest:)` |
| `askForm` | `post(form:)` |
| `askCancel` | `post()` |
| `displayImage` | `post(imageData:)` |
| `displayText` | `post(displayTextRequest:)` |
| `displayCarousel` | `post(displayCarouselRequest:)` |
| `displayClear` | `post()` |
| `displayPop` | `post()` |
| `printImage` | `post(imageData:)` |
| `printText` | `post(text:)` |
| `printXml` | `post(xml:)` |
| `log` | `getAll(level:, source:, dateFrom:, dateTo:, limit:, search:, includeDetails:)`, `stream()`, `downloadZip(level:, source:, dateFrom:, dateTo:, limit:)` |
| `notification` | `post(request:)` |

!!! info "Sem accessor para ACBr"
    O SDK Dart atual não expõe um método dedicado para `POST /print/acbr`. Para esse endpoint, use HTTP direto.

---

## Exemplos rápidos

### Venda

```dart
await TefIP.instance.sale.post(
  request: SaleStartRequestModel(
    customerName: 'João Silva',
    total: 99.90,
  ),
);

await TefIP.instance.saleItem.post(
  item: SaleItemModel(
    code: '123',
    description: 'Coca-Cola 2L',
    quantity: 1,
    unitPrice: 10.00,
  ),
);
```

### Ask

```dart
final answer = await TefIP.instance.ask.post(
  questionRequest: AskSingleQuestionRequestModel(
    question: AskQuestionModel(type: TefIPQuestionType.cpfOrcnpj),
    parameters: AskParametersModel(),
  ),
);

print(answer.value);
```

### Display

```dart
await TefIP.instance.displayText.post(
  displayTextRequest: DisplayTextRequestModel(
    content: [
      {'text': 'Aguardando operador'},
    ],
    backgroundColor: 'white',
    showCloseButton: false,
  ),
);
```

### Logs

```dart
final logs = await TefIP.instance.log.getAll(
  level: TefIPLogLevel.error,
  limit: 50,
);

TefIP.instance.log.stream().listen((log) {
  print('[${log.level.name}] ${log.message}');
});
```

### Notificações

```dart
await TefIP.instance.notification.post(
  request: NotificationRequestModel(
    title: 'Novo pagamento',
    message: 'Restaure o aplicativo para processar',
  ),
);
```

---

## Timeout

Por padrão, o SDK não define timeout global. Isso é útil para fluxos de pagamento em que o terminal pode aguardar interação do operador ou do cliente por tempo indeterminado.

Para definir um timeout global:

```dart
TefIP.requestsTimeOut = const Duration(minutes: 2);
```

Também é possível passar `timeout:` em chamadas que suportam override por requisição.

---

## Tratamento de exceções

Todo método do SDK pode lançar dois tipos de exceção:

```dart
try {
  final result = await TefIP.instance.transaction.post(
    transactionRequest: TransactionRequestModel(
      type: TefIPTransactionType.pix,
      amount: 50.00,
    ),
  );
  print(result.nsu);
} on TefIPRequestException catch (e) {
  print(e.statusCode);
  print(e.message);
  print(e.rawBody);
} on TefIPUnexpectedException catch (e) {
  print(e.exception);
}
```

| Exceção | Quando ocorre | Campos |
|---------|--------------|--------|
| `TefIPRequestException` | API retornou 4xx/5xx ou falha de conexão tratada | `statusCode`, `message`, `rawBody?` |
| `TefIPUnexpectedException` | Erro inesperado fora do fluxo HTTP padrão | `exception` |

---

## Referência de enums

### `TefIPTransactionType`

| Valor | `tPag` | Descrição |
|-------|--------|-----------|
| `TefIPTransactionType.money` | `"01"` | Dinheiro |
| `TefIPTransactionType.credit` | `"03"` | Crédito |
| `TefIPTransactionType.debit` | `"04"` | Débito |
| `TefIPTransactionType.pix` | `"17"` | PIX |
| `TefIPTransactionType.unknown` | `"99"` | Desconhecido |

### `TefIPInstallmentType`

| Valor | Descrição |
|-------|-----------|
| `TefIPInstallmentType.single` | À vista |
| `TefIPInstallmentType.seller` | Parcelado pelo lojista |
| `TefIPInstallmentType.buyer` | Parcelado pelo comprador |

### `TefIPTransactionStatus`

| Valor | Descrição |
|-------|-----------|
| `TefIPTransactionStatus.pending` | Pendente |
| `TefIPTransactionStatus.paid` | Pago |
| `TefIPTransactionStatus.cancelled` | Cancelado |
| `TefIPTransactionStatus.unknown` | Desconhecido |

### `TefIPSalePaymentType`

| Valor | Descrição |
|-------|-----------|
| `TefIPSalePaymentType.money` | Dinheiro |
| `TefIPSalePaymentType.credit` | Crédito |
| `TefIPSalePaymentType.debit` | Débito |
| `TefIPSalePaymentType.gift` | Cartão-presente |
| `TefIPSalePaymentType.pix` | PIX |
| `TefIPSalePaymentType.veroWallet` | Carteira digital Vero |
| `TefIPSalePaymentType.voucher` | Voucher |
| `TefIPSalePaymentType.adm` | Operação administrativa |
| `TefIPSalePaymentType.cancel` | Cancelamento de pagamento |
| `TefIPSalePaymentType.cancelDigitalWallet` | Cancelamento de carteira digital |
| `TefIPSalePaymentType.unknown` | Desconhecido |

### `TefIPQuestionType`

| Valor | Descrição |
|-------|-----------|
| `TefIPQuestionType.list` | Lista de opções |
| `TefIPQuestionType.button` | Botões de opção |
| `TefIPQuestionType.text` | Texto livre |
| `TefIPQuestionType.phone` | Telefone |
| `TefIPQuestionType.number` | Somente números |
| `TefIPQuestionType.cpf` | CPF |
| `TefIPQuestionType.cnpj` | CNPJ |
| `TefIPQuestionType.cpfOrcnpj` | CPF ou CNPJ |
| `TefIPQuestionType.email` | E-mail |
| `TefIPQuestionType.cep` | CEP |
| `TefIPQuestionType.date` | Data |
| `TefIPQuestionType.time` | Hora |
| `TefIPQuestionType.money` | Valor monetário |
| `TefIPQuestionType.regex` | Regex personalizada |

### `TefIPCarouselTransition`

| Valor | Descrição |
|-------|-----------|
| `TefIPCarouselTransition.fade` | Dissolve entre imagens |
| `TefIPCarouselTransition.slide` | Desliza entre imagens |
| `TefIPCarouselTransition.none` | Troca instantânea |
