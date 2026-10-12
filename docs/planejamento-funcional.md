# Cresce, Amor | Planejamento funcional do MVP

> Versão 0.3, outubro de 2026. Documento de trabalho: as recomendações marcadas como **[decidir]** precisam de confirmação antes de virar código.

## Decisões já tomadas

| Data | Decisão | Onde está detalhada |
|---|---|---|
| 12/10/2026 | Só **pessoas físicas** vendem. | P2, D1 |
| 12/10/2026 | Escopo ampliado: **todo o universo infantil de 0 a 6 anos** (roupas, calçados, brinquedos, livros, enxoval, acessórios e itens de passeio), não só roupas. | P3, D13 |
| 12/10/2026 | Frete pelos **Correios**, pago pelo comprador na hora da compra. **Frete grátis ou reduzido** é um **brinde** que cada vendedor pode oferecer na sua loja, por conta dele. | D4 |
| 12/10/2026 | Comissão por **nível de visibilidade**: Clássica (5%), Megafone (8%) e Outdoor (10%). As **3 primeiras vendas** de cada vendedor não pagam comissão (boas-vindas). | D12 |
| 12/10/2026 | **Pagamento intermediado com retenção** aprovado. | D2 |
| 12/10/2026 | A **tarifa do provedor de pagamento é descontada do vendedor**. | D2 |

## Sumário

1. Leitura do problema
2. Premissas: o que confirmar antes de construir
3. Decisões estruturais (com recomendação)
4. Perfis, papéis e permissões
5. Jornada de compra
6. Jornada de venda
6. Pós-venda, problemas e disputas
7. Ciclos de vida (máquinas de estado)
8. Regras de negócio
9. Escopo do MVP: dentro, depois e fora
10. Arquitetura funcional e modelo de dados
11. Inventário de telas
12. Como saber se o MVP funcionou
13. Riscos principais
15. Perguntas em aberto, por prioridade

---

## 1. Leitura do problema

### O que cada lado precisa

| Lado | Dor | O que o produto precisa resolver |
|---|---|---|
| Família que vende | Roupas, brinquedos e acessórios perdem o uso em poucos meses; vender em grupos e brechós dá trabalho, exige negociação e expõe a família. | Anunciar rápido, não negociar com desconhecidos, receber com segurança, enviar sem complicação. |
| Família que compra | Produto infantil novo é caro e dura pouco; usado tem risco de "chegar diferente da foto". | Encontrar o tamanho ou a faixa de idade certa rápido, confiar no estado descrito, ter a quem recorrer se der errado. |
| Plataforma | Marketplace só funciona com oferta e procura ao mesmo tempo. | Gerar confiança dos dois lados e manter o custo de cada transação viável. |

### Três tensões que moldam todo o produto

1. **Ticket baixo × frete alto.** Uma peça usada costuma custar pouco, e o frete de uma única peça pode custar quase o mesmo que ela. Se isso não for tratado, a compra deixa de fazer sentido. Consequência direta no desenho: **incentivar várias peças do mesmo vendedor em um único envio** (carrinho agrupado por vendedor, frete único por vendedor, venda de lotes).
2. **Peça única.** Cada anúncio é, em geral, uma peça só. Isso exige reserva, controle de concorrência no checkout e remoção automática do anúncio vendido. Não existe "estoque" no sentido de loja.
3. **Confiança entre desconhecidos.** A plataforma precisa segurar o dinheiro até a peça chegar (ou até um prazo), padronizar a descrição do estado de conservação e ter um caminho claro para "recebi diferente do anunciado".
4. **Itens grandes e itens de segurança.** Com o escopo ampliado para todo o universo infantil, entram produtos que os Correios não transportam (por peso ou tamanho, como berço e alguns carrinhos) e produtos em que o uso anterior afeta a segurança da criança (cadeirinha de carro, berço, chupeta). Isso exige regras próprias por categoria (ver D13).

### O problema do começo (oferta antes da procura)

Um catálogo vazio afasta compradores; sem compradores, vendedores desistem. Recomendações:

- Começar com **oferta curada**: convidar um grupo inicial de famílias para anunciar antes da abertura ao público.
- Considerar **recorte geográfico inicial** (uma cidade ou região), o que concentra frete mais barato e facilita a divulgação boca a boca. **[decidir]**
- Focar a descoberta em **tamanho e faixa de idade**: a primeira pergunta de quem compra para criança é "o que serve no meu filho agora?".

---

## 2. Premissas: o que confirmar antes de construir

| # | Premissa | Status | O que depende dela | Como confirmar |
|---|---|---|---|---|
| P1 | É marketplace, não loja própria. | Firme | Todo o modelo de pedidos, repasses e responsabilidade. | Já decidido. |
| P2 | Famílias (pessoa física) anunciam e vendem. | **Confirmado**: só pessoa física | Cadastro (CPF), forma de recebimento, obrigações fiscais da plataforma sobre a comissão, limites de volume por vendedor. | Falta conversar com contador sobre a comissão e com advogado sobre termos de uso. Brechós e lojistas (CNPJ) ficam fora. |
| P3 | Produtos do universo infantil, de 0 a 6 anos. | **Confirmado** (ampliado) | Categorias, grade de tamanhos e faixas de idade, filtros, regras de segurança e de envio por categoria. | Validar a lista de categorias e as regras de D13. |
| P4 | Produtos usados ou seminovos. | **Confirmar** | Escala de estado de conservação; se peças novas com etiqueta são aceitas (recomendação: sim, como um dos estados). | Decisão de produto. |
| P5 | Cada vendedor cadastra suas peças. | Firme | Painel do vendedor. | — |
| P6 | Compradores compram de diferentes vendedores. | **Confirmar formato** | Carrinho, checkout, frete, divisão do pagamento. | Ver decisão D3. |
| P7 | Plataforma transmite confiança aos dois lados. | Firme como objetivo, **define custo** | Pagamento intermediado com retenção, política de problemas, moderação, avaliações. | Ver decisões D2, D6 e D9. |
| P8 | Site responsivo, com app no futuro. | Firme | Arquitetura com API separada da interface desde o início. | — |
| P9 | **(implícita)** A plataforma processa o pagamento. | **Confirmado**: pagamento intermediado com retenção | Tudo que envolve dinheiro: checkout, repasse, comissão, reembolso. | Escolha do provedor de pagamento (D2). É a decisão que mais trava o resto. |
| P10 | **(implícita)** A plataforma organiza o frete. | **Confirmado**: Correios, pago pelo comprador; frete grátis ou reduzido como brinde do vendedor | Cálculo de frete, etiqueta, rastreio, prazos de postagem. | Falta escolher a forma de integração com os Correios (D4). |
| P11 | **(implícita)** Há receita por comissão. | **Confirmado**: comissão por nível de visibilidade | Cálculo do repasse, tela financeira do vendedor, ordenação da busca. | Falta validar o que cada nível entrega (D12). |

