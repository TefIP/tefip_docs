# SDK PHP

!!! info "Em desenvolvimento"
    O pacote PHP para o TEF IP ainda não está disponível.

Enquanto isso, use a API diretamente via `curl` — todas as páginas de [API Reference](api/transaction.md) incluem exemplos prontos em PHP.

**Exemplo rápido:**

```php
<?php
$ch = curl_init('http://localhost:9050/status');
curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$response = json_decode(curl_exec($ch), true);
curl_close($ch);
```

Acompanhe as novidades na [página de SDKs](sdks.md).
