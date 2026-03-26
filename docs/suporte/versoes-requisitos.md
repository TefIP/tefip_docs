# Versões e Requisitos

O TEF IP é distribuído em versões específicas para cada adquirente. Cada versão é otimizada para o hardware do adquirente correspondente. Escolher a versão correta é essencial para que a integração com o terminal funcione.

---

## Versões Disponíveis

| Adquirente | Hardware Alvo | Dependências Externas |
|------------|---------------|-----------------------|
| Stone | SmartPOS | App "Stone SDK" |
| Rede | SmartPOS | App "Pagamento Rede" |
| Getnet | SmartPOS | App "Global Payments" |
| Emulador | Windows / Android | Nenhuma (perfeito para testes) |

---

## Requisitos de Instalação

### No Terminal Adquirente (SmartPOS)

1. **Conectividade**: O terminal deve estar na mesma rede Wi-Fi que o PDV.
2. **IP Estático**: Recomenda-se configurar um IP fixo para o terminal no roteador para evitar que o PDV perca a conexão.
3. **Apps de Apoio**: Garanta que as dependências externas listadas na tabela acima estejam instaladas e atualizadas.
4. **Permissões**: Ao abrir o TEF IP pela primeira vez, aceite todas as permissões de rede, telefone e armazenamento.

### No Windows (Emulador)

1. **Porta 9050**: Certifique-se de que a porta `9050` está aberta no Firewall do Windows para conexões de entrada.
2. **Basic Auth**: Configure o usuário e senha desejados no arquivo de configurações do app.
3. **Visual C++ Redistributable**: Pode ser necessário para a execução do app em algumas versões do Windows.

---

## Como Identificar a Versão

Ao abrir o aplicativo TEF IP, a versão e o adquirente configurado geralmente constam na tela "Sobre" ou no rodapé da tela inicial. Certifique-se de que o logotipo do adquirente no app corresponde ao terminal físico que você possui.