Todas as premissas bloqueadoras estão confirmadas. Falta escolher o provedor de pagamento e a forma de integração com os Correios, o que não muda as regras, só a implementação.

---

## 3. Decisões estruturais (com recomendação)

### D1. Conta única com dois papéis
**Recomendação:** uma única conta. Toda pessoa cadastrada pode comprar; para vender, ela **ativa o perfil de vendedor** completando dados adicionais.
**Por quê:** famílias que vendem também compram o tamanho seguinte; separar contas cria atrito e duplica cadastro.
**Regra:** o vendedor não pode comprar o próprio anúncio.

### D2. Pagamento intermediado com retenção (decidido)
**Em palavras simples:** o comprador paga à plataforma, não ao vendedor. A plataforma guarda o dinheiro enquanto a peça viaja e só repassa ao vendedor quando a peça chega e está como anunciada. Se não chegar ou chegar diferente, o dinheiro volta ao comprador. Funciona como um "cofre de confiança" entre duas famílias que não se conhecem.

**Exemplo:** Ana compra um macacão de R$ 40 da Bia, com frete de R$ 22. Ana paga R$ 62 por Pix. A Bia é avisada, posta pelos Correios, Ana recebe e confirma. Só então a Bia recebe R$ 40 menos 5% de comissão (nível Clássica) e menos a tarifa do pagamento, ou seja, um pouco menos de R$ 38. Se fosse uma das 3 primeiras vendas da Bia, só a tarifa seria descontada. Se Ana não confirmar nem reclamar em 7 dias após a entrega, o valor é liberado do mesmo jeito.

**Recomendação:** usar um provedor de pagamentos com **divisão automática do valor** entre plataforma e vendedor e **retenção até a liberação** (subcontas para o vendedor). O dinheiro do comprador fica retido e só é liberado ao vendedor quando:
- o comprador confirma o recebimento, **ou**
- passa um prazo após a entrega registrada sem abertura de problema (sugestão: 7 dias). **[decidir prazo]**

**Por quê:** é o principal elemento de confiança para quem compra e evita que a plataforma tenha que repassar dinheiro manualmente.
**Alternativa descartada para o MVP:** classificados (comprador paga direto ao vendedor). Mais simples, mas elimina a proteção ao comprador e a receita por comissão fica difícil de cobrar.
**Implicação:** o vendedor precisa passar pela verificação de identidade exigida pelo provedor (CPF, dados bancários ou chave Pix). Isso entra na jornada de venda.
**Métodos no MVP:** Pix e cartão de crédito. Boleto fica para depois (demora a compensar e complica a reserva da peça).
**Tarifa do provedor (decidido):** os provedores cobram uma tarifa por transação, em geral menor no Pix e maior no cartão. Essa tarifa é **descontada do vendedor**, inclusive nas 3 vendas de boas-vindas. Como a tarifa muda conforme o comprador paga com Pix ou cartão, o vendedor vê no anúncio uma **estimativa** ("você recebe cerca de R$ X") e, no extrato, o valor exato de cada venda. As regras de venda e a página "Como funciona" explicam isso antes do primeiro anúncio.

### D3. Carrinho com vários vendedores, fechamento por vendedor
**Recomendação para o MVP:** o carrinho aceita peças de vendedores diferentes, mas **aparece agrupado por vendedor**, e cada grupo é fechado em um checkout próprio, gerando **um pedido por vendedor**.
**Por quê:** cada vendedor envia de um endereço diferente, então frete, prazo, rastreio, cancelamento e reembolso já são naturalmente por vendedor. Um único pagamento dividido entre vários vendedores complica reembolsos parciais e não traz ganho real no começo.
**Evolução:** pagamento único para vários vendedores fica para depois, se os dados mostrarem que compradores fecham com muitos vendedores ao mesmo tempo.
**Incentivo:** destacar "mais peças deste vendedor no seu tamanho" para aproveitar o mesmo frete.

### D4. Frete pelos Correios, com etiqueta gerada pela plataforma
**Decidido:** envio pelos Correios (PAC e SEDEX). Na compra, o comprador vê as opções com preço e prazo; após o pagamento, o vendedor recebe a **etiqueta pronta** para imprimir e postar.
**Forma de integração [decidir]:**
- **Contrato direto da plataforma com os Correios:** exige CNPJ da plataforma e contrato comercial; dá acesso às tabelas de contrato.
- **Por um intermediador de etiquetas** que emite etiquetas dos Correios: começa mais rápido, sem contrato próprio, e já entrega cotação, etiqueta e rastreio prontos.
Recomendação: começar pelo intermediador e avaliar o contrato direto quando o volume justificar.
**Por quê:** o vendedor não precisa calcular nada, o rastreio entra no sistema automaticamente e a plataforma sabe quando a peça foi postada e entregue (o que alimenta a liberação do pagamento).
**Dado necessário:** peso e dimensões do pacote. Para não pedir isso a cada anúncio, usar **pacotes padrão por categoria** (ex.: "body/camiseta", "conjunto", "casaco", "calçado", "brinquedo pequeno", "brinquedo médio") e somar para vários itens, com teto por envio.
**Limites dos Correios:** há limite de peso e de tamanho por pacote. Produtos que não cabem nesse limite (berço, alguns carrinhos e cercadinhos) **não entram no MVP** ou exigem outra forma de entrega (ver D13).

**Quem paga o frete (decidido):** o **comprador**, pelo valor cotado nos Correios, somado ao total na hora da compra.

**Brinde de frete da loja (decidido):** cada vendedor tem a sua **loja** (o perfil público com todos os anúncios dele) e pode oferecer, como brinde, **frete grátis** ou **frete reduzido**. O custo do brinde é **descontado do valor que o vendedor recebe**.

Configuração do brinde, feita uma vez na loja (proposta **[validar]**):
- **Tipo:** frete grátis, ou desconto no frete (valor fixo em reais, ex.: "R$ 10 de desconto no frete").
- **Condição:** em qualquer pedido, ou só em pedidos acima de R$ X. O pedido mínimo estimula a compra de várias peças da mesma loja, o que já é o objetivo do frete único.
- **Ligar e desligar** a qualquer momento; vale para pedidos feitos a partir daí.

Como aparece para o comprador:
- Selo na loja e nos anúncios dela ("Frete grátis acima de R$ 80" / "R$ 10 de desconto no frete").
- No carrinho: "faltam R$ 15 para ganhar o frete grátis desta loja".
- No resumo da compra: frete cotado, desconto do brinde e frete final.

