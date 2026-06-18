# SDK Ruby

Não existe pacote oficial Ruby para o TEF IP neste momento.

Enquanto isso, a integração recomendada é chamar a API diretamente com `Net::HTTP` ou outro cliente HTTP, usando Basic Auth.

```ruby
require 'net/http'
require 'json'

uri = URI('http://localhost:9050/status')
req = Net::HTTP::Get.new(uri)
req.basic_auth('admin', '1234')
res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
data = JSON.parse(res.body)
```

Os exemplos completos por endpoint estão nas páginas de [API Reference](api/transaction.md). O panorama geral fica em [SDKs](sdks.md).
