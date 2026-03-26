# SDKs

Os SDKs do TEF IP encapsulam as chamadas HTTP com tipagem, modelos de dados e tratamento de erros — mas são **opcionais**. Qualquer cliente HTTP com Basic Auth integra diretamente à API.

---

## SDK ou HTTP direto?

| | SDK oficial | HTTP direto |
|---|---|---|
| Tipagem e modelos | Sim | Manual |
| Tratamento de erros padronizado | Sim | Manual |
| Dependência externa | Sim | Nenhuma |
| Linguagens disponíveis | Dart/Flutter (hoje) | Qualquer |

Use o SDK quando estiver desenvolvendo em uma linguagem com pacote disponível. Para outras linguagens, chame a API diretamente — todas as páginas de [API Reference](api/transaction.md) incluem exemplos prontos em JavaScript, PHP e Ruby.

---

## Disponíveis

| Linguagem | Pacote | Documentação |
|-----------|--------|--------------|
| Dart / Flutter | [`dart_tefip`](https://pub.dev/packages/dart_tefip) | [SDK Dart](sdk-dart.md) |

---

## Em desenvolvimento

| Linguagem | Status | Alternativa |
|-----------|--------|-------------|
| JavaScript | Em desenvolvimento | [Exemplos com `fetch`](api/transaction.md) |
| PHP | Em desenvolvimento | [Exemplos com `curl`](api/transaction.md) |
| Ruby | Em desenvolvimento | [Exemplos com `Net::HTTP`](api/transaction.md) |
