# Códigos de Erro

O TEF IP utiliza códigos de status HTTP padrão para indicar o sucesso ou falha de uma requisição. Todas as respostas de erro acompanham um corpo JSON com detalhes adicionais.

## Formato de Erro

```json
{
  "code": 400,
  "message": "Mensagem descritiva do erro"
}
```

---

## Tabela de Referência

| Código | Nome | Significado para o Integrador |
|--------|------|-------------------------------|
| `200` | OK | A operação foi processada com sucesso. |
| `204` | No Content | Sucesso, mas não há conteúdo de retorno (ex.: respostas de CORS). |
| `400` | Bad Request | O corpo da requisição (JSON/XML/Binário) é inválido ou faltam campos obrigatórios. |
| `401` | Unauthorized | As credenciais de Basic Auth estão ausentes ou incorretas. |
| `403` | Forbidden | A operação foi recusada pela adquirente ou as permissões são insuficientes. |
| `409` | Conflict | Você tentou iniciar uma operação que conflita com o estado atual (ex.: iniciar venda com outra aberta). |
| `500` | Internal Error | Ocorreu um erro inesperado no servidor. Verifique os logs do dispositivo. |
| `503` | Service Unavailable | O servidor está ocupado (`isBusy`) ou o aplicativo está em segundo plano (`isActive = false`). |

---

## Sugestões de Tratamento

*   **Tratamento de 503 (Ocupado):** O PDV deve aguardar alguns segundos e tentar novamente (retry). Se o erro persistir por mais de 15 segundos com a mensagem de "Segundo Plano", avise o operador para abrir o app TEF IP na tela do terminal.
*   **Tratamento de 409 (Conflito):** Se receber 409 ao iniciar uma venda, o seu sistema deve perguntar ao operador se deseja "Recuperar" a venda em aberto ou "Cancelar" a anterior antes de prosseguir.
*   **Tratamento de 401 (Não Autorizado):** Verifique as configurações de usuário e senha no seu sistema e no terminal. Geralmente o padrão é `admin` / senha configurada.
