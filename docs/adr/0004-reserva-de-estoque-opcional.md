# ADR 0004: Reserva de estoque opcional

- **Status:** aceita
- **Data:** julho de 2026

## Contexto

Dois compradores podiam fechar o pedido da última unidade do mesmo produto ao mesmo tempo, e os dois pedidos seriam aceitos. Muitos feirantes, porém, não contam estoque: vendem o que levaram até acabar.

## Decisão

O controle de estoque é opcional, por produto. Um produto passa a ter estoque controlado na primeira vez que o feirante informa uma quantidade. Produtos que nunca foram contados são tratados como ilimitados.

Para produtos com estoque controlado, o pedido segue invariantes rígidas:

- **Reservar exatamente uma vez** no checkout, tudo ou nada, dentro da transação do checkout. Se qualquer item controlado não tiver estoque, o checkout inteiro é desfeito.
- **Depois, exatamente um efeito final por pedido:** baixar quando o pedido é concluído, ou liberar quando ele morre (expirado, cancelado, recusado, cartão negado). Nunca os dois, nunca nenhum, nunca duas vezes.
- Toda escrita de estoque é agregada por produto e acontece no mesmo comando da mudança de status do pedido, condicionada a essa mudança realmente acontecer.
- Reaproveitar um pedido não pago com o carrinho alterado libera a reserva antiga antes de reservar a nova.

## Alternativas consideradas

- **Controlar o estoque de todo produto.** Obriga trabalho a quem não conta estoque e bloquearia a venda de produtos com quantidade desconhecida.
- **Não controlar estoque.** Simples, mas vender além do estoque prejudica comprador e feirante.

## Consequências

- A venda acima do estoque deixa de ser possível para produtos controlados.
- Há mais transições para testar. Cada forma de um pedido morrer tem o próprio teste de liberação.
