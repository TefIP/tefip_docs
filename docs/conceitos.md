# Conceitos

Esta página explica como o TEF IP processa requisições internamente — informação essencial para entender comportamentos como o bloqueio de operações simultâneas, notificações push automáticas e formatos de erro.

---

## Fluxo de uma requisição

Toda requisição HTTP feita ao TEF IP passa por uma cadeia de middlewares antes de chegar ao recurso (endpoint) correspondente:

```
Requisição HTTP
  → logRequests
  → authMiddleware
  → corsMiddleware
  → jsonMiddleware
  → busyMiddleware
  → notificationMiddleware
  → errorHandlerMiddleware
  → Recurso (ask | display | sale | print | status | transaction | swagger)
  → Resposta HTTP
```

| Middleware | O que faz |
|---|---|
| `logRequests` | Registra request e response no console do app |
| `authMiddleware` | Valida Basic Auth; rejeita com `401` se credenciais ausentes ou inválidas |
| `corsMiddleware` | Adiciona headers CORS a todas as respostas; responde `204` a requisições `OPTIONS` (preflight) |
| `jsonMiddleware` | Força `Content-Type: application/json` nas respostas (exceto respostas binárias) |
| `busyMiddleware` | Retorna `503` se o servidor estiver ocupado (flag `isBusy = true`) |
| `notificationMiddleware` | Envia push notification ao dispositivo para alertar o operador |
| `errorHandlerMiddleware` | Captura exceções e formata respostas de erro padronizadas |

---

## Rotas públicas

As rotas abaixo **não exigem autenticação** e **ignoram** `busyMiddleware` e `notificationMiddleware`:

| Rota | Descrição |
|------|-----------|
| `GET /` | Raiz do servidor |
| `GET /docs` | Swagger UI |
| `GET /openapi.bundle.yaml` | Spec OpenAPI dinâmica |

Todas as demais rotas exigem Basic Auth e passam pela cadeia completa.

---

## Flag `isBusy`

O TEF IP é um servidor de **operação única** — algumas operações bloqueiam o servidor enquanto estão em andamento.

**O que ativa `isBusy = true`:**

- `POST /transaction` (pagamento em processamento)
- `POST /transaction/{referenceId}/reversal` (estorno em processamento)
- `POST /print/image`, `POST /print/text`, `POST /print/xml` (impressão em andamento)

**O que acontece enquanto ocupado:**

Qualquer nova requisição que passe pelo `busyMiddleware` recebe:

```json
{ "code": 503, "message": "Aplicativo ocupado realizando outra operação, tente mais tarde!" }
```

**Rotas que ignoram o busy check** (além das públicas):

`GET /status`, `GET /info`, `POST /restart`

**Como verificar o estado:** consulte `isBusy` via `GET /info`.

---

## Flag `isActive`

Indica se o app TEF IP está em **primeiro plano** (foreground) no dispositivo.

- **`true`** — app visível na tela; pronto para processar transações
- **`false`** — app minimizado ou em segundo plano

**Impacto em transações:**

`POST /transaction` e `POST /transaction/{referenceId}/reversal` verificam esta flag antes de processar. Se o app estiver em segundo plano:

1. O servidor aguarda até **15 segundos** pelo app voltar ao foreground
2. Se o app voltar dentro do prazo, a transação é processada normalmente
3. Se não voltar, o servidor retorna `503`:

```json
{ "code": 503, "message": "Aplicativo em segundo plano. Abra o app para concluir o pagamento." }
```

**Como verificar o estado:** consulte `isActive` via `GET /info`.

---

## CORS

O servidor aceita requisições de qualquer origem. Não é necessária nenhuma configuração adicional para clientes web ou browser:

- **Origens permitidas:** `*` (todas)
- **Métodos permitidos:** `GET`, `POST`, `PUT`, `DELETE`, `OPTIONS`
- **Headers permitidos:** qualquer
- **Preflight (`OPTIONS`):** respondido com `204 No Content`

---

## Notificações push

Para cada requisição recebida em rotas não-públicas, o TEF IP envia automaticamente uma push notification ao dispositivo:

| Campo | Valor |
|-------|-------|
| Título | `TEF IP - Comando recebido` |
| Corpo | `Restaure o aplicativo` |

Isso garante que o operador seja alertado mesmo com o app minimizado — especialmente útil para que o app volte ao foreground e `isActive` se torne `true` antes de uma transação.

---

## Formatos de erro

Todos os erros retornam um objeto JSON com `code` e `message`:

| Situação | Status HTTP | Corpo da resposta |
|---|---|---|
| Credenciais ausentes ou inválidas | `401` | `{ "code": 401, "message": "..." }` |
| Payload inválido (JSON malformado) | `400` | `{ "code": 400, "message": "Requisição inválida: <detalhe>" }` |
| Servidor ocupado | `503` | `{ "code": 503, "message": "Aplicativo ocupado realizando outra operação, tente mais tarde!" }` |
| App em segundo plano (transação) | `503` | `{ "code": 503, "message": "Aplicativo em segundo plano. Abra o app para concluir o pagamento." }` |
| Erro de pagamento do adquirente | `403` | Estrutura variável por adquirente |
| Erro interno inesperado | `500` | `{ "code": 500, "message": "..." }` |
