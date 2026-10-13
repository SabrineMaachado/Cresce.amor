# Estudo: fornecedores de pagamento e de etiquetas para o MVP

> Outubro de 2026. Feito a partir da documentação pública dos fornecedores e de comparativos de terceiros. As taxas variam muito entre fontes e mudam com frequência: **confirmar tudo em conversa comercial antes de contratar**.

## O que o Cresce, Amor precisa

| Necessidade | Por quê |
|---|---|
| Vendedor pessoa física, sem obrigar a abrir conta em outro lugar | Público de famílias, cadastro tem que ser simples. |
| Divisão automática do pagamento (plataforma × vendedor) | Comissão descontada sem trabalho manual. |
| Retenção do valor até a entrega ou um prazo, com liberação pela plataforma | É o coração da compra protegida (D2). |
| Cartão de crédito, Pix e boleto | Decisão de 13/10/2026. |
| Reembolso pela plataforma | Cancelamentos e disputas. |
| API bem documentada | O produto vai ser desenvolvido sob medida. |

## 1. Pagamento

| Critério | **Asaas** | Pagar.me | Mercado Pago |
|---|---|---|---|
| Vendedor pessoa física | Subconta criada pela plataforma via API | "Recebedor" pessoa física, liquidado direto na conta bancária | Vendedor precisa ter **conta Mercado Pago** verificada e autorizar a plataforma |
| Divisão automática | Sim | Sim, com regras detalhadas (quem paga a tarifa, quem responde por estorno) | Sim, mas no modelo simples só 1 vendedor por pagamento |
| Retenção até liberar | **Sim, nativa: Conta Escrow** — segura o valor da subconta por um prazo (`daysToExpire`) e permite liberar antes pela plataforma | Não encontrei recurso equivalente documentado; seria preciso controlar pela agenda de recebimentos | Prazo de liberação ajustável só com o comercial |
| Cartão, Pix, boleto | Sim | Sim (Pix com divisão) | Sim |
| Ponto de atenção | Escrow tem **custo mensal por subconta** habilitada; a tarifa do meio de pagamento é descontada antes da divisão | Mais robusto e usado por grandes marketplaces, mas a retenção por evento exige mais desenvolvimento | Fricção: cada família precisa ter ou criar conta no Mercado Pago |

**Recomendação: Asaas.** É o único dos três com retenção pronta exatamente no formato do nosso D2 (segurar até a entrega, liberar automaticamente após o prazo ou antes pela plataforma), e cria a conta do vendedor sem tirá-lo do nosso site. Pagar.me fica como alternativa caso o Asaas não aceite o modelo ou o custo do escrow inviabilize.

**Perguntas para a conversa comercial com o Asaas:**
1. Custo mensal da Conta Escrow por subconta: dá para cobrar só de subcontas com venda no mês, ou ativar o escrow só quando houver venda?
2. Tarifas de cartão (à vista e parcelado), Pix e boleto para o nosso volume inicial.
3. Como o escrow funciona com **cartão**, cujo valor só fica disponível depois de cerca de 30 dias: a liberação ao vendedor segue esse prazo ou pode ser antecipada (e a que custo)?
4. Exigências de cadastro (verificação de identidade) para subconta pessoa física e prazo de aprovação.
5. Como ficam estornos de cartão (contestação do comprador) dentro do escrow.
6. Limites de volume para subcontas pessoa física.

## 2. Etiquetas e frete

| Critério | **Melhor Envio** | SuperFrete | Frenet |
|---|---|---|---|
| Modelo | Serviço de etiquetas com descontos sobre a tabela dos Correios e outras transportadoras | Serviço de etiquetas com foco em Correios | Calculadora de frete sobre **contratos próprios** do lojista; não emite etiqueta com desconto próprio |
| Mensalidade | Sem mensalidade, paga por etiqueta | Sem mensalidade, paga por etiqueta | Planos pagos a partir de cerca de R$ 67/mês |
| API | Documentação pública (cotação, carrinho, compra, etiqueta, rastreio) | Possui integrações; documentação de API menos citada | Sim |
| Pessoa física remetente | Aceita **declaração de conteúdo eletrônica** em vez de nota fiscal; CPF de remetente e destinatário obrigatório | Aceita | Depende do contrato |
| Ponto de atenção | Confirmar se a plataforma pode emitir etiquetas com **remetente terceiro** (o vendedor) pela conta dela | Mais forte no app e no checkout de lojas | Não serve: não temos contrato com os Correios |