**Proteção do vendedor:** o desconto do brinde **nunca passa do valor que o vendedor vai receber** naquele pedido. Se o frete de uma região distante for maior, o brinde cobre só até esse limite e o comprador paga a diferença. Ao configurar, o vendedor vê exemplos de quanto o frete costuma custar.

**Entrega em mãos:** fora do MVP, porque não gera comprovante de entrega e enfraquece a proteção do comprador.

### D5. Um anúncio = uma peça (ou um lote fechado)
- Quantidade sempre 1.
- **Lote** é permitido como um único anúncio com várias peças do mesmo tamanho (ex.: "5 bodies 3–6 meses"), com fotos de todas. Vende tudo junto.
- Ao iniciar o pagamento, a peça fica **reservada** por um tempo curto (sugestão: 15 minutos para cartão, validade do Pix para Pix). Se o pagamento não se confirmar, a peça volta a ficar disponível.
- Adicionar ao carrinho **não reserva**. O carrinho avisa quando uma peça foi vendida a outra pessoa.

### D6. Moderação de anúncios
**Recomendação:** os **primeiros anúncios de cada vendedor novo** (sugestão: os 3 primeiros) passam por **análise antes de publicar**; depois disso, publicação imediata com **análise posterior** e botão "denunciar" para qualquer usuário.
**Por quê:** filtra o grosso dos problemas sem criar fila permanente para a equipe.

### D7. Tamanho e faixa de idade como eixo principal
- **Roupas — grade padronizada obrigatória:** Prematuro, RN, 0–3m, 3–6m, 6–9m, 9–12m, 12–18m, 18–24m, 2, 3, 4, 5, 6, 8 anos. (O 8 cobre crianças de 6 anos que já usam numeração maior.) **[validar grade]**
- **Demais categorias — faixa de idade indicada:** 0–6 meses, 6–12 meses, 1–2 anos, 2–4 anos, 4–6 anos. Um produto pode ter mais de uma faixa. O filtro de idade da busca combina as duas coisas: escolher "2 anos" mostra roupas tamanho 2 e brinquedos da faixa 2–4.
- **Tamanho da etiqueta** (texto livre opcional), porque marcas variam.
- **Medidas opcionais** (comprimento, largura de peito, cintura) com ícone de ajuda mostrando onde medir. Anúncios com medidas ganham selo "com medidas".
- Calçados: numeração brasileira.

### D8. Estado de conservação com critérios objetivos
Escala fechada, com descrição visível para quem anuncia e para quem compra:

| Estado | Critério |
|---|---|
| Novo com etiqueta | Nunca usado, etiqueta da loja presa. |
| Novo sem etiqueta | Nunca usado, sem etiqueta. |
| Seminovo | Usado poucas vezes, sem nenhuma marca, mancha, bolinha ou desbotamento visível. |
| Usado, em bom estado | Sinais leves de uso (leve desbotamento, poucas bolinhas). Detalhes descritos. |
| Usado, com detalhe | Possui um detalhe pontual (pequena mancha, costura a refazer). **Foto do detalhe obrigatória.** |

Peças rasgadas, com mancha grande, mofo ou odor não são aceitas.

Para brinquedos, livros e itens com partes ou funcionamento, dois campos extras obrigatórios:
- **Completo?** (sim / não, com lista do que falta).
- **Funcionando?** (sim / não se aplica). Item que não funciona não é aceito.

### D9. Avaliações
**MVP:** o comprador avalia o pedido (nota de 1 a 5 e comentário opcional) depois que ele é concluído. A nota média e o número de vendas aparecem no perfil do vendedor.
**Depois:** avaliação do comprador pelo vendedor.

### D10. Comunicação entre comprador e vendedor
**MVP:** sem chat livre antes da compra. Duas formas de comunicação:
- **Perguntas no anúncio** (públicas, visíveis a todos, moderáveis). Reduz perguntas repetidas e evita negociação por fora. **[decidir se entra no MVP ou logo depois]**
- **Mensagens dentro do pedido**, só depois do pagamento, para combinar detalhes de envio.

Bloqueio automático de telefone, e-mail e links nas mensagens, para manter a transação dentro da plataforma (onde existe proteção).

**Fora do MVP:** ofertas e contraproposta de preço.

### D11. Proteção de dados de crianças
- **Proibido** foto com criança identificável (rosto). Peça vestida só sem rosto, ou peça estendida/em cabide. Regra clara no cadastro e motivo de reprovação na moderação.
- Remoção automática dos metadados das fotos (inclui localização).
- Nunca pedir nome ou idade da criança. Se no futuro houver "perfil do filho" para recomendações de tamanho, guardar só faixa de tamanho, não data de nascimento. Revisar com advogado à luz da LGPD.

### D12. Comissão por nível de visibilidade
**Decidido:** o vendedor escolhe, em cada anúncio, um nível de visibilidade. Quanto mais visibilidade, maior a comissão cobrada **sobre o preço da peça** (o frete não entra na conta).

| Nível | Comissão | Limite | O que entrega (proposta **[decidir]**) |
|---|---|---|---|
| Clássica | 5% | sem limite | Aparece na busca e nas categorias normalmente, sem destaque. É o nível padrão. |
| Megafone | 8% | sem limite | Aparece antes dos anúncios Clássica nos resultados de busca e categoria, com selo de destaque. |
| Outdoor | 10% | sem limite | Tudo do Megafone, mais vitrine na página inicial e nas páginas de categoria. |

**Regras propostas:**
- O nível é escolhido ao publicar e pode ser **aumentado** a qualquer momento. Diminuir também é permitido, mas só vale para vendas futuras.
- A comissão cobrada é a do nível **ativo no momento do pagamento** e fica gravada no pedido.
- Anúncios em destaque são **sinalizados** como destaque para o comprador (transparência), e os resultados continuam respeitando os filtros escolhidos.
- Para o destaque não virar bagunça, dentro de cada nível a ordem segue o critério escolhido pelo comprador (mais recentes, menor preço etc.).

**Boas-vindas: 3 vendas sem comissão (decidido, opção B):**
- As **3 primeiras vendas** de cada vendedor não pagam comissão. Só a tarifa do pagamento é descontada.
- A isenção vale para a comissão **Clássica**. Se o vendedor escolher Megafone ou Outdoor, paga a diferença de visibilidade (3% ou 5%) já nessas vendas. **[validar]**
- Conta a venda **concluída**; venda cancelada ou reembolsada devolve a vaga de isenção.
- A isenção é por CPF, não por conta, para não ser reaproveitada com outra conta.
- O painel do vendedor mostra quantas vendas de boas-vindas restam.

