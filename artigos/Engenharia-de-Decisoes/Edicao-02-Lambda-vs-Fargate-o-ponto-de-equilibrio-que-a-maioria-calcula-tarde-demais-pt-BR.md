  # Engenharia de Decisões
### Edição #02 — Lambda vs. Fargate: o ponto de equilíbrio que a maioria calcula tarde demais


Esta newsletter analisa decisões de engenharia tomadas diante de problemas reais. Cada edição apresenta o contexto, os critérios de decisão, as alternativas avaliadas, os trade-offs aceitos e as evidências que sustentam a escolha.

Sem tutorial. Sem lista de ferramentas. Decisão, critério, evidência, resultado.

Hoje: automação de pipelines de dados com Lambda e Fargate — e o trade-off financeiro entre execução sob demanda e capacidade contínua, que muitas equipes só descobrem quando o volume cresce.

> Este não é um benchmark de performance entre Lambda e Fargate. É um modelo econômico baseado em premissas explícitas.



## Baseline

A escolha entre AWS Lambda e contêineres gerenciados (ECS/Fargate) costuma ser tratada como decisão de estilo de arquitetura. No início de um pipeline — poucas execuções por dia e cargas de segundos — a diferença absoluta de custo entre as duas abordagens tende a ser pequena, enquanto o Lambda reduz a complexidade operacional inicial (implantação, ou *deploy*).

Vale uma correção conceitual antes de seguir: Fargate também é computação gerenciada — a AWS provisiona e opera a infraestrutura, e você não administra servidor. A diferença real entre as duas abordagens não é "serverless vs. infraestrutura própria". É **serverless orientado a função/evento** (Lambda) versus **contêiner gerenciado com capacidade configurável** (ECS/Fargate).

O problema aparece quando o pipeline passa a processar milhões de eventos por mês: a decisão que era operacional vira decisão financeira, e poucas equipes recalculam o modelo de custo quando o volume da carga de trabalho (*workload*) muda de ordem de grandeza.

## Problema

Como decidir entre Lambda e contêiner para automação de pipeline de dados de forma que a escolha continue correta não apenas no piloto, mas no volume de produção esperado de 6 a 12 meses?

## Critérios de Decisão

- Custo total projetado no volume real esperado, não no volume do piloto
- Complexidade operacional que a equipe está disposta a absorver
- Restrições técnicas do ambiente de execução (timeout, memória, armazenamento temporário) frente à necessidade real do pipeline
- Existência de um ponto de equilíbrio financeiro calculável — não estimado por intuição

## Premissas do Modelo

Todo número apresentado nesta edição depende destas premissas. Mudar qualquer uma delas muda o resultado:

- Região: us-east-1 · Arquitetura: x86
- **Lambda:** 2 GB de memória alocada por execução; a CPU é proporcional à memória configurada e não é um parâmetro independente
- **Fargate:** 1 vCPU + 2 GB de memória por tarefa; CPU e memória são parâmetros da configuração da tarefa, sujeitos às combinações suportadas pelo Fargate
- 2 segundos de duração média por execução
- Carga suficientemente uniforme ao longo do mês (sem picos concentrados)
- 730 horas em um mês comercial
- Cálculo considera exclusivamente compute + requisições
- Para facilitar a comparação, o modelo não considera créditos ou Free Tier, nem CloudWatch, transferência de dados, NAT Gateway, ECR, Savings Plans ou Spot
- Modelo de preços: agosto/2026 · us-east-1 · x86 · On-Demand

## Alternativas Consideradas

**Lambda com pacote ZIP.** Empacotamento tradicional do código da função. Menor fricção de implantação inicial.

**Lambda com imagem de contêiner (OCI).** Mesma arquitetura Lambda/FaaS, empacotada como imagem Docker — o que muda é a forma de empacotamento, não o modelo de cobrança nem a natureza da execução. Reduz atrito de portabilidade entre ambientes.

**ECS/Fargate.** Cobrança pelos recursos solicitados pela tarefa (vCPU, memória e, quando aplicável, armazenamento) durante o tempo em que a tarefa permanece ativa; o custo não é diretamente indexado ao número de eventos processados nesse período.

