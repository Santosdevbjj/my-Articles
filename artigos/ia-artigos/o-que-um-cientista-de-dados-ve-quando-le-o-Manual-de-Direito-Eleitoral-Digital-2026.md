# O que um cientista de dados vê quando lê o Manual de Direito Eleitoral Digital 2026

**Como analisei a nova arquitetura regulatória de IA nas eleições brasileiras com as mesmas perguntas que faço a qualquer sistema em produção**



## 1. Problema

A inteligência artificial já entrou na cadeia de produção e distribuição de conteúdo eleitoral, e as Eleições 2026 introduzem regras específicas para lidar com esse uso. Para quem produz essas peças — campanhas, agências, assessorias —, o valor de negócio por trás desta análise é direto: **criar controles capazes de reduzir a exposição a riscos de conformidade**, inclusive aqueles que podem produzir consequências como multas ou cassação nas hipóteses previstas pela regulamentação, tratando compliance como um problema de engenharia de dados resolvido antes da publicação, não como uma reação a notificações que já chegaram.

O problema é que tratar IA generativa apenas como ferramenta de produção de conteúdo ignora outra dimensão: quando esse conteúdo entra no ambiente eleitoral, passam a importar também transparência, rastreabilidade, evidência, governança e conformidade. É a mesma diferença entre rodar um modelo em notebook e colocar esse modelo em produção com logging, versionamento e SLA. Quem só sabe operar a ferramenta, e não documentar o processo, aumenta sua exposição a riscos de conformidade: dependendo do enquadramento jurídico da conduta, podem existir consequências como remoção do conteúdo, multa e, nas hipóteses previstas pelo art. 9º-C, §2º, consequências relacionadas a abuso do poder político e uso indevido dos meios de comunicação, inclusive cassação de registro ou mandato.

Analisei o Manual de Direito Eleitoral Digital 2026, do advogado especializado em Direito Digital Alexandre Atheniense, justamente para entender essa arquitetura — não como jurista, mas com o olhar de quem constrói e audita pipelines de dados.

## 2. Contexto

A regulação de IA eleitoral no Brasil não nasceu de uma lei federal específica. Nasceu de resoluções do TSE, editadas por ciclo eleitoral:

- **27/02/2024** — Resolução TSE nº 23.732/2024 insere os arts. 9º-B a 9º-H na Resolução nº 23.610/2019. O Manual trata esse momento como o marco inicial da regulação específica de IA eleitoral: dever de rotulagem, vedação a deepfake, responsabilidade de provedores.
- **02/03/2026** — Resolução TSE nº 23.755/2026 aprofunda a disciplina para o pleito atual: cria a "janela de silêncio"; permite ao juiz, motivadamente, inverter o ônus da prova quando a dificuldade técnica de comprovação da manipulação digital tornar excessivamente onerosa a demonstração da irregularidade; cria obrigações de plano de conformidade para provedores de aplicação de internet abrangidos pela norma (art. 125-B); e estabelece restrições para provedores que ofertem sistemas de inteligência artificial em relação à recomendação e ao favorecimento político-eleitoral.
- **08/05/2026** — em precedente envolvendo manipulação digital no caso de Fortaleza/CE (vídeo com montagem de Barack Obama, Taylor Swift, Tom Cruise e Cristiano Ronaldo simulando apoio a uma candidatura), o TSE reafirmou a natureza objetiva da vedação do art. 9º-C: a adulteração de conteúdo digital com finalidade eleitoral pode caracterizar a irregularidade independentemente da comprovação de potencialidade para induzir o eleitor em erro.
- **27/07/2026** — Portaria TSE nº 463/2026 regulamenta o art. 125-B, detalhando as categorias de provedores abrangidos (redes sociais, mensageria, hospedagem de vídeo, mecanismos de busca e chatbots de IA generativa, entre outros), o conteúdo exigido dos planos de conformidade, indicadores e prazos de acompanhamento. Entre os provedores sujeitos à obrigação, aqueles com mais de 5 milhões de usuários ativos mensais no Brasil tiveram até 16/08/2026 para apresentar seus planos; a Portaria também permite ao TSE determinar plano simplificado para provedores abaixo desse limite quando suas funcionalidades, alcance ou relevância produzirem impacto significativo sobre a integridade do processo eleitoral.
- **01/09/2026** — o TSE julgou o caso do vídeo exibido na convenção do PL (Jair Bolsonaro recriado por IA declarando apoio a Flávio Bolsonaro). Por 5 votos a 2, fixou os critérios técnicos do que conta como deepfake; por 4 votos a 3, rejeitou o pedido de multa, por entender que a transmissão de convenção partidária não converte automaticamente manifestação interna em propaganda eleitoral antecipada.


