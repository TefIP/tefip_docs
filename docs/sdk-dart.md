# SDK Dart

O [`dart_tefip`](https://pub.dev/packages/dart_tefip) é o SDK oficial para Dart e Flutter. Ele encapsula todas as chamadas HTTP ao TEF IP com tipagem forte, modelos de request/response e tratamento de erros.

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

O SDK usa o padrão **singleton** com setters estáticos. Configure uma única vez antes da primeira chamada — o local recomendado é o `main()` ou a inicialização do app:

```dart
import 'package:dart_tefip/dart_tefip.dart';

TefIP.baseUrl  = 'http://192.168.1.10:9050'; // IP do terminal na rede local
TefIP.username = 'admin';
TefIP.password = 'minha-senha';
```

Para desenvolvimento local com o emulador:

```dart
TefIP.baseUrl = 'http://localhost:9050';
```

!!! warning "TefIPClient não existe"
    Não existe nenhuma classe `TefIPClient` no SDK. Use sempre `TefIP.instance` para chamar os endpoints.

---

## Chamando endpoints

Todos os endpoints estão disponíveis via `TefIP.instance.<grupo>.<método>(...)`:

```dart
// Processar um pagamento PIX de R$ 50,00
final result = await TefIP.instance.transaction.post(
  transactionRequest: TransactionRequestModel(
    type: TefIPTransactionType.pix,
    amount: 50.00,
    referenceId: 'pedido-001',
  ),
);
print(result.nsu);
```

---

## Timeout

Por padrão o SDK **não define timeout** — comportamento adequado para transações de pagamento, que podem aguardar o cliente interagir com o terminal por tempo indeterminado.

Para definir um timeout global:

```dart
TefIP.requestsTimeOut = const Duration(minutes: 2);
```

!!! tip
    Defina o timeout junto com as demais configurações, antes da primeira chamada.

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
  // Erro HTTP retornado pela API (4xx / 5xx)
  print(e.statusCode); // ex.: 503
  print(e.message);    // mensagem de erro
  print(e.rawBody);    // corpo bruto da resposta (nullable)
} on TefIPUnexpectedException catch (e) {
  // Erro de rede, timeout ou exceção não tratada
  print(e.exception);
}
```

| Exceção | Quando ocorre | Campos |
|---------|--------------|--------|
| `TefIPRequestException` | API retornou 4xx ou 5xx | `statusCode`, `message`, `rawBody?` |
| `TefIPUnexpectedException` | Sem conexão, timeout, erro interno | `exception` |

---

## Referência de enums

### `TefIPTransactionType` — tipo de pagamento (`/transaction`)

| Valor | `tPag` | Descrição |
|-------|--------|-----------|
| `TefIPTransactionType.credit` | `"03"` | Crédito |
| `TefIPTransactionType.debit` | `"04"` | Débito |
| `TefIPTransactionType.pix` | `"17"` | PIX |
| `TefIPTransactionType.unknown` | `"99"` | Desconhecido |

### `TefIPInstallmentType` — modalidade de parcelamento (`/transaction`)

| Valor | Descrição |
|-------|-----------|
| `TefIPInstallmentType.single` | À vista (padrão) |
| `TefIPInstallmentType.seller` | Parcelado pelo lojista (sem juros ao comprador) |
| `TefIPInstallmentType.buyer` | Parcelado pelo comprador (com juros) |

### `TefIPTransactionStatus` — status de transação

| Valor | Descrição |
|-------|-----------|
| `TefIPTransactionStatus.pending` | Pendente |
| `TefIPTransactionStatus.paid` | Pago |
| `TefIPTransactionStatus.cancelled` | Cancelado |
| `TefIPTransactionStatus.unknown` | Desconhecido |

### `TefIPSalePaymentType` — tipo de pagamento (`/sale/payment`)

| Valor | Descrição |
|-------|-----------|
| `TefIPSalePaymentType.credit` | Crédito |
| `TefIPSalePaymentType.debit` | Débito |
| `TefIPSalePaymentType.pix` | PIX |
| `TefIPSalePaymentType.money` | Dinheiro |
| `TefIPSalePaymentType.voucher` | Voucher / ticket alimentação |
| `TefIPSalePaymentType.gift` | Cartão-presente |
| `TefIPSalePaymentType.veroWallet` | Carteira digital Vero |
| `TefIPSalePaymentType.adm` | Operação administrativa |
| `TefIPSalePaymentType.cancel` | Cancelamento de pagamento |
| `TefIPSalePaymentType.cancelDigitalWallet` | Cancelamento de carteira digital |
| `TefIPSalePaymentType.unknown` | Desconhecido |

### `TefIPQuestionType` — tipo de campo (`/ask`)

| Valor | Descrição |
|-------|-----------|
| `TefIPQuestionType.text` | Texto livre |
| `TefIPQuestionType.number` | Somente números |
| `TefIPQuestionType.phone` | Telefone |
| `TefIPQuestionType.cpf` | CPF (com validação) |
| `TefIPQuestionType.cnpj` | CNPJ (com validação) |
| `TefIPQuestionType.cpfOrcnpj` | CPF ou CNPJ |
| `TefIPQuestionType.email` | E-mail (com validação) |
| `TefIPQuestionType.cep` | CEP (com validação) |
| `TefIPQuestionType.date` | Data |
| `TefIPQuestionType.time` | Hora |
| `TefIPQuestionType.money` | Valor monetário |
| `TefIPQuestionType.regex` | Validação por regex personalizado |
| `TefIPQuestionType.list` | Lista de opções (requer `options`) |
| `TefIPQuestionType.button` | Botões de opção (requer `options`) |

### `TefIPCarouselTransition` — transição do carrossel (`/display/carousel`)

| Valor | Duração | Descrição |
|-------|---------|-----------|
| `TefIPCarouselTransition.fade` | 200 ms | Dissolve entre imagens |
| `TefIPCarouselTransition.slide` | 200 ms | Desliza entre imagens |
| `TefIPCarouselTransition.none` | 0 ms | Troca instantânea |
