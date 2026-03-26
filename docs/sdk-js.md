# SDK JavaScript

!!! info "Em desenvolvimento"
    O pacote JavaScript para o TEF IP ainda não está disponível.

Enquanto isso, use a API diretamente via `fetch` — todas as páginas de [API Reference](api/transaction.md) incluem exemplos prontos em JavaScript.

**Exemplo rápido:**

```js
const res = await fetch('http://localhost:9050/status', {
  headers: {
    'Authorization': 'Basic ' + btoa('admin:1234'),
  },
});
const data = await res.json();
```

Acompanhe as novidades na [página de SDKs](sdks.md).
