# Primeiros Passos

Este guia leva você da instalação até a primeira resposta real do terminal de pagamento. Ao final, você terá o servidor rodando e confirmará que ele responde às suas requisições — pronto para começar a integrar os endpoints de pagamento.

## Pré-requisitos

- **Hardware:** terminal Android compatível (Stone, Getnet ou Rede) **ou** computador com Windows 10 ou superior
- **Rede local:** o PDV e o terminal devem estar na mesma rede (ou usar `localhost` quando o TEF IP rodar na mesma máquina)

## Instalação

### Terminais Android (Adquirentes)

A instalação em terminais físicos é feita diretamente pela loja ou canal de distribuição do seu adquirente — o processo varia para cada um:

- **Stone:** distribuído pela Stone via portal de parceiros.
- **Getnet:** distribuído pela Getnet via canal de desenvolvedores.
- **Rede:** distribuído pela Rede via canal de parceiros.

Caso encontre alguma dificuldade, entre em contato com o nosso [SUPORTE](https://wa.me/551533243333?text=Ol%C3%A1%2C+tenho+interesse+em+come%C3%A7ar+a+usar+o+TEF+IP+e+preciso+de+mais+informa%C3%A7%C3%B5es.).

### Windows *(Emulador — desenvolvimento/testes)*

1. Baixe o instalador `.exe` na [página do Emulador](emulator.md#download).
2. Execute o instalador e siga o assistente de instalação.
3. Ao final, o TEF IP será registrado como serviço do Windows e iniciará automaticamente.

!!! info "Build com emulador"
    O instalador Windows disponível nas releases inclui o emulador de hardware — ideal para desenvolvimento e testes sem terminal físico. Para uso em produção, utilize o app distribuído pelo seu adquirente no terminal Android correspondente.

### Emulador (APK Android) *(Emulador — desenvolvimento/testes)*

O APK Android disponível na [página do Emulador](emulator.md#download) é o build com emulador de hardware — destinado a **desenvolvimento e testes** sem terminal físico.

!!! warning "Não usar em produção"
    Este APK **não deve ser instalado em terminais físicos de produção** (Stone, Getnet, Rede). Para terminais físicos, obtenha o app pelo canal do seu adquirente conforme descrito acima.


## Iniciando o servidor

Se for o primeiro acesso, a inicialização automática já estará ativa.

Para verificar os servidores ativos:

1. Abra o aplicativo TEF IP no terminal.
2. Acesse a seção **Servidores** na tela inicial.
3. Se não aparecer na tela inicial, abra o menu → **Configurações → Servidores**.

Nessa tela é possível visualizar os IPs detectados e executar ações como reiniciar ou parar os serviços.

![GIF do TEF IP entrando nos servidores ativos](assets/gif/emulador-terminal-entrar-servidores.gif){ style="display: block; margin: 0 auto;" }

## Verificando a conexão

!!! warning "Autenticação"
    Todas as requisições exigem Basic Auth. Use as credenciais configuradas no TEF IP (`admin` / senha definida na instalação). Os únicos endpoints sem autenticação são `/docs` e `/openapi.bundle.yaml`.

Use o endpoint `GET /status` para confirmar que o servidor está respondendo:

=== "cURL"

    ```bash
    curl -u admin:1234 \
         http://localhost:9050/status
    ```

=== "Dart"

    ```dart
    import 'package:dart_tefip/dart_tefip.dart';

    TefIP.baseUrl = 'http://localhost:9050';
    TefIP.username = 'admin';
    TefIP.password = '1234';

    final status = await TefIP.instance.status.get();
    print(status);
    ```

=== "JavaScript"

    ```js
    // TODO: pacote JavaScript ainda não criado — usando fetch diretamente
    const res = await fetch('http://localhost:9050/status', {
      headers: {
        'Authorization': 'Basic ' + btoa('admin:1234'),
      },
    });
    const data = await res.json();
    console.log(data);
    ```

=== "PHP"

    ```php
    <?php
    // TODO: pacote PHP ainda não criado — usando curl diretamente
    $ch = curl_init('http://localhost:9050/status');
    curl_setopt($ch, CURLOPT_USERPWD, 'admin:1234');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = json_decode(curl_exec($ch), true);
    curl_close($ch);
    print_r($response);
    ```

=== "Ruby"

    ```ruby
    # TODO: pacote Ruby ainda não criado — usando Net::HTTP diretamente
    require 'net/http'
    require 'json'

    uri = URI('http://localhost:9050/status')
    req = Net::HTTP::Get.new(uri)
    req.basic_auth('admin', '1234')
    res = Net::HTTP.start(uri.hostname, uri.port) { |h| h.request(req) }
    data = JSON.parse(res.body)
    puts data
    ```

**Resposta esperada:**

```json
{
  "status": "ok",
  "uptimeSeconds": 144,
  "startedAt": "2026-01-28T16:20:53.223883"
}
```

## Próximos passos

Com o servidor respondendo, o caminho natural é:

1. **[Comportamento](comportamento.md)** — entenda como o servidor lida com operações simultâneas, app em segundo plano e autenticação antes de integrar os endpoints.
2. **[Transações](api/transaction.md)** — processe pagamentos e estornos.
3. **[Vendas](api/sale.md)** — monte carrinho com itens e múltiplas formas de pagamento.
4. **[Swagger UI](api/swagger.md)** — explore e teste todos os endpoints diretamente no terminal.