## Modelo de Custo

**Lambda:** custo associado a requisições + duração × memória (GB-segundo).

**Fargate:** custo associado aos recursos solicitados pela tarefa (vCPU e memória) × tempo de execução.

O modelo de custo muda de cobrança predominantemente por execução (Lambda) para cobrança por capacidade alocada durante o período em que a tarefa está ativa (Fargate). Ambos podem crescer com a carga de trabalho, mas a função de custo é determinada por variáveis diferentes: duração e memória por execução no Lambda; recursos e tempo de execução das tarefas no Fargate — com autoscaling e paralelismo, o Fargate não necessariamente cresce de forma linear com o volume mensal.

Vale registrar também: o Lambda possui tiered pricing — desconto de 10% entre 6 e 15 bilhões de GB-segundos/mês e de 20% acima disso, por arquitetura. No cenário desta edição, o consumo fica em 40 milhões de GB-segundos — muito abaixo do primeiro patamar de desconto, então o tiered pricing não altera o cenário-base.

## Cenário-base

Pipeline de ingestão que valida, limpa e transforma arquivos JSON, com 10 milhões de execuções/mês: Lambda configurado com 2 GB de memória e duração média de 2 segundos por execução; Fargate configurado com 1 vCPU e 2 GB de memória por tarefa. Lambda é modelado por execução; Fargate é modelado por capacidade contínua neste cenário — são unidades de comparação diferentes, não configurações equivalentes.

**Custo no Lambda:** requisições (10 milhões × $0,20/milhão = $2,00) + compute (40.000.000 GB-s × $0,0000166667 = $666,67) = **$668,67/mês**.

**Custo da capacidade Fargate no modelo:** assumindo que cada tarefa processa uma unidade de trabalho por vez, a carga representa aproximadamente 7,6 vCPU-equivalentes de capacidade média (20.000.000 segundos-CPU ÷ 730h em segundos). Isso corresponde a aproximadamente 7,6 tarefas de 1 vCPU em execução contínua. Arredondando para 8 tarefas ativas 24/7: 8 tarefas × 730 h × US$0,04937/h = **US$288,32/mês**.

*Importante: este cenário não representa uma arquitetura ECS/Fargate completa. Ele representa uma unidade econômica de capacidade contínua, usada para comparar a função de custo com o Lambda — se uma tarefa processasse múltiplas unidades de trabalho em paralelo, o número de tarefas necessárias mudaria.*

*Nota: valores em USD representam preços de referência de us-east-1 para o cenário definido e podem mudar conforme região, arquitetura, contrato e atualização da tabela de preços da AWS.*

## Break-even

Sob estas premissas, o custo unitário estimado por execução no Lambda, incluindo requisição e compute, é de aproximadamente US$0,00006687, enquanto uma tarefa Fargate de 1 vCPU + 2 GB mantida 24/7 custa aproximadamente US$36,04/mês. Portanto:

> US$36,04 ÷ US$0,00006687 ≈ **540 mil execuções/mês**

Break-even não é um número fixo; é uma função: **custo mensal da capacidade Fargate ÷ custo Lambda por execução**. Alterar memória, duração, throughput ou distribuição de carga desloca esse ponto — o resultado acima descreve o cenário calculado, não uma regra universal entre Lambda e Fargate.

## Análise de Sensibilidade

*(carga uniforme e capacidade Fargate contínua — mesmas premissas do cenário-base)*

O volume mensal isolado não determina o vencedor — mas mostra a tendência sob as premissas fixadas. Os valores de Fargate representam a capacidade contínua mínima necessária sob a hipótese de carga uniforme e uma unidade de trabalho por vez por tarefa; não representam necessariamente o custo de uma arquitetura otimizada para cargas intermitentes. O número de tarefas é arredondado para cima.