**Ponto de atenção:** com a tarifa do pagamento descontada do vendedor, a comissão é receita líquida da plataforma, mas ainda pequena por venda (5% de uma peça barata). Recomendação: fazer uma simulação simples (vendas por mês × preço médio × comissão) para saber quantas vendas pagam os custos fixos.

### D13. Categorias do universo infantil e regras por tipo de produto
**Decidido:** o catálogo cobre o universo infantil de 0 a 6 anos. Proposta de categorias e regras **[validar]**:

| Categoria | Exemplos | No MVP? | Regra especial |
|---|---|---|---|
| Roupas | bodies, macacões, conjuntos, vestidos, casacos, pijamas | Sim | Grade de tamanho obrigatória. Roupa íntima usada não é aceita. |
| Calçados | tênis, sandálias, sapatinhos | Sim | Numeração brasileira. |
| Acessórios de vestir | bonés, toucas, meias, laços | Sim | Meia usada só em lote higienizado ou nova **[decidir]**. |
| Brinquedos | pelúcias, blocos, brinquedos educativos | Sim | Completo e funcionando (D8). Foto do selo do Inmetro quando houver. Brinquedos com pilha: informar se acompanha. |
| Livros | livros de pano, de banho, infantis | Sim | Páginas completas, sem rasgos. |
| Enxoval | mantas, lençóis de berço, toalhas, saquinhos | Sim | Sem mancha. |
| Passeio | carrinhos compactos, bolsas de maternidade, slings | Só os que cabem no limite dos Correios | Carrinho: informar se freios e travas funcionam; foto do mecanismo. |
| Alimentação | pratinhos, copos, babadores | Sim, com restrição | Mamadeiras, bicos e chupetas **só novos e lacrados**. |
| Higiene e banho | banheiras, redutores | Sim, com restrição | Itens de uso íntimo ou que vão à boca: só novos e lacrados. |
| Segurança | cadeirinha de carro, bebê-conforto | **Não no MVP** | Exigem certificação do Inmetro e, usados, podem ter sofrido acidente sem dano visível. Recomendação: proibir. |
| Móveis | berço, cercadinho, cadeirão | **Não no MVP** | Não cabem no limite dos Correios; berço também exige certificação. |

Antes de abrir uma categoria com regra de segurança, **validar com advogado** a responsabilidade da plataforma pela revenda desses itens.

---

## 4. Perfis, papéis e permissões

### Perfis
- **Visitante:** sem login.
- **Membro comprador:** qualquer conta cadastrada e com e-mail confirmado.
- **Membro vendedor:** membro que ativou o perfil de vendedor (dados de recebimento aprovados pelo provedor de pagamento).
- **Equipe da plataforma** (painel administrativo), com papéis separados:
  - **Moderação:** analisa anúncios, denúncias, perguntas e mensagens.
  - **Atendimento:** acompanha pedidos, media problemas e disputas, cancela pedidos.
  - **Financeiro:** consulta pagamentos, retém e libera repasses, faz reembolsos.
  - **Administração:** tudo acima, mais configurações (comissão, prazos, categorias, grade de tamanhos) e gestão da equipe.

### Matriz de permissões

| Ação | Visitante | Comprador | Vendedor | Equipe |
|---|:-:|:-:|:-:|:-:|
| Ver página "como funciona", regras e ajuda | ✔ | ✔ | ✔ | ✔ |
| Pesquisar, navegar, filtrar e ordenar | ✔ | ✔ | ✔ | ✔ |
| Ver anúncio e perfil público do vendedor | ✔ | ✔ | ✔ | ✔ |
| Simular frete por CEP no anúncio | ✔ | ✔ | ✔ | — |
| Adicionar ao carrinho | ✔ (carrinho local, migra no login) | ✔ | ✔ | — |
| Favoritar peça / seguir vendedor | — | ✔ | ✔ | — |
| Fazer pergunta no anúncio | — | ✔ | ✔ | — |
| Finalizar compra | — | ✔ | ✔ (não no próprio anúncio) | — |
| Acompanhar pedidos, abrir problema, avaliar | — | ✔ | ✔ | — |
| Criar e gerenciar anúncios | — | — | ✔ | — |
| Responder perguntas, gerenciar vendas, imprimir etiqueta | — | — | ✔ | — |
| Ver extrato de vendas e repasses | — | — | ✔ | — |
| Moderar, mediar, reter/liberar repasses, reembolsar | — | — | — | ✔ (conforme papel) |
| Suspender conta | — | — | — | ✔ (Administração/Moderação) |

**Princípio:** o visitante chega até o momento de pagar sem criar conta. O cadastro é pedido no checkout, quando o valor já ficou claro.

---

## 5. Jornada de compra

Legenda: 🔓 sem login · 🔐 exige login · ✅ validação · ⚠️ confirmação explícita

