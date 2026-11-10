# ADR 0003: Alinhar a validade do PIX ao mínimo de 30 minutos do provedor

- **Status:** aceita
- **Data:** junho de 2026

## Contexto

O primeiro desenho dava ao comprador 10 minutos para pagar o QR do PIX, e depois disso o pedido expirava. O provedor de pagamento, porém, impõe uma validade mínima de 30 minutos ao próprio QR. O comprador podia então pagar entre o minuto 10 e o minuto 30, com o pedido já expirado: um pagamento órfão que precisaria de reembolso.

## Decisão

Definir a janela de pagamento do pedido em 30 minutos e enviar a mesma validade ao provedor. O QR e o pedido expiram no mesmo instante, então se torna impossível pagar um pedido expirado. A janela vem de um único valor de configuração, e o app mostra uma contagem regressiva ao vivo.

Pedidos não pagos também ficam ocultos para o feirante até a confirmação do pagamento, para que ele veja apenas pedidos reais.

## Alternativas consideradas

- **Manter 10 minutos e construir reembolsos automáticos para pagamentos atrasados.** Avaliada e descartada: mais código, mais estados e uma experiência pior (o comprador paga e depois recebe o dinheiro de volta) para resolver um problema que o alinhamento elimina.

## Consequências

- Não é preciso um fluxo de reembolso para pagamentos PIX atrasados.
- O comprador tem uma janela maior do que a planejada no início. A contagem regressiva mantém a urgência visível.