| Execuções/mês | Lambda | Fargate | Melhor custo | Diferença mensal |
|---|---|---|---|---|
| 100 mil | $6,69 | $36,04 (1 tarefa mínima) | Lambda | $29,35 |
| 500 mil | $33,43 | $36,04 | Lambda (próximo do break-even) | $2,61 |
| **1 milhão** | **$66,87** | **$36,04** | **Fargate (inversão)** | **$30,83** |
| 5 milhões | $334,33 | $144,16 (4 tarefas) | Fargate | $190,17 |
| 10 milhões | $668,67 | $288,32 (8 tarefas) | Fargate | $380,35 |
| 50 milhões | $3.343,34 | $1.405,56 (39 tarefas) | Fargate | $1.937,78 |

O que a tabela evidencia é uma função de decisão, não um número isolado: abaixo de 540 mil execuções/mês, o custo mínimo de manter uma tarefa Fargate ativa pesa mais que pagar por execução no Lambda; acima disso, a curva se inverte.

*Observação: o custo do Fargate aparece em degraus porque o modelo arredonda a capacidade necessária para tarefas inteiras de 1 vCPU — Lambda cresce de forma aproximadamente linear por execução, Fargate cresce em saltos por tarefa adicionada. Em uma arquitetura real, o dimensionamento dependeria também do throughput (taxa de processamento) por tarefa, paralelismo e estratégia de autoscaling.*

## Trade-offs Aceitos

- **No cenário calculado, abaixo do break-even de ~540 mil execuções/mês:** aceitar o modelo de cobrança por invocação do Lambda, trocando potencial de eficiência de custo em cargas de trabalho de alta utilização por menor responsabilidade operacional e capacidade de escalar a zero.
- **Acima desse patamar, com tráfego constante:** aceitar a maior complexidade operacional de orquestrar tarefas, capacidade e autoscaling no ECS/Fargate, em troca de potencial redução do custo de computação. O custo poderia ser reduzido adicionalmente com Compute Savings Plans, disponíveis tanto para Lambda quanto para Fargate, quando houver compromisso justificável (o desconto exige comprometer volume por prazo determinado, não incorporado ao cenário-base), ou com Fargate Spot para cargas de trabalho tolerantes a interrupção (desconto de até 70% sobre o preço on-demand, segundo a AWS).
- **Timeout máximo de 15 minutos e sistema de arquivos local efêmero no Lambda** (`/tmp` gravável, configurável de 512 MB a 10.240 MB): aceitável para pipelines de eventos curtos; pode exigir decomposição do processamento ou uso de outros serviços quando uma etapa individual ultrapassa esse limite.

## Limitações da Análise

> Este modelo assume distribuição temporal uniforme e capacidade Fargate mantida continuamente ativa. Cargas em rajada (*bursty*), Savings Plans, Fargate Spot, cold starts, paralelismo, overhead de inicialização e custos auxiliares (rede, CloudWatch, NAT Gateway, ECR, free tier) não incorporados ao cenário-base deslocam o ponto de equilíbrio e devem ser calculados caso a caso. O modelo assume ainda que cada execução representa uma unidade de trabalho independente e que o throughput do contêiner é suficiente para processar essas unidades na taxa considerada.

> **Importante:** isso não é um benchmark de performance entre Lambda e Fargate — é um modelo econômico. O volume mensal, sozinho, não é o mesmo que capacidade necessária: a distribuição temporal da carga é a variável que decide o resultado.

## Decisão

Não existe vencedor único — existe um ponto de equilíbrio que deveria ser calculado para a carga de trabalho real, não assumido por padrão arquitetural. Abaixo dele, Lambda tende a vencer em custo e simplicidade operacional. Acima dele, contêiner gerenciado tende a vencer em custo, ao preço de gestão adicional — e ambas as leituras dependem da distribuição temporal da carga, não apenas do volume mensal.

A decisão correta, portanto, não é "Lambda ou Fargate"; é "qual função de custo representa melhor a carga de trabalho que teremos?"

**Regra de decisão deste modelo:**

- **< ~540 mil execuções/mês:** Lambda tende a ser economicamente favorecido.
- **≈ 540 mil:** zona de equilíbrio.
- **> ~540 mil, carga uniforme:** Fargate tende a apresentar menor custo de compute no modelo.
- **Carga em rajada (*bursty*):** recalcular — volume mensal sozinho não determina capacidade.

