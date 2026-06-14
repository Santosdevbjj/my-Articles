


# Compra Facilitada, Fraude Garantida: Por Que as Empresas de Telecom Ainda Permitem que Golpistas Usem o Seu CPF

*Por Sérgio Luiz dos Santos — Especialista em Sistemas Críticos, Dados, Cloud e Segurança Digital*



> **"Compra facilitada, fraude garantida."**
> Esse ditado do mercado resume um problema recorrente no mercado brasileiro — e que as empresas de telecomunicações têm o poder, e a obrigação, de resolver.



## 1. O Problema de Negócio

Imagine receber uma cobrança no seu nome de uma operadora de internet que você nunca contratou. O modem foi instalado em outro endereço. O serviço foi usado por meses por um desconhecido. E a dívida que está "sujando" o seu CPF no Serasa é sua — mesmo que você jamais tenha assinado nada.

Esse cenário não é exceção. É rotina.

No setor de telecomunicações brasileiro, a **fraude de assinatura** — onde um estelionatário usa o CPF de outra pessoa para contratar internet, TV por assinatura ou pacotes de telefonia — é um dos vetores de prejuízo mais subestimados do mercado.

O problema central é estrutural: **empresas vendem serviços por telefone, pelo site e em lojas físicas aceitando apenas o nome e o CPF do cliente. Em muitos casos, os mecanismos de validação de identidade ainda são insuficientes para impedir o uso indevido de CPFs de terceiros. Qualquer pessoa com acesso a esses dois dados — facilmente encontrados em vazamentos na internet — pode contratar um serviço em nome de outra pessoa.**

Isso não decorre necessariamente de negligência individual, mas de processos de contratação que priorizam velocidade comercial em detrimento da validação robusta de identidade.

## 2. Contexto

Para entender a dimensão do problema, é preciso entender como funciona o ciclo da fraude de assinatura em telecom:

O golpista obtém nome e CPF de uma vítima — seja por banco de dados vazado, redes sociais ou engenharia social. Liga para a operadora ou acessa o site. Informa os dados. Solicita a instalação de internet em um endereço conveniente para ele. O vendedor, focado na comissão, não questiona. A venda é aprovada. O modem é instalado. O serviço é usado por meses, até o plano ser cancelado por inadimplência.

Resultado: a vítima acorda com o nome negativado. A empresa acumula prejuízos concretos: custo de instalação e deslocamento técnico, equipamentos entregues (modem, decodificador, antena), meses de sinal emitido sem receber, equipe de cobrança acionada, atendimento de suporte e, nos casos mais graves, exposição a processos judiciais por danos morais e sanções regulatórias da ANPD por falha na proteção de dados pessoais. O golpista segue para a próxima vítima.

> **Caso ilustrativo:** Considere uma vítima residente em Minas Gerais cujo CPF é utilizado para contratar internet banda larga em São Paulo. O cadastro registra um e-mail desconhecido, endereço sem qualquer vínculo com o histórico da vítima, e a validação telefônica é realizada por terceiro usando um chip pré-pago recém-ativado. Mesmo com todas essas inconsistências, o serviço é instalado e utilizado por meses sem pagamento. A vítima só descobre o que aconteceu ao ser notificada por uma plataforma de monitoramento de crédito de que seu nome foi negativado — por uma dívida que jamais contraiu. Esse tipo de ocorrência ilustra como falhas no onboarding geram prejuízos simultâneos para o consumidor e para a operadora, e como os sinais de alerta estavam presentes desde o início — mas nenhum sistema os capturou.

No Brasil, esse cenário é agravado por três fatores estruturais:

**Canais de venda pulverizados.** Operadoras e provedores terceirizam vendas para call centers, vendedores de rua e representantes comerciais que são remunerados por comissão de venda *instalada*. O incentivo desses profissionais é fechar a venda — não validar a identidade do cliente.

