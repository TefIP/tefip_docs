# SDK Ruby

!!! info "Em desenvolvimento"
    O pacote Ruby para o TEF IP ainda não está disponível.

Enquanto isso, use a API diretamente via `Net::HTTP` — todas as páginas de [API Reference](api/transaction.md) incluem exemplos prontos em Ruby.

**Exemplo rápido:**

```ruby
require 'net/http'
require 'json'

uri = URI('http://localhost:9050/status')
req = Net::HTTP::Get.new(uri)
req.basic_auth('admin', '1234')
res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
data = JSON.parse(res.body)
```

Acompanhe as novidades na [página de SDKs](sdks.md).