## Resultado do Modelo

Sob as premissas deste modelo, o ponto de equilíbrio ocorre em aproximadamente 540 mil execuções/mês para uma tarefa Fargate de 1 vCPU + 2 GB mantida continuamente ativa.

Em 10 milhões de execuções/mês, o modelo estima US$668,67/mês para Lambda contra US$288,32/mês para Fargate — uma diferença de aproximadamente US$380/mês.

O resultado não significa que Fargate seja "melhor". Significa que, para este perfil de carga de trabalho e estas premissas, a função de custo muda de lado.

## Impacto Potencial

No cenário analisado, a escolha entre as duas arquiteturas representa uma diferença estimada de aproximadamente US$380/mês, ou cerca de US$4,6 mil/ano.

Esse valor não representa economia realizada em produção. É o resultado de um modelo sob premissas explícitas. O objetivo não é afirmar que Fargate sempre será mais barato, mas mostrar que a arquitetura deveria ser reavaliada quando a carga, o padrão temporal ou o custo unitário mudam.

> **O erro não é escolher Lambda ou contêiner. É não recalcular a escolha quando o volume muda de ordem de grandeza — e volume mensal, sozinho, também não basta: a distribuição temporal da carga determina quanta capacidade o contêiner precisa manter.**

## Atualização Tecnológica

Em novembro de 2025, a AWS lançou o **Lambda Managed Instances**: funções Lambda executando sobre capacidade EC2 provisionada e gerenciada pelo serviço Lambda, incluindo provisionamento, escalonamento, patching e ciclo de vida. O modelo utiliza preços de EC2, permite aplicar Savings Plans e Reserved Instances ao compute subjacente e adiciona uma taxa de gerenciamento de 15% sobre o preço On-Demand da instância EC2. O serviço também passou a disponibilizar logs de sistema do capacity provider no CloudWatch, ampliando a visibilidade sobre eventos relacionados ao ciclo de vida e à capacidade gerenciada.

A diferença arquitetural importante é que Managed Instances não simplesmente substitui Fargate pelo Lambda: ele permite múltiplas invocações concorrentes no mesmo ambiente de execução, conforme a configuração e o runtime, alterando justamente a função de custo analisada nesta edição.

Isso não invalida o cálculo desta edição — mas move a fronteira da decisão. Lambda Managed Instances não é simplesmente uma terceira opção equivalente entre Lambda tradicional e Fargate: é a experiência de desenvolvimento serverless combinada ao modelo de preços e capacidade do EC2, especialmente relevante para cargas de trabalho de estado estável e previsível — justamente um dos perfis que torna essa discussão econômica ainda mais relevante. Por isso, é relevante para qualquer equipe que refaça essa análise a partir de agora: **a tecnologia evolui, mas a necessidade de calcular a função de custo antes da implantação continua exatamente a mesma.**



## Fontes e Referências

- [AWS Lambda Pricing](https://aws.amazon.com/lambda/pricing/) — preços de requisições e duração
- [AWS Fargate Pricing](https://aws.amazon.com/fargate/pricing/) — preços de vCPU/hora, memória/hora e Fargate Spot
- [AWS Lambda — Tiered Pricing](https://aws.amazon.com/blogs/compute/introducing-tiered-pricing-for-aws-lambda/) — descontos por volume de GB-segundos
- [AWS Lambda Managed Instances](https://aws.amazon.com/about-aws/whats-new/2025/11/aws-lambda-managed-instances/) — modelo de execução, concorrência e precificação

*Valores de referência: agosto/2026. Preços e percentuais de desconto da AWS estão sujeitos a alteração — consulte a documentação oficial para os valores vigentes.*



## Próxima edição

Docker, CI/CD e suíte de testes: quando essa esteira de MLOps é maturidade técnica — e quando é sobre-engenharia disfarçada de boas práticas em um projeto que ainda não chegou à produção.



*Engenharia de Decisões — arquitetura, trade-offs e evidências reais em Cloud, IA e Dados.*