**Dados pessoais amplamente vazados.** Ao longo dos últimos anos, incidentes de vazamento de dados expuseram nome e CPF de um volume significativo de cidadãos brasileiros. Esses dados, sozinhos, não provam identidade.

**Ausência de automação de segurança no onboarding.** A maioria dos provedores regionais e uma parte significativa das grandes operadoras ainda não implementaram camadas automatizadas de verificação de identidade no processo de venda.



## 3. Premissas da Análise

Para a discussão que segue, algumas premissas são importantes:

> *No passado, o CPF identificava uma pessoa. Hoje, ele identifica apenas um registro em uma base de dados. Identidade digital exige muito mais do que isso.*

- O CPF e o nome completos **não são** credenciais de identidade suficientes. São dados cadastrais — e dados vazados.
- A fraude de assinatura em telecom é economicamente diferente da fraude em e-commerce: o golpista não compra um produto físico e desaparece — ele *usa* o serviço por meses, gerando prejuízo recorrente para a operadora.
- As tecnologias necessárias para resolver esse problema **já existem e estão disponíveis no mercado brasileiro** — o que falta é decisão de implementação.
- O objetivo não é eliminar a agilidade da venda. É fazer com que o sistema faça as verificações **por trás dos panos**, enquanto o vendedor continua atendendo normalmente.



## 4. Estratégia da Solução: O Funil de Segurança em 5 Camadas

A solução não é uma tecnologia isolada. É um funil estruturado onde cada camada elimina uma parcela dos golpistas — de forma que, se um passar pela primeira barreira, a segunda o barre. E assim sucessivamente.

### 4.1 Baseline Atual do Mercado

Antes de descrever o funil ideal, é importante entender onde o mercado está hoje.

Em grande parte das operadoras e provedores, a contratação de um serviço ocorre mediante informação de:

- Nome completo
- CPF
- Data de nascimento
- Endereço informado pelo próprio solicitante

Em muitos casos, **não há** biometria facial, prova de posse do telefone ou validação cruzada de identidade. O processo foi desenhado para privilegiar a velocidade da venda — o que, em um cenário de dados pessoais amplamente vazados, é equivalente a deixar a porta destrancada.

Esse é o baseline que o funil a seguir se propõe a superar.



### Fluxo do Funil de Segurança

```
Cliente solicita o serviço
         ↓
 Validação Cadastral (Bureau + Score de Fraude)
         ↓
 Quebra de Canal (Link no telefone vinculado ao CPF)
         ↓
 Biometria Facial com Liveness Check
         ↓
 Validação de Pagamento (Titular do cartão = Titular do CPF?)
         ↓
 Auditoria de Campo (Técnico + IA + Documento físico)
         ↓
 Ativação do Serviço
```

Cada etapa é uma barreira independente. Um golpista que burle a primeira encontra a segunda. A chance de passar por todas as cinco é estatisticamente reduzida a níveis muito baixos quando o funil está ativo.



### Arquitetura Conceitual da Solução

Para quem atua em tecnologia, o diagrama abaixo representa a visão de alto nível da integração entre os componentes do funil:

```
   Canal de Venda (Telefone / Site / Loja Física)
                        │
                        ▼
               CRM Comercial da Operadora
                        │
                        ▼
            ┌─── Motor Antifraude ───────────────┐
            │  ├── Bureau de Crédito             │
            │  │   (Serasa / Boa Vista /         │
            │  │    BigData Corp)                │
            │  ├── Score de Fraude               │
            │  ├── Validação Telefônica          │
            │  │   (Bureau de Operadoras)        │
            │  ├── Biometria Facial              │
            │  │   (Liveness Check)              │
            │  └── Motor de Regras de Negócio    │
            └────────────────────────────────────┘
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
         Aprovação           Reprovação
              │                   │
              ▼                   ▼
   Agendamento de          Alerta ao Vendedor
    Instalação             + Registro de Ocorrência
              │
              ▼
      Auditoria de Campo
    (Técnico + App + IA)
              │
              ▼
      Ativação do Serviço
```

