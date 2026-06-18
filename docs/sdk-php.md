# SDK PHP

Não existe pacote oficial PHP para o TEF IP neste momento.

Enquanto isso, a integração recomendada é chamar a API diretamente com `curl` ou outro cliente HTTP, usando Basic Auth.

```php
<?php
$ch = curl_init('http://localhost:9050/status');
curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$response = json_decode(curl_exec($ch), true);
curl_close($ch);
```

Os exemplos completos por endpoint estão nas páginas de [API Reference](api/transaction.md). O panorama geral fica em [SDKs](sdks.md).