Esse último julgado é o dado jurisprudencial mais recente desta análise: ele estabelece, para as Eleições 2026, critérios expressos para a caracterização de deepfake que o Manual — fechado em agosto — só conseguia discutir como hipótese em aberto.

## 3. Premissas

Para este artigo, tomei como fonte primária o texto do Manual e, para os eventos posteriores ao seu fechamento, fontes oficiais do TSE (notícias, resoluções e a Portaria nº 463/2026). Onde o Manual registra crítica doutrinária a um dispositivo (a janela de silêncio, a vedação de neutralidade da IA), tratei isso como *crítica de terceiros documentada*, não como posição consolidada — é assim que o próprio autor trata essas passagens, e mantive a mesma cautela. Onde a fonte disponível não permitia confirmar um dado com precisão (como a identificação processual exata de um julgamento de maio de 2026), optei por uma formulação mais genérica em vez de arriscar um número de processo ou data que não pude confirmar de forma cruzada — o detalhe está registrado na seção de Limitações.

Este artigo não pretende substituir uma análise jurídica nem oferecer orientação para campanhas. A proposta é outra: observar como regras eleitorais relacionadas à IA podem ser traduzidas, do ponto de vista de Ciência de Dados e Engenharia de Sistemas, em critérios, controles, evidências e processos de governança.

### Baseline: como o compliance costuma ser tratado hoje

Para fins desta análise, adoto como baseline uma abordagem manual e reativa — **checagem acionada apenas quando uma notificação do TSE ou uma representação já chegou** — não porque seja necessariamente a prática dominante em campanhas (isso não foi medido aqui), mas porque representa o ponto de partida mais simples contra o qual comparar um pipeline contínuo. A pergunta que orienta essa prática costuma ser binária: "a IA pode ou não pode ser usada?"

Esse baseline tem duas limitações: reduz um problema multidimensional a uma variável binária, e desloca a análise para depois da publicação, quando parte da evidência já pode ter se perdido. A leitura do Manual sugere uma alternativa — um pipeline contínuo, anterior à publicação — que depende de pelo menos oito variáveis analisadas em conjunto:

```
Tecnologia
   ↓
Tipo de conteúdo
   ↓
Pessoa representada
   ↓
Realismo/verossimilhança
   ↓
Finalidade eleitoral
   ↓
Momento da publicação
   ↓
Forma de divulgação
   ↓
Evidência disponível
```

É essa cadeia — e não uma checagem reativa pós-publicação — que a análise a seguir tenta reconstruir.

## 4. Estratégia de análise

Apliquei ao Manual a mesma pergunta que aplico a qualquer sistema antes de aprovar um deploy: **onde estão os pontos de decisão, e o que cada um deles exige como evidência?**

O resultado é uma árvore de decisão conceitual que pode ser utilizada por equipes técnicas e jurídicas como checklist preliminar antes da publicação de uma peça:

```
Conteúdo utiliza IA ou tecnologia equivalente?
│
├── Não
│   └── Aplicam-se as regras eleitorais gerais (dever de diligência, art. 9º)
│
└── Sim
    │
    ├── O uso está dentro de alguma hipótese de exceção à rotulagem?
    │   └── Sim → verificar demais regras aplicáveis
    │
    └── Não
        │
        ├── É conteúdo sintético sujeito à rotulagem (art. 9º-B)?
        │   └── Sim → validar rotulagem (cumpre o dever de transparência)
        │
        └── Continuar análise, independentemente da rotulagem
            │
            └── O conteúdo cria, reproduz ou altera imagem, voz
                ou manifestação de pessoa viva, falecida ou fictícia?
                    │
                    ├── Não → verificar demais regras aplicáveis
                    │
                    └── Sim
                        │
                        └── Possui grau de realismo/verossimilhança?
                               │
                               ├── Não → não satisfaz esse elemento
                               │         da definição de deepfake
                               │
                               └── Sim
                                    │
                                    └── É caracterizado como propaganda eleitoral?
                                           │
                                           ├── Não → a vedação específica do art. 9º-C
                                           │         não incide, conforme a tese
                                           │         fixada em 01/09/2026
                                           │
                                           └── Sim → vedação de deepfake (art. 9º-C),
                                                     mesmo com rotulagem e autorização
```

