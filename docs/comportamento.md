# Comportamento do Servidor

Esta página descreve como o TEF IP se comporta em situações que afetam diretamente a sua integração. Leia antes de implementar os endpoints — as decisões aqui (especialmente sobre `referenceId` e o comportamento de servidor ocupado) determinam se sua integração vai funcionar corretamente em produção.

!!! danger "Leitura obrigatória antes de integrar"
    Desenvolvedores que pulam esta página frequentemente descobrem em produção que não conseguem estornar transações (por falta de `referenceId`) ou não tratam corretamente o `503` de servidor ocupado.

---

## Fluxo de uma transação

Ao enviar um pagamento, o TEF IP repassa o comando ao terminal, que interage diretamente com o portador do cartão ou exibe o QR Code do PIX. A resposta do servidor só chega quando o pagamento é **aprovado, recusado ou cancelado** — o que pode levar vários segundos dependendo da rede e do adquirente.

Durante esse tempo o servidor fica bloqueado: nenhuma outra operação pode ser enviada até a transação ser concluída.

**Sequência:**

1. PDV envia `POST /transaction`
2. TEF IP repassa ao terminal do adquirente
3. Terminal processa (cliente insere cartão, digita senha, confirma PIX…)
4. TEF IP retorna a resposta ao PDV
5. PDV pode enviar a próxima operação

!!! tip "Tempo de resposta"
    Dimensione o timeout do seu cliente HTTP para pelo menos 60 segundos — transações com cartão físico dependem da interação do cliente no terminal.

---

## Identificação de transações

Para consultar ou estornar uma transação depois, você precisa de um **identificador** que ligue o seu sistema ao TEF IP. Esse identificador — chamado de `referenceId` — é informado pelo PDV no momento do pagamento.

- **Com `referenceId`:** você pode consultar a transação diretamente e solicitar estorno a qualquer momento.
- **Sem `referenceId`:** a transação aparece apenas na listagem geral e não pode ser estornada individualmente.

Use um identificador já existente no seu sistema — número do pedido, UUID da venda — qualquer valor único serve. O TEF IP armazena esse vínculo e o devolve na resposta.

---

## Fluxo de uma venda

Uma venda é uma **sessão aberta** que agrega itens e formas de pagamento antes de ser consolidada. Apenas uma venda pode estar ativa por vez — tentar abrir uma segunda enquanto há uma em aberto retorna `409`.

!!! info "Venda e transação são independentes"
    A venda registra **o quê** foi vendido e **como** foi pago — mas não processa o pagamento financeiro. O débito no cartão ou PIX é feito separadamente via `POST /transaction`.

Veja o [ciclo de vida completo e os endpoints de venda →](api/sale.md)

---

## Cancelamento de item vs. exclusão

Ao remover um item de uma venda em aberto, você tem duas opções com comportamentos distintos:

| Ação | Resultado |
|------|-----------|
| Excluir o item | Item removido permanentemente — não aparece no cupom/NF |
| Cancelar o item | Item mantido no histórico como cancelado — consta no cupom/NF com status cancelado |

Use o cancelamento quando o item precisar aparecer no documento fiscal mesmo sem ser cobrado. Use a exclusão quando o item foi adicionado por engano e não deve constar em nenhum documento.

---

## Coleta de dados no terminal

O TEF IP pode exibir perguntas no display do terminal (CPF, texto livre, listas, e-mail, CEP…) e coletar respostas sem que o PDV precise de tela própria. A conexão HTTP fica aberta até o usuário confirmar ou cancelar.

Veja os endpoints e tipos de campo disponíveis em [Perguntas (Ask) →](api/ask.md)

---

## Servidor ocupado

O TEF IP processa **uma operação por vez**. Enquanto um pagamento, estorno ou impressão está em andamento, o servidor recusa novas operações.

**Operações que bloqueiam o servidor:**

| Endpoint | Operação |
|----------|----------|
| `POST /transaction` | Pagamento em processamento |
| `POST /transaction/{referenceId}/reversal` | Estorno em processamento |
| `POST /print/image` | Impressão de imagem em andamento |
| `POST /print/text` | Impressão de texto em andamento |
| `POST /print/xml` | Impressão de XML em andamento |

**O que acontece ao enviar uma requisição durante esse período:**

```json
{
  "code": 503,
  "message": "Aplicativo ocupado realizando outra operação, tente mais tarde!"
}
```

**Como verificar se o servidor está livre:**

Use `GET /info` para consultar o estado atual. Se o servidor estiver ocupado, aguarde antes de enviar uma nova operação.

!!! tip "Endpoints sempre disponíveis"
    `GET /status`, `GET /info` e `POST /restart` respondem normalmente mesmo durante uma operação — use-os para monitorar o estado do servidor.

---

## App em segundo plano

Pagamentos e estornos exigem que o TEF IP esteja **visível na tela** do terminal. Se o operador minimizou o app, o servidor aguarda automaticamente até **15 segundos** pelo retorno ao primeiro plano.

**O que acontece:**

1. O PDV envia `POST /transaction` ou `POST /transaction/{referenceId}/reversal`.
2. O TEF IP detecta que está minimizado e aguarda até 15 s.
3. Se o app retornar ao primeiro plano dentro do prazo, a operação prossegue normalmente.
4. Se não retornar, o servidor rejeita a requisição:

```json
{
  "code": 503,
  "message": "Aplicativo em segundo plano. Abra o app para concluir o pagamento."
}
```

!!! info "Notificação automática"
    Ao receber o comando, o TEF IP envia uma notificação ao operador pedindo para restaurar o aplicativo. Veja a seção [Notificações ao operador](#notificacoes-ao-operador) abaixo.

**Como verificar se o app está em primeiro plano:**

Use `GET /info` para consultar o estado atual do aplicativo.

---

## Notificações ao operador

Sempre que o TEF IP recebe um comando do PDV, ele exibe automaticamente uma notificação no terminal alertando o operador para restaurar o aplicativo.

Isso é especialmente útil quando o operador minimizou o TEF IP — a notificação serve como aviso para que o app volte ao primeiro plano antes do tempo limite de 15 segundos expirar.

Nenhuma configuração é necessária — o comportamento é automático.

---

## Endpoints sem autenticação

A maioria dos endpoints exige Basic Auth. As únicas exceções são:

| Endpoint | Descrição |
|----------|-----------|
| `GET /docs` | Swagger UI |
| `GET /openapi.bundle.yaml` | Especificação OpenAPI |

Todos os demais endpoints exigem credenciais válidas. Requisições sem autenticação ou com credenciais inválidas recebem `401`.

---

## CORS

O TEF IP aceita requisições de qualquer origem. Nenhuma configuração adicional é necessária para clientes web ou browser:

- **Origens permitidas:** todas
- **Métodos permitidos:** `GET`, `POST`, `PUT`, `DELETE`, `OPTIONS`
- **Headers permitidos:** qualquer
- **Preflight (`OPTIONS`):** respondido com `204 No Content`

---

## Respostas de erro

Todos os erros retornam `{ "code": <status>, "message": "..." }`. Veja a [Visão Geral de Erros →](api/erros.md) para a referência completa de códigos HTTP e sugestões de tratamento.