| # | Etapa | O que acontece | Pontos de controle | Exceções e cuidados |
|---|---|---|---|---|
| 1 | Descobrir o marketplace | Página inicial explica em 3 passos como comprar e vender, mostra a proteção da compra e peças recentes. | 🔓 | Primeira visita: oferecer "escolha o tamanho" logo no início. |
| 2 | Pesquisar ou navegar | Busca por texto e navegação por categoria (Bodies, Macacões, Camisetas, Calças, Vestidos, Conjuntos, Casacos, Pijamas, Calçados...) e por tamanho. | 🔓 | Busca sem resultado: sugerir tamanhos vizinhos e permitir "avise-me quando chegar" (depois do MVP). |
| 3 | Filtrar e ordenar | Filtros: tamanho (principal, fica salvo para a próxima visita), categoria, gênero/unissex, estado, faixa de preço, marca, cor, estação, "com medidas", localização do vendedor. Ordenação: mais recentes (padrão), menor preço, maior preço. | 🔓 | Peças vendidas e pausadas nunca aparecem. |
| 4 | Ver detalhe da peça | Galeria de fotos, título, preço, tamanho, medidas, estado (com critério explicado), marca, composição, descrição, vendedor (nota, vendas, tempo de casa, cidade), perguntas, outras peças do vendedor no mesmo tamanho. | 🔓 | Se a peça foi vendida enquanto a página estava aberta, mostrar "vendida" e sugerir semelhantes. |
| 5 | Verificar tamanho, estado, preço, vendedor e frete | Simulação de frete pelo CEP. Selo "compra protegida" com link para as regras. | 🔓 ✅ CEP válido | Frete indisponível para a região: informar antes do carrinho. |
| 6 | Adicionar ao carrinho ou comprar agora | Carrinho agrupado por vendedor; "comprar agora" vai direto ao checkout daquele vendedor. | 🔓 (carrinho local) | Peça vendida a outra pessoa: aviso no carrinho e remoção. Bloqueio de compra do próprio anúncio. |
| 7 | Identificar-se | Entrar ou criar conta (e-mail e senha ou login social). Carrinho local migra para a conta. | 🔐 ✅ e-mail confirmado antes do pagamento (código por e-mail) | Recomendação: confirmação por código no próprio fluxo, sem sair do checkout. |
| 8 | Endereço de entrega | Selecionar endereço salvo ou cadastrar novo (busca por CEP preenche rua e cidade). Recalcula o frete. | 🔐 ✅ CEP, número, destinatário | CPF do comprador é pedido aqui (exigido pelo provedor de pagamento e pela etiqueta). |
| 9 | Escolher frete e pagamento | Opções de frete com preço e prazo. Pix ou cartão. Resumo: itens + frete = total. | 🔐 ✅ dados do cartão validados pelo provedor | A comissão da plataforma **não** é cobrada do comprador (recomendação; ver P11). |
| 10 | Confirmar a compra | Botão "Confirmar e pagar" após resumo com itens, endereço, frete, total e link para as regras de compra protegida. | ⚠️ aceite das regras na primeira compra ✅ **revalidar disponibilidade e preço** no momento da confirmação; peça entra em **reserva** | Reserva expira sem pagamento: pedido cancelado automaticamente e peça volta ao catálogo. Pagamento recusado: permanecer no checkout com mensagem clara. |
| 11 | Pagamento aprovado | Pedido passa a "Pago". Comprador e vendedor são notificados. Anúncio sai do catálogo como "vendido". | ✅ confirmação vem do **provedor de pagamento** (aviso automático), nunca da tela do comprador | Pix pago depois da expiração da reserva e peça já vendida: reembolso automático. |
| 12 | Acompanhar o pedido | Linha do tempo: pago → enviado (com rastreio) → entregue. Mensagens com o vendedor dentro do pedido. | 🔐 | Vendedor não posta no prazo: cancelamento automático com reembolso integral (ver RN-30). |
| 13 | Receber o produto | Após entrega registrada pela transportadora, comprador é convidado a **confirmar recebimento** ou **relatar um problema**. | 🔐 ⚠️ "Recebi e está tudo certo" libera o pagamento ao vendedor (deixar isso claro antes do clique) | Sem ação em 7 dias: conclusão automática. |
| 14 | Avaliar | Nota e comentário sobre o vendedor e a peça. | 🔐 ✅ só quem comprou, só pedido concluído, uma vez por pedido | Avaliação ofensiva: denúncia e moderação. |

---

## 6. Jornada de venda

| # | Etapa | O que acontece | Pontos de controle | Exceções e cuidados |
|---|---|---|---|---|
| 1 | Criar conta | Mesma conta do comprador. | ✅ e-mail confirmado ⚠️ aceite dos termos de uso e da política de privacidade | — |
| 2 | Ativar perfil de vendedor (abrir a loja) | Nome da loja, foto ou ícone e, se quiser, o brinde de frete. Dados exigidos: nome completo, CPF, data de nascimento, telefone celular, endereço de postagem (origem do frete), dados de recebimento (conta bancária ou chave Pix no mesmo CPF). Nome público e cidade aparecem no perfil; o resto não. | ✅ CPF válido e único na plataforma ✅ maioridade (18+) ✅ celular confirmado por código ✅ **verificação do provedor de pagamento** (subconta aprovada) ⚠️ aceite das regras de venda (estado de conservação, prazos de postagem, comissão) | Verificação pendente: pode criar anúncios como rascunho, mas não publicar. Verificação recusada: explicar o motivo e o caminho. |
| 3 | Cadastrar uma peça | Formulário guiado em etapas curtas. Rascunho salvo automaticamente. | 🔐 vendedor ativo | — |
| 4 | Adicionar fotos | Mínimo 3 fotos (frente, costas, etiqueta), máximo 10. Foto obrigatória do detalhe quando o estado for "Usado, com detalhe". Dicas de luz e fundo na tela. | ✅ formato e tamanho de arquivo ✅ resolução mínima ✅ remoção de metadados ⚠️ declaração de que não há criança identificável na foto | Primeira foto vira capa; vendedor pode reordenar. |
| 5 | Informar dados da peça | Categoria, tamanho (grade padronizada), tamanho da etiqueta, medidas (opcional), gênero/unissex, marca (lista com sugestão + "outra"), cor, estação, composição (opcional), estado de conservação (escala fechada), título, descrição, preço, pacote padrão para frete, **nível de visibilidade** (Clássica, Megafone, Outdoor). Para categorias fora do vestuário: faixa de idade, completo e funcionando. Estimativa do valor que o vendedor vai receber, já descontadas a comissão e a tarifa do pagamento (avisando quando a venda entra nas 3 de boas-vindas). | ✅ campos obrigatórios por categoria ✅ preço entre mínimo e máximo da plataforma ✅ descrição obrigatória para "Usado, em bom estado" e "Usado, com detalhe" ✅ filtro de palavras proibidas e de contatos (telefone, e-mail, links) | Lote: informar quantidade de peças e garantir uma foto de cada. |
| 6 | Publicar o anúncio | Revisão final em formato de prévia. | ⚠️ "Confirmo que a descrição corresponde à peça" ✅ moderação prévia para os primeiros anúncios | Anúncio reprovado: motivo claro e possibilidade de corrigir e reenviar. |
| 7 | Gerenciar anúncios | Lista com abas: Ativos, Em análise, Rascunhos, Pausados, Vendidos, Reprovados. Ações: editar, pausar, reativar, excluir, responder perguntas. | 🔐 | Anúncio **reservado** ou **vendido** não pode ser editado nem excluído. Editar preço ou fotos de anúncio ativo pode voltar à análise **[decidir]**. Anúncios sem atualização há muito tempo: lembrete para revisar ou baixar preço (depois do MVP). |
| 8 | Receber uma venda | Notificação (e-mail e no site) com itens, prazo de postagem e etiqueta disponível. | ✅ só depois da confirmação do pagamento pelo provedor | O vendedor **não escolhe aceitar ou recusar**: se não puder enviar, cancela (o que conta contra a reputação). |
| 9 | Preparar e enviar | Instruções de embalagem. Download da etiqueta. Botão "Marcar como postado" (o rastreio confirma). | ✅ prazo de postagem (sugestão: 3 dias úteis) ⚠️ cancelamento pelo vendedor exige motivo | Lembretes automáticos no 2º dia útil. Sem postagem no prazo: cancelamento automático e reembolso. |
| 10 | Acompanhar a transação | Linha do tempo igual à do comprador, com a data prevista de liberação do valor. Mensagens do pedido. | 🔐 | Problema aberto pelo comprador: o vendedor é notificado e tem prazo para responder. |
| 11 | Consultar pagamentos, comissões e repasses | Extrato por venda: preço da peça, comissão, valor líquido, status (a liberar, liberado, retido, pago) e data. Totais do mês. | 🔐 | Valor retido por problema aberto: mostrar o motivo e o andamento. |