Note que a rotulagem não encerra o fluxo: ela cumpre um requisito de transparência, mas a análise sobre a incidência das demais regras aplicáveis continua adiante, de forma independente. Essa árvore é uma abstração operacional para organizar as perguntas que precisam ser respondidas antes da publicação — ela não substitui a interpretação jurídica, e algumas de suas ramificações (como "é propaganda eleitoral?") dependem de análise contextual, não de checagem automática.

E é justamente aqui que a formação de cientista de dados ajuda, com uma ressalva importante: parte da regulação se comporta como regra determinística de negócio (prazo, rotulagem, proibição de publicação), e parte depende de classificação contextual — especialmente a caracterização do conteúdo como propaganda eleitoral — enquanto outros critérios, como o grau de realismo ou verossimilhança, integram a caracterização do conteúdo segundo os critérios fixados pelo TSE. O julgamento de 01/09/2026 deixou isso explícito ao definir que a análise da natureza do ato comunicativo depende de conteúdo, linguagem, destinatários e contexto — não de um teste binário.

## 5. Insights

**A rotulagem e a licitude são dois testes independentes.** Este é um dos pontos em que a distinção costuma ser mais importante. Marcar um conteúdo como "gerado por IA" cumpre o dever de transparência do art. 9º-B, mas não neutraliza a vedação do art. 9º-C. É como confundir *documentação de um modelo* com *aprovação de compliance* — são etapas diferentes do mesmo pipeline, e uma não substitui a outra.

```
Rotulagem
   ↓
Transparência

Licitude
   ↓
Conformidade com as regras aplicáveis
```

**A definição de deepfake e a incidência da vedação podem ser organizadas, para fins desta análise, em dois grupos de critérios.** Essa organização em dois grupos é uma abstração analítica proposta neste artigo para facilitar a leitura — não uma divisão formal feita pelo TSE. O primeiro grupo descreve as características do conteúdo sintético; o segundo corresponde à condição de incidência da vedação: sua caracterização como propaganda eleitoral.

O precedente de 08/05/2026 (caso Fortaleza/CE) fixou a natureza objetiva da vedação: não é preciso provar que o eleitor foi enganado, basta a adulteração com finalidade eleitoral. Mas o caráter objetivo não elimina a necessidade de qualificar previamente o fato — antes de aplicar a consequência objetiva do art. 9º-C, é necessário determinar se o conteúdo satisfaz os elementos definidos pela norma e pelo precedente.

O julgamento de 01/09/2026 tornou esses elementos explícitos:

- o conteúdo deve ser **sintético**, produzido ou manipulado por IA ou tecnologia equivalente;
- deve possuir **grau de realismo ou verossimilhança**;
- deve **criar, reproduzir ou alterar** imagem, voz ou manifestação de pessoa viva, falecida ou fictícia;
- e a vedação exige que o conteúdo seja **caracterizado como propaganda eleitoral**.

No caso do vídeo exibido na convenção do PL, o conteúdo apresentava características compatíveis com a definição de deepfake, mas a incidência da vedação do art. 9º-C dependia também de sua caracterização como propaganda eleitoral. O TSE entendeu que a transmissão da convenção, por si só, não transformava a manifestação de apoio político dirigida aos convencionais em propaganda eleitoral antecipada — e, por isso, rejeitou o pedido de multa.

```
Características do conteúdo
          +
Contexto de comunicação
          ↓
Classificação jurídica
```

Regra objetiva, portanto, não significa classificação automática: significa que, uma vez qualificado o fato, não cabe defesa alegando que o eleitor não foi enganado.

```
SISTEMA REGULATÓRIO
                            │
              ┌─────────────┴─────────────┐
              │                           │
      REGRAS DETERMINÍSTICAS      CLASSIFICAÇÃO CONTEXTUAL
              │                           │
     prazo / rotulagem              propaganda eleitoral
     proibição / registro           linguagem / destinatário
     publicação                     contexto / finalidade
              │                           │
              └─────────────┬─────────────┘
                            ↓
                     DECISÃO DE COMPLIANCE
```

