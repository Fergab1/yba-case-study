# ADR 0002: Carteira e saques em vez de split de marketplace

- **Status:** aceita
- **Data:** setembro de 2026

## Contexto

Os compradores pagam ao Ybá, e os feirantes precisam receber o que venderam. O app já tinha uma carteira do vendedor com extrato, saldo disponível, tarifas de saque e um registro da chave PIX de destino, mas os saques paravam em "solicitado" e alguém precisava pagá-los manualmente.

## Decisão

O Ybá continua sendo o intermediador financeiro. Os pagamentos dos compradores entram na conta da plataforma no Mercado Pago, e cada saque é pago por um worker em segundo plano, via Mercado Pago Payouts, para a chave PIX do feirante, com requisições assinadas.

## Alternativas consideradas

- **Split de marketplace.** Cada feirante cria uma conta no Mercado Pago e autoriza o Ybá por OAuth, de modo que o pagamento nasce na conta dele, com uma tarifa da plataforma. Rejeitada: elimina a carteira e o fluxo de saque, leva uma etapa de cadastro para fora do app, exige renovar o token de cada feirante a cada seis meses e muda a posição comercial e regulatória da empresa. Esse último ponto é uma decisão de negócio, não técnica.
- **Saques para conta bancária.** Rejeitada por enquanto: exige o código ISPB do banco e só aceita conta corrente, enquanto a chave PIX já cadastrada cobre todos os tipos de chave aceitos pelo provedor.

## Consequências

- A conta da plataforma precisa ter saldo suficiente para cobrir os saques. O dinheiro de pedidos não concluídos ainda não pode ser sacado.
- Para o piloto, o cliente preferiu pagar os saques manualmente. O caminho automático continua no código, atrás de uma configuração, e um fluxo de liberação manual envia um e-mail por saque com links assinados para uma página de confirmação. O backend se recusa a iniciar com os dois modos ligados.
- Se o cliente quiser deixar a posição de intermediador, o split passa a ser o caminho e esta decisão é substituída.
