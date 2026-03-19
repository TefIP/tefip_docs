# Emulador

O TefIP inclui um **modo emulador** que simula o hardware do adquirente localmente — sem precisar de nenhum terminal físico. É a forma mais rápida de desenvolver e testar toda a integração com o TefIP.

!!! warning "Não usar em produção"
    O build com emulador é destinado exclusivamente a **desenvolvimento e testes**. Para uso em produção, utilize o app distribuído pelo seu adquirente no terminal Android correspondente (Stone, Getnet ou Rede).

Veja abaixo um pagamento sendo processado no emulador:

<p align="center">
  <img src="/assets/gif/emulador-terminal-pagamento-manual.gif" alt="GIF do terminal processando pagamento manual" />
</p>

---

## Download { #download }

Escolha a plataforma para desenvolvimento e testes:

| Plataforma | Arquivo | Observação |
|------------|---------|------------|
| **Windows 10+** | `TefIP-emulador-setup.exe` | Instalador com assistente; registra o TefIP como serviço do Windows |
| **Android (APK)** | `TefIP-emulador.apk` | Sideload manual; não disponível na Play Store |

!!! info "Onde baixar"
    Os arquivos de download são disponibilizados pelo canal TefIP. Entre em contato com o suporte para obter o link de download da versão mais recente.

---

## Instalação no Windows

1. Execute o instalador `TefIP-emulador-setup.exe`.
2. Siga o assistente de instalação (próximo → próximo → instalar).
3. Ao final, o TefIP é registrado como **serviço do Windows** e inicia automaticamente com o sistema.
4. Um ícone aparecerá na bandeja do sistema — clique nele para abrir o painel de controle.

<p align="center">
  <img src="/assets/gif/emulador-windows-instalacao.gif" alt="GIF de assistente de instalação Windows" />
</p>
---

## Instalação do APK Android

1. No dispositivo Android, acesse **Configurações → Segurança** e habilite **"Fontes desconhecidas"** (ou "Instalar apps desconhecidos").
2. Transfira o arquivo `TefIP-emulador.apk` para o dispositivo (via USB, e-mail ou link direto).
3. Toque no arquivo `.apk` para iniciar a instalação e confirme.
4. Abra o app **TefIP** após a instalação.

!!! tip "Dispositivo de testes"
    Qualquer smartphone ou tablet Android com Android 8.0+ funciona para desenvolvimento. Não é necessário nenhum hardware de adquirente.

---


## Próximos passos

- [Primeiros Passos](getting-started.md) — configure o servidor e faça a primeira requisição
- [Referência da API: Transações](api/transaction.md) — processe pagamentos e estornos
- [Referência da API: Status](api/status.md) — monitore e reinicie o servidor remotamente