Cada componente do Motor Antifraude é uma API independente, consultada em paralelo via arquitetura orientada a eventos. O resultado consolidado — aprovação ou reprovação — é devolvido ao CRM em tempo real, sem intervenção humana no caminho crítico.

> **Princípio arquitetural:** O funil segue princípios semelhantes ao modelo **Zero Trust** amplamente adotado em segurança cibernética: nenhuma identidade é considerada confiável até que seja continuamente validada por múltiplas evidências independentes. No contexto de telecom, isso significa que o fato de alguém conhecer um CPF não lhe concede nenhuma confiança implícita — cada etapa do funil exige uma nova prova de identidade, independente das anteriores.



### Camada 1 — O Filtro Invisível (Dados Cadastrais)

No momento em que o cliente informa o CPF, o sistema deve rodar, em milissegundos e de forma transparente para o vendedor, uma análise via bureaus de crédito e dados (Serasa Experian, Boa Vista, BigData Corp). Do ponto de vista de engenharia, isso exige uma arquitetura de microsserviços orientada a eventos, onde APIs REST integradas ao CRM consultam essas bases em paralelo, garantindo uma latência abaixo de 200ms para não impactar o tempo de atendimento:

**Cruzamento de dados:** O sistema valida se o nome e a data de nascimento informados pelo cliente correspondem ao CPF. Um erro de um dia na data de nascimento bloqueia a venda na hora.

**Score de Fraude:** Além de checar se o CPF está "limpo", o sistema calcula a probabilidade de aquela solicitação específica ser fraudulenta — com base no histórico de dados, padrões de comportamento e alertas de vazamento.

**Vínculo telefônico:** O sistema verifica se o número que está ligando tem histórico de associação com o CPF informado. Um chip pré-pago recém-ativado ligando em nome de um CPF de outra cidade é um sinal imediato de alto risco.

*Trade-off aceito:* Essa camada gera um custo por consulta. O custo de uma fraude instalada — equipamento, meses de sinal, equipe de recuperação de crédito — é ordens de magnitude maior.



### Camada 2 — A Quebra de Canal (Prova de Posse do Telefone)

Após a validação cadastral, o sistema executa uma estratégia chamada **quebra de canal**: o atendente informa ao cliente que um link de segurança foi enviado para o número de celular cadastrado no CPF dele junto às operadoras de telefonia.

O cliente precisa abrir esse link para a venda prosseguir.

O sistema consulta, em tempo real, a base das operadoras (Claro, Vivo, TIM) para verificar se aquele número está registrado no CPF do titular. Se o golpista estiver usando seu próprio celular, ele simplesmente não receberá o link no número da vítima. A venda não avança.

Essa camada, por si só, elimina a grande maioria dos golpistas oportunistas — aqueles que usam CPF alheio mas não têm controle sobre o telefone da vítima.



### Camada 3 — A Validação Biométrica (A Barreira Mais Robusta)

Se o cliente abre o link recebido, ele é direcionado para uma página segura no navegador — sem necessidade de instalar nenhum aplicativo. É aqui que entra a **biometria facial com Liveness Check** (Prova de Vida), fornecida por plataformas como Unico IDtech ou Facio:

O sistema solicita que o cliente tire uma selfie e realize um movimento facial simples (piscar, sorrir) — para garantir que não é uma foto estática ou uma máscara.

Essa imagem é cruzada instantaneamente com bancos de dados oficiais do governo (base do Denatran/CNH, RG Digital).

Se o rosto de quem está tentando contratar o serviço não corresponder ao titular do CPF, o sistema cancela o pedido imediatamente. O vendedor recebe um aviso: "Reprovado no Antifraude".

Nenhuma ação manual é necessária. Nenhum poder de decisão no vendedor. O sistema decide.



### Camada 4 — Cruzamento de Dados do Pagamento

Nos casos em que a contratação envolve taxa de adesão ou cobrança recorrente em cartão de crédito, entra uma camada adicional: o sistema verifica se o titular do cartão é o mesmo dono do CPF que está contratando o plano.