**Para a vedação específica do art. 9º-C, a autorização da pessoa retratada não funciona como exceção à regra** — inclusive quando concedida por herdeiros, no caso de pessoa falecida. Na linguagem de engenharia de requisitos, ela não deve ser tratada como um mecanismo capaz de transformar uma condição proibida em condição permitida.

**A "janela de silêncio" é uma restrição temporal aplicada a uma classe específica de novos conteúdos sintéticos.** Nas 72 horas antes e 24 horas depois do fim do pleito, publicação, republicação e impulsionamento pago de novo conteúdo sintético que utilize imagem, voz ou manifestação de candidato ou pessoa pública ficam vedados — mesmo que o conteúdo esteja corretamente rotulado. A regulamentação brasileira estabelece uma restrição temporal específica para esse período crítico, diferente de modelos internacionais analisados pelo Manual, como o da Coreia do Sul, que adota uma janela de 90 dias.

Há ainda uma questão interpretativa relevante: a norma utiliza a expressão "término do pleito", e o Manual registra que o alcance da vedação no intervalo entre eventual primeiro e segundo turnos ainda não havia sido definido pela Justiça Eleitoral na data de seu fechamento.

**A arquitetura regulatória de 2026 também alcança provedores que oferecem sistemas de inteligência artificial.** O art. 28, §1º-C estabelece vedações para provedores de aplicação que ofertem sistemas de inteligência artificial, impedindo-os, mesmo quando solicitados pelo usuário, de ranquear, recomendar ou priorizar candidaturas, emitir opiniões ou indicar preferência eleitoral, recomendar voto ou realizar favorecimento ou desfavorecimento político-eleitoral, entre outras condutas previstas no §1º-C. É uma restrição imposta ao provedor da aplicação, não diretamente ao usuário — uma característica relevante da arquitetura regulatória de 2026 e, como o próprio Manual reconhece, ainda não testada contra provedores sediados no exterior.

## 6. Decisões Técnicas e Trade-offs

Vale destacar as escolhas de desenho que a regulação fez — tratadas aqui como hipóteses sobre o efeito de cada escolha, não como consequências já demonstradas:

- **Natureza objetiva em vez de subjetiva (art. 9º-C):** a natureza objetiva da vedação elimina a necessidade de demonstrar que o eleitor efetivamente foi enganado. Do ponto de vista de desenho regulatório, isso pode aumentar a objetividade da aplicação, mas não elimina a necessidade de qualificar o contexto para determinar se a norma incide — como mostrou o próprio julgamento de 01/09/2026.
- **Dois grupos de critérios para organizar a análise, em vez de um único bloco de condições (decisão de 01/09/2026):** ao exigir "realismo/verossimilhança" *e* "caracterização como propaganda eleitoral", esse desenho pode reduzir falsos positivos em situações como convenções internas e materiais de bastidor, mas também cria uma zona de disputa: qualificar o que conta como propaganda eleitoral antecipada passa a exigir análise de conteúdo, linguagem, destinatários e contexto — introduzindo uma etapa de classificação contextual dentro de uma regra cuja consequência jurídica é objetiva.
- **Janela temporal curta (72h+24h) em vez de longa (modelo coreano, 90 dias):** a regra concentra a restrição temporal em uma janela específica próxima ao encerramento do pleito. Uma consequência possível desse desenho é deslocar determinadas estratégias de publicação para períodos anteriores à janela — hipótese que merece acompanhamento empírico, não uma conclusão já demonstrada.
- **Árvore de decisão com regras determinísticas e validação contextual, em vez de um classificador de linguagem para "propaganda eleitoral":** um modelo treinado para automatizar essa classificação enfrentaria alto risco de falsos positivos e falsos negativos em um cenário de desfecho severo (cassação de registro ou mandato), além de não existir hoje uma base de precedentes suficiente para validar esse tipo de classificador. A opção por uma árvore explícita, com pontos de decisão que exigem revisão humana nas etapas contextuais, prioriza auditabilidade sobre automação total — um trade-off de arquitetura, não apenas de leitura da norma.
- **Plano de conformidade obrigatório para provedores abrangidos pelo art. 125-B:** estabelecendo requisitos mensuráveis, prazos e mecanismos de acompanhamento, além de vinculá-lo aos requisitos regulatórios aplicáveis ao credenciamento e cadastro previstos na Resolução. A regulamentação posterior, pela Portaria TSE nº 463/2026, detalhou esse mecanismo — categorias de provedores abrangidos, conteúdo dos planos, indicadores e procedimentos de acompanhamento.