---

## 7. Pós-venda, problemas e disputas

Fluxo "Tenho um problema com este pedido", disponível ao comprador do envio até 7 dias após a entrega:

1. **Motivos:** não recebi; chegou diferente do anunciado (tamanho, estado, peça); chegou danificado no transporte; outro.
2. **Evidência:** fotos obrigatórias para "diferente do anunciado" e "danificado".
3. **Efeito imediato:** o repasse ao vendedor fica **retido**.
4. **Resposta do vendedor** (prazo sugerido: 2 dias úteis): aceitar devolução com reembolso, propor outra solução, ou contestar.
5. **Sem acordo ou sem resposta:** a equipe de Atendimento media e decide.
6. **Resultados possíveis:** reembolso integral com devolução da peça (etiqueta de devolução paga por quem errou: vendedor se a peça não correspondia ao anúncio; plataforma se o problema foi da transportadora **[decidir]**); reembolso sem devolução (casos de baixo valor, decisão da equipe); improcedente, com liberação ao vendedor.

**Arrependimento simples** ("não gostei", "não serviu" com tamanho correto no anúncio): **[decidir]**. Recomendação: no MVP, não aceitar devolução por arrependimento entre famílias, deixando isso explícito antes da compra, e investir em medidas e fotos para reduzir o erro de tamanho. **Validar com advogado** como o direito de arrependimento em compras pela internet se aplica a vendas entre pessoas físicas intermediadas pela plataforma.

---

## 8. Ciclos de vida (máquinas de estado)

### Anúncio
```
Rascunho ──publicar──▶ Em análise ──aprovar──▶ Ativo ◀──reativar── Pausado
                         │                     │  ▲                  ▲
                         └─reprovar─▶ Reprovado │  └─expira reserva──┐ │
                                                ├─pausar─────────────┼─┘
                                                ├─iniciar pagamento─▶ Reservado ──pagamento ok──▶ Vendido
                                                └─excluir / remover pela moderação─▶ Removido
```
- Vendedor novo depois da fase de análise: Rascunho → Ativo direto.
- Pedido cancelado após "Vendido" (vendedor não postou, reembolso): o anúncio volta para **Pausado**, não para Ativo, para o vendedor confirmar que ainda tem a peça.

### Pedido
```
Aguardando pagamento ──pago──▶ Pago ──postado──▶ Enviado ──entregue──▶ Entregue ──confirmado ou 7 dias──▶ Concluído
        │                       │                    │                     │
        └─expirou/recusado─▶ Cancelado ◀──vendedor não postou / cancelou───┘
                                                     └──problema aberto──▶ Em disputa ──▶ Concluído | Reembolsado
```

### Repasse ao vendedor
```
Pendente (pedido pago) ──pedido concluído──▶ Liberado ──provedor transfere──▶ Pago
        └──problema aberto──▶ Retido ──decisão──▶ Liberado | Estornado
```

---

## 9. Regras de negócio

**Contas**
- RN-01 Uma conta por e-mail; um perfil de vendedor por CPF.
- RN-02 Vendedor precisa ter 18 anos ou mais.
- RN-03 Dados pessoais (CPF, endereço completo, telefone) nunca aparecem publicamente. Perfil público mostra nome escolhido, cidade/estado, nota, vendas e data de entrada.
- RN-04 A plataforma pode suspender contas por fraude, reincidência em anúncios enganosos ou tentativa de negociar por fora.

**Anúncios**
- RN-10 Um anúncio corresponde a uma peça ou a um lote vendido junto; quantidade é sempre 1.
- RN-11 Mínimo de 3 fotos; foto do detalhe obrigatória no estado "Usado, com detalhe".
- RN-12 Proibidas fotos com criança identificável.
- RN-13 Itens não aceitos: roupa íntima usada, peças rasgadas ou com mancha grande, mofo ou odor, falsificações, itens que não funcionam, mamadeiras/bicos/chupetas usados, cadeirinhas de carro e bebê-conforto, móveis, itens que não cabem no limite dos Correios, produtos fora do universo infantil de 0 a 6 anos. **[validar lista]**
- RN-18 Cada anúncio tem um nível de visibilidade (Clássica, Megafone ou Outdoor); o padrão é Clássica.
- RN-14 Preço mínimo e máximo definidos pela plataforma. **[decidir valores]**
- RN-15 Anúncio reservado ou vendido não pode ser editado nem excluído.
- RN-16 Os primeiros anúncios de cada vendedor passam por análise antes de publicar.
- RN-17 Telefones, e-mails e links são bloqueados em títulos, descrições, perguntas e mensagens.

**Compra**
- RN-20 O vendedor não pode comprar o próprio anúncio.
- RN-21 Cada checkout contém peças de um único vendedor e gera um pedido.
- RN-22 Disponibilidade e preço são revalidados no momento da confirmação.
- RN-23 A peça é reservada ao iniciar o pagamento; a reserva expira se o pagamento não for confirmado no prazo.
- RN-24 Só a confirmação do provedor de pagamento muda um pedido para "Pago".
- RN-25 Pagamento confirmado para peça que já não está disponível gera reembolso automático.

**Envio**
- RN-30 Prazo de postagem: 3 dias úteis após o pagamento. Sem postagem, o pedido é cancelado e o comprador é reembolsado integralmente. **[decidir prazo]**
- RN-31 O frete é cobrado do comprador pelo valor cotado nos Correios, sem margem da plataforma.
- RN-32 O envio usa sempre a etiqueta gerada pela plataforma, pelos Correios.
- RN-33 O vendedor pode oferecer na sua loja um brinde de frete (grátis ou desconto fixo), em qualquer pedido ou acima de um valor mínimo; o custo sai do valor que ele recebe.
- RN-34 Pedido com várias peças do mesmo vendedor tem um único frete, e o brinde é aplicado sobre ele.
- RN-35 O desconto do brinde nunca ultrapassa o valor que o vendedor vai receber no pedido; a diferença fica com o comprador.

