# ADR 0001: Migrar API e banco para o Lightsail por custo mensal fixo

- **Status:** aceita
- **Data:** julho de 2026

## Contexto

A primeira versão da infraestrutura rodava a API no ECS Fargate, atrás de um Application Load Balancer e do CloudFront, com PostgreSQL no RDS dentro de uma VPC privada. Era uma base sólida, mas o custo mensal era variável e ficava acima do valor fixo que o cliente aprovou para o piloto.

## Decisão

Migrar computação e banco para um serviço de containers do Lightsail e um PostgreSQL gerenciado do Lightsail. Todo o resto (Cognito, S3, CloudFront para imagens, SQS, e-mail, observabilidade, alertas de orçamento) continua no CDK.

- Os recursos do Lightsail são gerenciados por scripts versionados e separados por ambiente (criação, associação de domínio, deploy), porque o Lightsail não tem suporte de primeira classe no CDK.
- O container não pode assumir uma role do IAM, então a API usa um usuário IAM dedicado, criado no CDK, com a chave guardada no Secrets Manager e injetada no deploy.
- A VPC, os security groups, o load balancer e o serviço Fargate foram removidos do CDK.
- O banco foi recriado a partir das migrations, sem dump, porque ainda não existiam dados reais.

## Alternativas consideradas

- **Manter Fargate e RDS.** É o melhor caminho para escala, mas o custo não cabia no orçamento aprovado para um piloto.
- **Migrar só o banco.** O load balancer e o Fargate continuariam sendo o maior custo fixo.

## Consequências

- O custo de infraestrutura ficou fixo, em torno de R$ 170 por mês.
- Parte da infraestrutura agora vive em scripts, não no CDK. Os scripts são versionados, parametrizados por ambiente e falham quando falta o arquivo do ambiente. Um arquivo compartilhado anterior teria deixado a produção subir em modo demonstração.
- Se o produto crescer além do piloto, voltar para Fargate e RDS é um caminho conhecido: a API é um container sem estado e o schema está inteiramente descrito nas migrations.