## 7. Da norma ao pipeline de dados

Do ponto de vista de Engenharia de Sistemas, a consequência prática para uma equipe que produz conteúdo eleitoral não é apenas "obedecer à lei". É transformar requisitos jurídicos em controles operacionais:

```
REQUISITO JURÍDICO
       ↓
REGRA DE NEGÓCIO
       ↓
CLASSIFICAÇÃO
       ↓
CONTROLE TÉCNICO
       ↓
LOG
       ↓
EVIDÊNCIA
       ↓
AUDITORIA OU REVISÃO DE CONFORMIDADE
       ↓
DECISÃO
```

A etapa de classificação existe porque, como visto na seção anterior, parte dos critérios é determinística (prazo, rotulagem) e parte depende de avaliação contextual (propaganda eleitoral, realismo) — e essa distinção precisa ser resolvida antes de qualquer controle técnico ser aplicado.

| Requisito | Controle técnico |
|---|---|
| Uso de IA | Registro da ferramenta |
| Conteúdo sintético | Identificação da técnica |
| Rotulagem | Validação antes da publicação |
| Pessoa representada | Identificação e classificação da pessoa retratada |
| Contexto eleitoral | Classificação preliminar da finalidade e do público |
| Data de publicação | Controle temporal |
| Aprovação | Registro do responsável |
| Evidência | Armazenamento do original |
| Alteração | Versionamento |
| Publicação | Registro de URL/data |
| Contestação | Preservação da cadeia de evidências |

## 8. Resultado da análise: transformar norma em controle operacional

Sob uma perspectiva de governança, guardar prompt, ferramenta usada, versão original, data de produção, aprovação e rotulagem aplicada não precisa ser tratado como mera burocracia — é análogo à trilha de auditoria que sistemas de ML em produção precisam manter quando estão sujeitos a requisitos de rastreabilidade e governança, em um ambiente regulatório no qual determinadas violações podem resultar em remoção, multa e, nas hipóteses previstas no art. 9º-C, §2º, consequências como cassação.

A pergunta operacionalmente mais útil não é apenas "posso usar IA?". É: "o que exatamente estou produzindo, com qual tecnologia, em que contexto, envolvendo quem, quando será publicado, quais regras se aplicam e que evidências serão capazes de demonstrar o que aconteceu?"

Essa pergunta fecha o ciclo: dados → modelo → conteúdo → publicação → evidência → auditoria → decisão.

### Custo do risco: por que isso importa para o negócio

Sob a ótica de custo de risco (*risk exposure*), a ausência de um pipeline de rastreabilidade transforma uma falha de conformidade evitável em exposição concreta: multas de R$ 5.000 a R$ 30.000 por veiculação irregular (art. 57-D da Lei nº 9.504/97) e, nas hipóteses do art. 9º-C, §2º, risco de cassação de registro ou mandato.

Um pipeline com registro de ferramenta, versionamento e evidência preservada não elimina esse risco — nenhuma arquitetura técnica substitui análise jurídica —, mas reduz a fricção operacional de responder a uma notificação ou representação: em vez de reconstruir sob pressão de prazo a origem de uma peça já publicada, a equipe consulta um registro estruturado desde o início. Essa é uma hipótese de desenho, coerente com a disciplina do artigo até aqui, não um resultado medido nesta análise.

## 9. Limitações da análise

1. **Este artigo não substitui análise jurídica.** É uma leitura interdisciplinar de uma norma em vigor, não um parecer para uso em caso concreto.
2. **O Manual tem data de fechamento.** A edição usada como fonte principal foi fechada em 05/08/2026, conforme informado pelo próprio autor.
3. **A jurisprudência continua evoluindo.** O julgamento de 01/09/2026 já demonstrou isso — e outros pontos da norma, como a vedação de neutralidade da IA (art. 28, §1º-C) contra provedores estrangeiros, ainda não foram testados em caso concreto, segundo as fontes consultadas até 19/09/2026.