**Dinheiro**
- RN-40 Comissão percentual sobre o preço da peça, conforme o nível de visibilidade ativo no momento do pagamento (Clássica 5%, Megafone 8%, Outdoor 10%), descontada do vendedor; o frete não entra na base da comissão.
- RN-44 Valor a receber pelo vendedor = preço − comissão − tarifa do pagamento − brinde de frete (quando houver).
- RN-45 As 3 primeiras vendas concluídas de cada CPF não pagam a comissão Clássica; a tarifa do pagamento é descontada normalmente.
- RN-41 O valor do vendedor é liberado quando o pedido é concluído (confirmação do comprador ou 7 dias após a entrega sem problema aberto).
- RN-42 Problema aberto retém o valor até a decisão.
- RN-43 Cancelamento antes do envio reembolsa o comprador integralmente, incluindo frete.

**Avaliações**
- RN-50 Só o comprador de um pedido concluído pode avaliar, uma vez por pedido, até 30 dias após a conclusão.
- RN-51 Cancelamentos causados pelo vendedor ficam visíveis como indicador no perfil (ex.: "envia no prazo em 98% das vendas").

---

## 10. Escopo do MVP: dentro, depois e fora

### Dentro do MVP
- Conta única com perfil de vendedor ativável.
- Catálogo com busca, categorias, filtros (com tamanho como filtro principal e memorizado) e ordenação.
- Página da peça com estado padronizado, medidas opcionais e simulação de frete.
- Favoritos.
- Carrinho agrupado por vendedor, checkout por vendedor, Pix e cartão.
- Níveis de visibilidade com comissão correspondente e vitrines de destaque.
- Pagamento intermediado com retenção e divisão automática.
- Frete pelos Correios com cotação, etiqueta gerada pela plataforma e rastreio; brinde de frete grátis ou reduzido configurado pela loja.
- 3 vendas de boas-vindas sem comissão.
- Pedido com linha do tempo, mensagens do pedido, confirmação de recebimento.
- Fluxo de problema com o pedido e mediação pela equipe.
- Avaliação do vendedor.
- Painel do vendedor: anúncios, vendas, extrato.
- Notificações por e-mail (e no site) para cada mudança de estado.
- Painel administrativo: moderação, pedidos e disputas, repasses, usuários, configurações básicas.
- Páginas institucionais: como funciona, compra protegida, regras de anúncio, termos, privacidade, ajuda.

### Logo depois (fase 2)
- Perguntas públicas no anúncio (se não entrarem no MVP).
- Seguir vendedor e alerta de novas peças num tamanho ("avise-me").
- Ofertas de preço.
- Pagamento único para vários vendedores.
- Avaliação do comprador pelo vendedor.
- Lembretes para baixar preço de anúncios parados; ações em massa no painel do vendedor.
- Notificações por WhatsApp.

### Mais adiante (fase 3+)
- Aplicativo.
- Perfil do filho para recomendação de tamanhos e "próximo tamanho".
- Saldo na plataforma (receber em crédito para comprar com bônus).
- Doação de peças não vendidas.
- Lojistas e brechós com CNPJ.
- Coleta em pontos parceiros.

### Fora (decisão consciente)
- Troca direta entre famílias (sem dinheiro): complica logística e proteção.
- Chat livre antes da compra.
- Pagamento combinado por fora.

---

## 11. Arquitetura funcional e modelo de dados

### Módulos (domínios)

| Módulo | Responsabilidade |
|---|---|
| Identidade e contas | Cadastro, login, confirmação de e-mail e celular, perfis, papéis, endereços. |
| Vendedores | Ativação do perfil, dados de recebimento, verificação junto ao provedor, reputação. |
| Catálogo | Anúncios, fotos, categorias, grade de tamanhos, marcas, estados de conservação. |
| Busca | Indexação, filtros, ordenação. Começar com a busca do próprio banco; ferramenta dedicada só se o volume pedir. |
| Carrinho e checkout | Carrinho agrupado, revalidação, reserva, criação do pedido. |
| Pedidos | Ciclo de vida do pedido, prazos automáticos, mensagens do pedido. |
| Pagamentos | Integração com o provedor, avisos automáticos de pagamento, reembolsos, repasses, comissão, extrato. |
| Logística | Cotação, etiqueta, rastreio, pacotes padrão. |
| Pós-venda | Problemas, disputas, decisões. |
| Avaliações | Notas e comentários, indicadores de reputação. |
| Moderação | Fila de análise, denúncias, filtros de conteúdo. |
| Notificações | E-mail e avisos no site, a partir de eventos dos outros módulos. |
| Administração | Painel da equipe, configurações, registro de ações (quem fez o quê). |

### Diretrizes de arquitetura
- **Um único sistema bem dividido por módulos** (não microsserviços) no começo: menos custo e complexidade, e os módulos acima podem virar serviços separados se um dia for preciso.
- **API separada da interface** desde o início, para o app futuro reutilizar tudo.
- **Banco relacional** (ex.: PostgreSQL): pedidos e dinheiro exigem consistência e transações.
- **Armazenamento de imagens** em serviço de objetos, com redimensionamento automático e entrega por CDN.
- **Tarefas agendadas** para os prazos (reserva expirada, postagem atrasada, conclusão automática, liberação de repasse).
- **Eventos internos** (ex.: "pedido pago", "pedido entregue") alimentando notificações e repasses.
- **Avisos automáticos do provedor de pagamento e de frete** tratados com segurança: verificação de assinatura e processamento idempotente (o mesmo aviso recebido duas vezes não pode gerar efeito duplicado).
- **Valores em centavos** (inteiros), nunca em ponto flutuante.
- **Registro de auditoria** para toda ação da equipe e toda mudança de estado de pedido e repasse.

A escolha de linguagem e framework fica para a etapa seguinte, depois das decisões bloqueadoras.

### Modelo de dados conceitual

