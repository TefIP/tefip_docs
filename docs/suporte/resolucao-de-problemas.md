# Resolução de Problemas

Use este guia para resolver as situações mais comuns encontradas durante a instalação ou operação do TEF IP no dia-a-dia.

---

## Checklist de Conectividade

Se o PDV não conseguir se comunicar com o TEF IP, verifique:

1.  **Rede**: O terminal de pagamento e o computador do PDV estão na mesma rede Wi-Fi/CABO?
2.  **IP**: O IP configurado no seu sistema PDV é o mesmo que aparece na tela do app TEF IP?
3.  **Basic Auth**: O usuário e a senha configurados no seu sistema estão corretos?
4.  **Firewall**: Se estiver usando Windows, a porta `9050` está aberta para conexões de entrada?

---

## Mensagens de Erro no Terminal

### "Terminal Busy" / "Ocupado"
*   **Causa**: Outra operação (pagamento ou impressão) está em andamento.
*   **Solução**: Aguarde o término da operação atual. Se o terminal parecer travado, use `POST /restart` ou reinicie o app manualmente.

### "App em Segundo Plano"
*   **Causa**: O operador minimizou o app TEF IP.
*   **Solução**: Abra o app TEF IP no terminal. Verifique se as notificações estão habilitadas para o app.

---

## Como usar o `/restart`

O endpoint `POST /restart` é uma ferramenta poderosa. Ele força a reinicialização dos serviços internos do servidor sem precisar fechar o app manualmente.

Use quando:
*   O terminal não responder a novos comandos de venda mesmo estando ocioso.
*   Houver erros persistentes de comunicação com a adquirente (Stone/Rede/Getnet).
*   Você alterar configurações críticas de rede no app.
