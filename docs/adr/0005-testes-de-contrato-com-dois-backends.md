# ADR 0005: Testes de contrato contra PostgreSQL e um backend em memória

- **Status:** aceita
- **Data:** junho de 2026

## Contexto

A maior parte das regras de negócio do Ybá fica perto dos dados: transições de pedido, estoque, lançamentos da carteira. Testar só com mocks não provaria que o SQL está certo. Testar só contra um banco real deixa cada teste mais lento e mais difícil de rodar.

## Decisão

Cada store de dados tem duas implementações, uma em PostgreSQL e outra em memória. A mesma suíte de testes de contrato roda contra as duas. Os testes de serviço usam o backend em memória pela velocidade, e os testes de contrato garantem que ele se comporta exatamente como o banco real.

## Alternativas consideradas

- **Só testes com banco.** Corretos, mas com retorno lento para uma suíte de milhares de casos.
- **Só mocks.** Rápidos, mas deixariam passar SQL errado.

## Consequências

- Retorno rápido a cada mudança, com o SQL ainda verificado.
- Duas implementações precisam ficar em paridade. Qualquer mudança em um store precisa atualizar as duas, e a suíte de contrato falha se elas divergirem.
