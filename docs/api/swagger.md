# Swagger / OpenAPI

O TEF IP expõe uma interface Swagger UI interativa e o spec OpenAPI diretamente no terminal — sem necessidade de nenhuma ferramenta externa.

!!! info "Rotas públicas"
    Os endpoints `/docs` e `/openapi.bundle.yaml` **não exigem autenticação** e podem ser acessados diretamente no navegador.

---

## GET /docs

Abre a interface Swagger UI no navegador. Permite explorar e testar todos os endpoints da API de forma interativa.

O spec é carregado automaticamente e os servidores disponíveis são detectados e listados em tempo real com base nas instâncias ativas do TEF IP na rede.

**Como acessar:**

Abra o navegador e acesse `http://<ip-do-terminal>:9050/docs`.

![TODO: adicionar screenshot da Swagger UI do TEF IP](../assets/screenshot-swagger-ui.png)

> **Ação necessária:** adicione o arquivo `docs/assets/screenshot-swagger-ui.png` aqui com um screenshot da interface Swagger UI em funcionamento.

---

## GET /openapi.bundle.yaml

Retorna o spec OpenAPI completo em formato YAML. O spec inclui a lista de servidores detectados dinamicamente, incluindo o status de cada instância (Online/Offline).

**Resposta — 200**

```yaml
openapi: 3.0.0
info:
  title: TEF IP API
  version: 1.0.0
# ...
servers:
  - url: http://192.168.1.100:9050
    description: Online
  - url: http://192.168.1.101:9050
    description: Offline
```

O arquivo pode ser importado em qualquer ferramenta compatível com OpenAPI (Postman, Insomnia, etc.).

### Como importar no Postman

1. Abra o Postman e clique em **Import**.
2. Selecione **Link** e cole: `http://<ip-do-terminal>:9050/openapi.bundle.yaml`
3. Clique em **Continue** e confirme a importação.

### Como importar no Insomnia

1. Abra o Insomnia e clique em **Create** → **Import from URL**.
2. Cole: `http://<ip-do-terminal>:9050/openapi.bundle.yaml`
3. Confirme a importação.