Este artigo é, portanto, uma fotografia de uma arquitetura regulatória em movimento.

## 10. Próximos passos

- Acompanhar como a Justiça Eleitoral vai calibrar, na prática, os critérios de "propaganda eleitoral antecipada" fixados em 01/09/2026 — é uma das questões que merece acompanhamento mais próximo.
- Acompanhar como a vedação do art. 28, §1º-C será aplicada na prática, especialmente em relação a provedores estrangeiros, cuja atuação apresenta desafios adicionais de jurisdição e execução.
- Revisitar este artigo após o primeiro turno, quando a janela de silêncio de 2026 for aplicada pela primeira vez em caso concreto.



## Fontes e referências

**Fonte principal**
- ATHENIENSE, Alexandre. *Manual do Direito Eleitoral Digital: Eleições 2026*. 3ª ed. Belo Horizonte: Alexandre Atheniense Advogados, 2026. Disponível em: [alexandreatheniense.com.br](https://www.alexandreatheniense.com.br/) e [info.alexandreatheniense.com.br/manual-eleitoral-digital-2026](https://info.alexandreatheniense.com.br/manual-eleitoral-digital-2026)

**Precedente jurisprudencial (08/05/2026)**
- TSE. Ac. de 8/5/2026 no AgR-REspEl nº 060020163, rel. Min. Ricardo Villas Bôas Cueva. Precedente sobre manipulação digital, deepfake eleitoral (vídeo com Barack Obama, Taylor Swift, Tom Cruise e Cristiano Ronaldo) e natureza objetiva da vedação do art. 9º-C, §1º, da Resolução nº 23.610/2019. Disponível em: [temasselecionados.tse.jus.br — Propaganda eleitoral / Internet / Redes sociais](https://temasselecionados.tse.jus.br/temas-selecionados/propaganda-eleitoral/internet/redes-sociais) e [acórdão em PDF](https://sjur-servicos.tse.jus.br/sjur-servicos/rest/download/pdf/3513709)

**Atualização jurisprudencial (setembro de 2026)**
- TSE. *TSE fixa tese sobre deepfake e delimita regra para as Eleições 2026*. Notícia publicada em 01/09/2026, atualizada em 02/09/2026. Disponível em: [tse.jus.br — TSE fixa tese sobre deepfake](https://www.tse.jus.br/comunicacao/noticias/2026/Setembro/tse-fixa-tese-sobre-deepfake-e-delimita-regra-para-as-eleicoes-2026) — Processo relacionado: Representação nº 0601315-97.2026.6.00.0000

**Normas e regulamentação**
- Lei nº 4.737/1965 (Código Eleitoral)
- Lei nº 9.504/1997 (Lei das Eleições), art. 57-D
- Resolução TSE nº 23.610/2019, texto compilado com as alterações posteriores, especialmente as promovidas pelas Resoluções nº 23.732/2024 e nº 23.755/2026. Consultar o texto compilado em: [tse.jus.br](https://www.tse.jus.br/)
- Portaria TSE nº 463, de 27 de julho de 2026 — regulamenta o art. 125-B da Resolução nº 23.610/2019/TSE. Disponível em: [tse.jus.br — Portaria nº 463/2026](https://www.tse.jus.br/legislacao/compilada/prt/2026/portaria-no-463-de-27-de-julho-de-2026)

**Outros recursos oficiais do TSE**
- Eleições 2026: [tse.jus.br/eleicoes/eleicoes-2026](https://www.tse.jus.br/eleicoes/eleicoes-2026)
- Cartilha de Boas Práticas — Inteligência Artificial (PDF): [tse.jus.br](https://www.tse.jus.br/comunicacao/arquivos/cartilha-de-boas-praticas-inteligencia-artificial/@@display-file/file/cartilha-de-boas-praticas-inteligencia-artificial.pdf)
- Temas Selecionados — Inteligência Artificial e Deepfake: [Inteligência Artificial](https://www.tse.jus.br/list-subjects?subjects=Intelig%C3%AAncia%20artificial) · [Deepfake](https://www.tse.jus.br/list-subjects?subjects=Deepfake)



#inteligenciaArtificial ​#ArtificialIntelligence #IA #AI #data