Se "João" está contratando o serviço, mas o cartão é de "Maria", a venda vai para análise manual ou é recusada automaticamente.

O sistema de antifraude especializado em transações digitais (como ClearSale ou Konduto) entra em ação para checar se o titular do cartão é o mesmo dono do CPF que está contratando o plano — executando essa verificação em segundos.



### Camada 5 — Auditoria de Campo (A Barreira Física)

Se um golpista altamente sofisticado conseguir passar pelas quatro camadas anteriores — o que é estatisticamente raro quando há biometria — a última barreira é o técnico de instalação.

O técnico utiliza um aplicativo interno da operadora que o obriga a, no momento da instalação: fotografar o documento original (RG ou CNH) do cliente, registrar o reconhecimento facial de quem está recebendo o técnico e coletar assinatura digital.

O sinal da internet só é liberado pela central após o sistema de inteligência artificial confirmar que os dados do documento e do rosto batem com a venda aprovada.



## 5. Insights: O Que os Dados Revelam Sobre Esse Problema

Analisando o padrão de fraudes em telecom, alguns insights são especialmente relevantes para quem atua em engenharia de dados e arquitetura de sistemas:

🎯 **A fraude não é aleatória — ela segue padrões:** Chips pré-pagos recém-ativados, endereços de instalação inconsistentes com o histórico do CPF e horários atípicos são sinais que um bom modelo de score de fraude captura antes de qualquer humano.

⚠️ **O canal de venda é o maior vetor de risco:** A fraude acontece porque o vendedor tem poder de aprovação. Retirar esse poder e transferi-lo para um sistema automatizado é a mudança mais impactante que uma operadora pode fazer.

🛑 **Dados públicos são armas nas mãos erradas:** Nome e CPF são apenas o ponto de partida de uma verificação, não o fim dela. Tratá-los como credenciais de autenticação é um erro conceitual grave.

⚖️ **A LGPD cria responsabilidade concreta:** Empresas que permitem que CPFs de terceiros sejam usados sem verificação estão sujeitas a sanções da ANPD. Isso transforma o problema de fraude em um problema de compliance regulatório — com consequências financeiras diretas.



## 6. Resultados: O Que Muda com o Funil de Segurança

### Impacto Financeiro da Fraude de Assinatura

Antes de apresentar o que muda com a solução, é importante tornar visível o que custa não fazer nada. Cada contratação fraudulenta gera custos diretos e indiretos para a operadora:

**Custos diretos por ocorrência:**
- Deslocamento e hora técnica de instalação — entre R$ 150 e R$ 400 por visita, dependendo da região
- Equipamentos entregues: modem, roteador, decodificador, antena — custo médio estimado entre R$ 300 e R$ 800 por kit
- Meses de sinal emitido sem receita — em planos de R$ 100 a R$ 200/mês, três meses de uso fraudulento representam entre R$ 300 e R$ 600 de receita perdida
- Equipe de cobrança e tentativas de recuperação de crédito — custo operacional adicional por ocorrência
- Atendimento de suporte ao longo do período de uso fraudulento

**Estimativa conservadora por ocorrência:** considerando custos médios de instalação, equipamentos, suporte e cobrança, uma estimativa conservadora sugere perdas diretas entre **R$ 800 e R$ 2.000 por ocorrência** — sem considerar os custos jurídicos e regulatórios.

**Riscos indiretos e regulatórios:**
- Processos judiciais movidos pela vítima por danos morais e negativação indevida — indenizações que costumam variar entre R$ 3.000 e R$ 10.000 por caso nos JECs
- Sanções da ANPD por falha no tratamento de dados pessoais (LGPD)
- Desgaste reputacional e perda de confiança do consumidor