```
Usuario (id, email, senha_hash, nome, telefone, email_confirmado_em, criado_em, status)
  ├─ Endereco (usuario_id, cep, rua, numero, complemento, bairro, cidade, uf, destinatario, padrao)
  ├─ PerfilVendedor (usuario_id, nome_publico, cpf, data_nascimento, endereco_origem_id,
  │                  conta_provedor_id, status_verificacao, anuncios_aprovados_qtd)
  └─ PapelEquipe (usuario_id, papel)

Anuncio (id, vendedor_id, titulo, descricao, categoria_id, tamanho_id, tamanho_etiqueta,
         genero, marca_id, cor, estacao, composicao, estado_conservacao, eh_lote, qtd_pecas_lote,
         faixas_idade, completo, itens_faltantes, funcionando, preco_centavos, pacote_padrao_id,
         nivel_visibilidade, status, reservado_ate, publicado_em, atualizado_em)
  ├─ FotoAnuncio (anuncio_id, url, ordem, eh_foto_detalhe)
  ├─ MedidaAnuncio (anuncio_id, tipo, valor_cm)
  └─ PerguntaAnuncio (anuncio_id, autor_id, texto, resposta, status)

Categoria · Tamanho (grade) · Marca · PacotePadrao (peso, dimensões)

Favorito (usuario_id, anuncio_id)
CarrinhoItem (usuario_id, anuncio_id, adicionado_em)

Pedido (id, comprador_id, vendedor_id, endereco_entrega (cópia), status,
        subtotal_centavos, frete_centavos, total_centavos, frete_cotado_centavos, brinde_frete_centavos, tarifa_pagamento_centavos, isento_boas_vindas,
        nivel_visibilidade (cópia),
        comissao_percentual (cópia), comissao_centavos, liquido_vendedor_centavos,
        prazo_postagem, entregue_em, concluido_em, criado_em)
  ├─ PedidoItem (pedido_id, anuncio_id, titulo (cópia), preco_centavos (cópia))
  ├─ HistoricoStatusPedido (pedido_id, de, para, motivo, autor, em)
  ├─ Envio (pedido_id, servico, codigo_rastreio, etiqueta_url, postado_em, eventos)
  ├─ Pagamento (pedido_id, provedor_id_externo, metodo, status, valor_centavos, eventos)
  ├─ Repasse (pedido_id, vendedor_id, valor_centavos, status, liberar_em, pago_em)
  ├─ MensagemPedido (pedido_id, autor_id, texto, em)
  ├─ Problema (pedido_id, motivo, descricao, fotos, status, decisao, decidido_por)
  └─ Avaliacao (pedido_id, nota, comentario, em)

Denuncia (alvo_tipo, alvo_id, autor_id, motivo, status)
Notificacao (usuario_id, tipo, dados, lida_em)
RegistroAuditoria (autor_id, acao, alvo, antes, depois, em)
PerfilVendedor também guarda: nome_loja, brinde_frete_tipo (nenhum|gratis|desconto), brinde_frete_valor_centavos,
  brinde_frete_pedido_minimo_centavos, vendas_boas_vindas_usadas (contadas por CPF)
Configuracao (comissao_por_nivel, vendas_boas_vindas, prazos, preco_min, preco_max, ...)
```

Ponto importante: o pedido **copia** título, preço e endereço no momento da compra, para que edições posteriores não alterem o histórico.

---

## 12. Inventário de telas

**Públicas:** Início · Como funciona · Compra protegida · Regras para anunciar · Resultado de busca/categoria · Página da peça · Loja do vendedor (perfil público) · Ajuda · Termos · Privacidade.

**Conta:** Entrar · Criar conta · Confirmar e-mail · Recuperar senha · Meus dados · Endereços · Favoritos · Notificações.

**Compra:** Carrinho (agrupado por vendedor) · Checkout (identificação → endereço → frete e pagamento → revisão) · Pagamento Pix (código e contagem regressiva) · Compra confirmada · Minhas compras · Detalhe do pedido (linha do tempo, mensagens, confirmar recebimento, relatar problema) · Avaliar.

**Venda:** Quero vender (ativação do perfil, em etapas) · Novo anúncio (etapas: fotos → dados → estado → preço → prévia) · Meus anúncios (abas) · Minhas vendas · Detalhe da venda (etiqueta, prazo, mensagens) · Extrato e repasses.

**Equipe:** Fila de moderação · Denúncias · Pedidos (busca e detalhe) · Problemas e disputas · Repasses · Usuários · Configurações · Registro de auditoria.

---

## 13. Como saber se o MVP funcionou

| Indicador | Por que importa |
|---|---|
| Anúncios ativos por tamanho | Mostra se há oferta suficiente em cada faixa (o catálogo precisa ter profundidade por tamanho, não só volume total). |
| Taxa de visita → compra | Saúde da procura. |
| Itens por pedido | Mostra se o incentivo a comprar mais peças do mesmo vendedor está diluindo o frete. |
| Peso do frete no total do pedido | Se for alto demais, o modelo logístico precisa mudar. |
| Pedidos com problema aberto (%) | Qualidade das descrições e da confiança. |
| Postagem no prazo (%) | Confiabilidade dos vendedores. |
| Vendedores que publicam um 2º lote | Se vender foi fácil o bastante para repetir. |
| Compradores que voltam em 90 dias | A criança cresce: a recompra é o motor do negócio. |

---

## 14. Riscos principais

| Risco | Mitigação no MVP |
|---|---|
| Frete inviabiliza peças baratas | Carrinho por vendedor, lotes, recorte geográfico inicial, destaque de peças do mesmo vendedor. |
| Catálogo vazio no lançamento | Grupo inicial de vendedoras convidadas antes da abertura. |
| Peça diferente do anunciado | Estado padronizado, fotos mínimas, foto do detalhe, retenção do pagamento, fluxo de problema. |
| Negociação por fora da plataforma | Bloqueio de contatos em textos, explicação clara da proteção que só existe dentro. |
| Fraude (contas falsas, golpe do pagamento) | Verificação do provedor, confirmação só pelo aviso do provedor, moderação dos primeiros anúncios. |
| Exposição de crianças | Proibição de rosto nas fotos, remoção de metadados, nenhum dado da criança. |
| Obrigações fiscais e legais não mapeadas | Consulta a contador e advogado antes do lançamento (comissão, termos de uso, privacidade, devoluções). |
| Operação manual pesada (moderação e disputas) | Prazos automáticos, moderação só nos primeiros anúncios, regras claras que reduzem casos ambíguos. |

---

## 15. Perguntas em aberto, por prioridade

**Bloqueiam o início do desenvolvimento do checkout e do financeiro:**
1. Há preferência de provedor de pagamento? (D2)
2. Correios por contrato direto ou por intermediador de etiquetas? (D4)
3. Nas vendas de boas-vindas com Megafone ou Outdoor, o vendedor paga só a diferença de visibilidade? (D12)
4. O brinde de frete proposto (grátis ou desconto fixo, com pedido mínimo opcional) atende? (D4)

**Definem regras e telas:**
5. O que cada nível de visibilidade entrega está de acordo com a proposta de D12?
6. A lista de categorias, as restrições de segurança (D13) e a grade de tamanhos e faixas de idade (D7) estão adequadas?
7. Lançamento nacional ou recorte geográfico inicial?
8. Devolução por arrependimento: aceitar ou não (após orientação jurídica)?
9. Perguntas públicas no anúncio entram no MVP?
10. Prazos: postagem (3 dias úteis), conclusão automática (7 dias), reserva (15 minutos) e janela para relatar problema estão adequados?
11. Preço mínimo e máximo por anúncio.

**Podem esperar, mas convém pensar cedo:**
12. Haverá curadoria de marcas ou qualquer marca é aceita?
13. Quem forma a equipe de operação no início (moderação e atendimento) e quanto tempo por dia ela tem? Isso calibra o quanto automatizar desde já.
