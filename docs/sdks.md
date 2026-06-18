# SDKs

Os SDKs do TEF IP são opcionais. A API continua sendo HTTP puro com Basic Auth, então qualquer linguagem pode integrar diretamente mesmo sem pacote dedicado.

---

## Panorama atual

| Linguagem | Pacote oficial | Status | Documentação |
|-----------|----------------|--------|--------------|
| Dart / Flutter | [`dart_tefip`](https://pub.dev/packages/dart_tefip) | Disponível | [SDK Dart](sdk-dart.md) |
| JavaScript | Não | Sem pacote oficial no momento | [SDK JavaScript](sdk-js.md) |
| PHP | Não | Sem pacote oficial no momento | [SDK PHP](sdk-php.md) |
| Ruby | Não | Sem pacote oficial no momento | [SDK Ruby](sdk-ruby.md) |

---

## Quando usar SDK

| Cenário | Recomendação |
|---------|--------------|
| App Flutter ou Dart | Use o `dart_tefip` |
| Backend ou PDV em outra linguagem | Use HTTP direto |
| Integração rápida ou prova de conceito | Pode começar por cURL/fetch/curl/Net::HTTP |

Todas as páginas de [API Reference](api/transaction.md) incluem exemplos prontos em `cURL`, `Dart`, `JavaScript`, `PHP` e `Ruby`.