**O efeito escala é o que torna o problema crítico.** Em uma operadora com 5.000 ativações mensais, uma taxa de fraude de apenas 1% representa 50 contratos fraudulentos por mês. A perdas diretas estimadas chegam a R$ 40.000–R$ 100.000 mensais — ou entre **R$ 480.000 e R$ 1,2 milhão por ano** — apenas em custos diretos, sem considerar litígios e sanções regulatórias. É exatamente nesse ponto que a discussão de segurança deixa de ser técnica e passa a ser estratégica.

A implementação estruturada das cinco camadas produz resultados mensuráveis em três dimensões:

**Para a empresa:** Redução drástica do custo de inadimplência fraudulenta — que inclui equipamento instalado, meses de sinal emitido, custo de recuperação de crédito e equipe de cobrança. Projetos de onboarding biométrico reportados por fornecedores especializados do setor, como Unico IDtech e Facio, costumam divulgar reduções significativas nos índices de fraude nos canais digitais — frequentemente superiores a 80%, dependendo do contexto operacional e do grau de maturidade do processo anterior à implementação.

**Para o cliente legítimo:** A experiência de contratação não muda de forma perceptível. O link de validação chega em segundos. A biometria é feita pelo próprio celular. O processo todo leva menos de dois minutos a mais — e garante que ninguém vai contratar um serviço no seu nome enquanto você dorme.

**Para o mercado:** A disseminação dessas práticas eleva o custo operacional do golpista a ponto de tornar a fraude de assinatura inviável como modelo. Quando todas as operadoras implementam biometria, o estelionatário não tem para onde ir.



### Indicadores de Sucesso: O Que Monitorar Após a Implantação

A gestão por resultado exige que a implementação do funil seja acompanhada por KPIs claros. Os indicadores prioritários para uma operadora que adota esse modelo:

| Indicador | O que mede |
|---|---|
| **Taxa de fraude no onboarding** | % de contratos identificados como fraudulentos sobre o total de ativações |
| **Redução de inadimplência fraudulenta** | Variação na inadimplência atribuída a contratos com inconsistências cadastrais |
| **Volume de chargebacks** | Estornos forçados por contestação de cobrança indevida |
| **Processos judiciais por negativação indevida** | Quantidade de ações movidas por vítimas de fraude de identidade |
| **Taxa de falso positivo** | % de clientes legítimos bloqueados incorretamente pelo sistema antifraude |
| **Tempo médio de onboarding** | Impacto das camadas de segurança na duração do processo de venda |
| **Confiabilidade do canal digital** | % de vendas digitais concluídas com validação biométrica bem-sucedida |

O objetivo não é apenas reduzir fraude — é fazê-lo sem degradar a experiência do cliente legítimo. O KPI de **falso positivo** é o indicador de equilíbrio: deve ser monitorado continuamente e mantido dentro dos limites definidos pela política de risco da organização, pois cada empresa possui apetite de risco diferente e contextos operacionais distintos.



### O Custo da Inação

Há uma decisão implícita que muitas operadoras tomam sem perceber: a de não agir.

Uma operadora que mantém processos de validação baseados apenas em CPF, nome e data de nascimento corre o risco de continuar absorvendo perdas financeiras recorrentes, aumento progressivo do volume de ações judiciais por negativação indevida, desgaste reputacional junto ao consumidor e maior exposição regulatória perante a ANPD. Em muitos casos, o custo acumulado da fraude ao longo dos anos supera — com folga — o investimento necessário para implementar controles modernos de identidade digital.

A inação não é neutra. Ela tem um preço — e esse preço cresce a cada ciclo de fraude não interrompido.



## 7. Próximos Passos: O Que Falta Para o Setor Evoluir

O caminho existe. O que falta é execução:

**Regulamentação setorial clara.** A Anatel e a ANPD precisam estabelecer requisitos mínimos de verificação de identidade para contratação de serviços de telecomunicações — assim como o Banco Central fez com o Open Finance e com as normas de prevenção à fraude no sistema financeiro.

**Auditoria de canais terceirizados.** Operadoras precisam implementar automação que cubra não apenas o canal digital, mas especialmente os canais de venda terceirizados — onde a maioria das fraudes acontece.