Observação: a Kangu, outra opção conhecida, encerrou as operações no início de 2025.

**Recomendação: Melhor Envio**, por ter API pública e madura e aceitar declaração de conteúdo para pessoa física. SuperFrete como alternativa.

**Perguntas para o Melhor Envio:**
1. A conta da plataforma pode comprar etiquetas com o vendedor como remetente (CPF e endereço dele)?
2. Como funciona o saldo pré-pago da plataforma para comprar etiquetas e o reembolso de etiquetas não usadas (pedido cancelado).
3. Avisos automáticos de rastreio (postado, entregue) para a nossa API.
4. Limites de peso e tamanho dos serviços dos Correios disponíveis.

## Fontes

- [Asaas: Introdução à Conta Escrow](https://docs.asaas.com/docs/introducao-conta-escrow)
- [Asaas: Valores sob garantia da Conta Escrow](https://docs.asaas.com/docs/bloqueio-dos-valores)
- [Asaas: Configurando a Conta Escrow para subcontas](https://docs.asaas.com/docs/habilitando-a-conta-escrow-para-as-subcontas)
- [Asaas: Encerrar garantia da cobrança](https://docs.asaas.com/reference/encerrar-garantia-da-cobranca-na-conta-escrow)
- [Asaas: Split de pagamento](https://blog.asaas.com/split-de-pagamento/)
- [Pagar.me: Recebedores](https://docs.pagar.me/docs/recebedores-2)
- [Pagar.me: Split](https://docs.pagar.me/reference/split-1)
- [Pagar.me: Quem arca com as taxas no split](https://pagarme.helpjuice.com/pt_BR/p1-funcionalidades/marketplace-quem-arca-com-as-taxas-em-uma-regra-de-split)
- [Pagar.me: Pix](https://docs.pagar.me/docs/pix-1)
- [Mercado Pago: Split 1:1, pré-requisitos](https://www.mercadopago.com.br/developers/pt/docs/split-payments/split-1-1/prerequisites)
- [Mercado Pago: Integrar marketplace](https://www.mercadopago.com.br/developers/pt/docs/split-payments/split-1-1/integration-configuration/integrate-marketplace)
- [Mind Group: comparativo de gateways 2026](https://mindconsulting.com.br/2026/07/gateways-pagamento-online-brasil-comparativo-2026/)
- [FWC: Payment gateways in Brazil 2026](https://fwctecnologia.com/en/blog/post/payment-gateways-brazil-comparison-2026)
- [Melhor Envio: Como preencher os dados da etiqueta](https://centraldeajuda.melhorenvio.com.br/hc/pt-br/articles/31220428174356-Como-preencher-os-dados-da-etiqueta-de-frete)
- [Melhor Envio: Regras de embarque dos Correios](https://centraldeajuda.melhorenvio.com.br/hc/pt-br/articles/31220449136660-Como-funcionam-os-Correios-pelo-Melhor-Envio-Regras-de-embarque)
- [Melhor Envio: API, inserir fretes no carrinho](https://docs.melhorenvio.com.br/reference/inserir-fretes-no-carrinho)
- [Ruacep: Melhor Envio, SuperFrete ou Frenet](https://www.ruacep.com.br/guias/compras/comparativo-plataformas-frete-melhor-envio-superfrete-frenet/)
- [SmartEnvios: plataformas de frete 2026](https://smartenvios.com/plataformas-de-frete-para-e-commerce/)
- [Melhor Envio: plataformas de frete](https://melhorenvio.com.br/blog/frete-e-logistica/plataformas-de-frete/)
