# SDK JavaScript

Não existe pacote oficial JavaScript para o TEF IP neste momento.

Enquanto isso, a integração recomendada é chamar a API diretamente com `fetch` ou qualquer cliente HTTP equivalente, usando Basic Auth.

```js
const res = await fetch('http://localhost:9050/status', {
  headers: {
    'Authorization': 'Basic ' + btoa('admin:1234'),
  },
});

const data = await res.json();
```

Os exemplos completos por endpoint estão nas páginas de [API Reference](api/transaction.md). O panorama geral fica em [SDKs](sdks.md).