**Modelos preditivos de Score de Fraude com Machine Learning.** Ir além do score de crédito tradicional e construir modelos supervisionados específicos para detecção de fraude de assinatura, alimentados por dados comportamentais e de histórico de ativação. Modelos de Machine Learning podem complementar as regras estáticas, identificando padrões de fraude — por meio de feature engineering e detecção de anomalias — que não seriam percebidos por mecanismos tradicionais baseados apenas em regras de negócio. Essa camada analítica é onde Dados, IA e Segurança convergem de forma mais poderosa.

**Integração entre operadoras.** Um golpista bloqueado na Operadora A hoje pode tentar a mesma fraude na Operadora B amanhã. Compartilhamento de alertas entre players do setor — com os controles adequados de LGPD — reduziria drasticamente a reincidência.

**Observabilidade e rastreabilidade de ponta a ponta.** Todo o fluxo do funil de segurança deve ser auditável de ponta a ponta — com logs estruturados, rastreamento de cada decisão do Motor Antifraude e dashboards de monitoramento em tempo real. Isso não é apenas boa prática de engenharia: é um requisito regulatório. Em caso de investigação por fraude, negativação indevida ou ação judicial, a operadora precisa demonstrar evidências do processo de validação aplicado em cada contratação. Arquiteturas baseadas em microsserviços sem observabilidade adequada criam pontos cegos que comprometem tanto a segurança quanto o compliance.



## Considerações Finais

Durante anos de trabalho com sistemas de missão crítica no setor bancário, aprendi uma lição que se aplica diretamente a esse problema: **segurança que depende de julgamento humano em tempo real é segurança que vai falhar.**

O vendedor não é o problema. O sistema que coloca a decisão de segurança nas mãos do vendedor é o problema.

As telecomunicações têm hoje acesso às mesmas tecnologias de verificação de identidade que o sistema financeiro usa há anos — biometria facial, score de fraude, validação de bureau, quebra de canal. A diferença é que o setor financeiro foi obrigado a implementar essas camadas por pressão regulatória e por consequências financeiras diretas de cada fraude.

No telecom, o custo ainda é difuso o suficiente para que muitas empresas prefiram absorver o prejuízo a investir em prevenção. Isso muda quando o custo regulatório passa a superar o custo da fraude — ou quando os clientes passam a escolher operadoras que provam, com transparência, que protegem seus dados.

Esse momento está chegando. Empresas que implementarem hoje o funil de segurança que descrevo aqui não estarão apenas reduzindo fraude — estarão construindo uma vantagem competitiva real em um mercado onde confiança é, cada vez mais, um diferencial de produto.

**A verdadeira transformação digital não acontece quando uma empresa vende mais rápido. Ela acontece quando a empresa consegue vender rápido sem abrir mão da segurança. Em um mercado onde dados pessoais circulam amplamente na internet, identidade digital deixou de ser um diferencial tecnológico e passou a ser um requisito básico de confiança.**

As empresas de telecomunicações não precisam inventar uma nova tecnologia para combater esse tipo de fraude. Elas precisam apenas aplicar, ao processo de contratação, o mesmo nível de proteção que já consideramos absolutamente normal quando alguém acessa sua conta bancária pelo celular.

Se um banco consegue confirmar a identidade de um cliente para movimentar milhares de reais em segundos, por que uma operadora ainda aceita contratar serviços apenas com nome, CPF e data de nascimento? A tecnologia necessária já existe. O que separa as empresas protegidas das vulneráveis não é inovação tecnológica — **é prioridade estratégica.**




**E no seu provedor ou operadora, como esse onboarding é feito hoje? O fator humano ainda é a única barreira ou a automação já tomou conta? Deixe sua opinião nos comentários!**



*#Telecomunicações #Antifraude #SegurançaDeDados #LGPD #EngenhariadeDados #IdentidadeDigital #Biometria #DataEngineering*



