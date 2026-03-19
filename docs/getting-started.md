# Primeiros Passos

Este guia mostra como instalar o TefIP, configurar o servidor e fazer a primeira requisição ao terminal de pagamento — tudo em menos de 10 minutos.

## Pré-requisitos

- **Hardware:** terminal Android compatível (Stone, Getnet ou Rede) **ou** computador com Windows 10 ou superior
- **Rede local:** o PDV e o terminal devem estar na mesma rede (ou usar `localhost` quando o TefIP rodar na mesma máquina)

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
3. Ao final, o TefIP será registrado como serviço do Windows e iniciará automaticamente.

!!! info "Build com emulador"
    O instalador Windows disponível nas releases inclui o emulador de hardware — ideal para desenvolvimento e testes sem terminal físico. Para uso em produção, utilize o app distribuído pelo seu adquirente no terminal Android correspondente.

### Emulador (APK Android) *(Emulador — desenvolvimento/testes)*

O APK Android disponível na [página do Emulador](emulator.md#download) é o build com emulador de hardware — destinado a **desenvolvimento e testes** sem terminal físico.

!!! warning "Não usar em produção"
    Este APK **não deve ser instalado em terminais físicos de produção** (Stone, Getnet, Rede). Para terminais físicos, obtenha o app pelo canal do seu adquirente conforme descrito acima.


## Iniciando o servidor
Se for o primeiro acesso, a inicialização automática já estará ativa.

1. Para verificar os servidores:

2. Abra o aplicativo TefIP no terminal.

3. Acesse a seção Servidores.

4. Se não estiver na tela inicial, abra o menu → Configurações → Servidores.

Nessa tela é possível visualizar os IPs detectados e executar ações como reiniciar ou parar os serviços.

<p align="center">
  <img src="/assets/gif/emulador-terminal-entrar-servidores.gif" alt="GIF do TefIP entrando nos servidores ativos" />
</p>

## Verificando a conexão

!!! warning "Autenticação"
    Recomendamos o uso de autenticação Basic Auth nos terminais.
    Se habilitada, todas as requisições devem incluir as credenciais configuradas.
  
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

- [Referência da API: Transações](api/transaction.md) — realize pagamentos e estornos
- [Referência da API: Venda](api/sale.md) — gerencie itens e pagamentos de uma venda
- [Swagger Docs](api/swagger.md) — Swagger UI interativo no próprio terminal
- [Outros produtos](http://djsystem.com.br) — Conheça outras soluções disponíveis
