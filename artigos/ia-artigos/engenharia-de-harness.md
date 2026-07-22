# Engenharia de Harness: por que trocar de modelo pode não resolver o seu problema de IA

*Ao longo dos meus estudos e práticas com IAs generativas, percebi um padrão recorrente no mercado: times gastando semanas discutindo se deveriam usar GPT, Claude ou Llama, enquanto o sistema continuava falhando em produção. Escrevi este artigo para sintetizar o que venho estudando sobre Engenharia de Harness e mostrar por que o diferencial competitivo raramente está na escolha da API — está no ecossistema que construímos ao redor dela.*

## O problema que ninguém nomeia

Muitas empresas investem tempo e dinheiro para escolher o melhor modelo de IA e descobrem, meses depois, que o maior problema nunca foi o modelo. Foi a arquitetura construída ao redor dele.

Esse cenário é especialmente comum em empresas que estão começando a integrar agentes de IA em processos de desenvolvimento, atendimento, operações ou automação de tarefas de negócio.

O ciclo se repete com frequência: a demonstração impressiona, a promessa é grande, e a implantação em produção decepciona. O agente que resolvia tudo no protótipo passa a falhar de forma imprevisível assim que encontra um caso real, um contexto ambíguo ou uma tarefa mais longa.

O erro mais comum é diagnosticar isso como "problema de modelo" — e sair em busca da próxima versão, do próximo LLM mais forte. Mas os dados apontam para outro lugar: a variável que mais explica a diferença entre um agente que impressiona e um agente que entrega não é o modelo. É o que existe ao redor dele.

## O conceito: Agente = Modelo + Harness

Harness é um termo emprestado do vocabulário equestre — o conjunto de rédeas, sela e freio que direciona um cavalo. O modelo de IA é o cavalo: rápido e poderoso, mas sem rumo próprio. O harness é a estrutura que direciona esse poder para um resultado confiável.

Formalizado por Martin Fowler e Birgitta Böckeler (Thoughtworks) em 2026, o conceito resume-se a uma equação simples: **Agente = Modelo + Harness**. Um agente de IA não é apenas um LLM — é o modelo somado a todo o sistema de instruções, ferramentas, memória, validações e controles construído ao redor dele.

## Como o harness funciona na prática

A engenharia de harness se organiza em dois mecanismos complementares:

- **Guias (feedforward):** orientam o agente *antes* de agir. Um arquivo `AGENTS.md` com as regras do projeto, documentação embutida no repositório, servidores de linguagem que informam a estrutura do código — tudo isso aumenta a chance de o agente acertar já na primeira tentativa.
- **Sensores (feedback):** observam o agente *depois* de agir e permitem autocorreção. Linters, testes automatizados e revisões de código por IA funcionam como controle de qualidade contínuo, especialmente quando emitem sinais já formatados para que o próprio agente os interprete.

Esses controles ainda se dividem entre **computacionais** — determinísticos, rápidos, rodando em milissegundos (testes, verificadores de tipo) — e **inferenciais** — mais lentos, mas capazes de julgamento semântico que uma regra fixa não alcança.

## Um exemplo simples

Imagine um agente responsável por abrir chamados de suporte técnico.

O modelo, sozinho, consegue interpretar a mensagem do cliente e redigir uma resposta coerente. Mas é o harness que decide:

- quais APIs podem ser chamadas para consultar o sistema de tickets;
- quais dados do cliente podem ser acessados e quais são sensíveis demais;
- quando o caso exige aprovação humana antes de qualquer ação;
- quais testes automáticos validam a resposta antes de ela sair;
- como cada decisão é registrada para auditoria posterior.

Tire o harness da equação e o que resta é um modelo respondendo texto sem noção de permissão, contexto ou consequência. É a diferença entre um sistema que apenas responde perguntas e um sistema capaz de executar ações com segurança, rastreabilidade e governança.

## A evidência que muda a decisão de investimento

Se a tese fosse só teórica, seria discutível. Mas há um dado concreto: a LangChain publicou o salto de seu agente de codificação do Top 30 para o Top 5 em um benchmark internacional de referência — de 52,8% para 66,5% de performance — **sem trocar o modelo**. A mudança foi inteiramente no harness.

O resultado sugere que, em muitos cenários, investir apenas na troca do LLM pode gerar menos retorno do que investir na arquitetura construída ao redor dele.

A Anthropic chegou a conclusão semelhante: a forma como a infraestrutura ao redor do agente é configurada altera os resultados de codificação em múltiplos pontos percentuais, independentemente do modelo escolhido.

Isso reposiciona a pergunta que toda liderança técnica faz ao decidir adotar IA. Não é "qual modelo vamos usar" — é "que sistema vamos construir ao redor dele".

## A diferença em uma imagem

**Sem harness**

```
Prompt → LLM → Resposta
```

**Com harness**

```
Prompt → Harness → LLM → Resposta validada
              │
              ├── Contexto
              ├── Ferramentas
              ├── Memória
              ├── Guardrails
              ├── Avaliação
              └── Observabilidade
```

A resposta final não muda de natureza — continua sendo texto gerado por um modelo. O que muda é tudo que acontece antes e depois dela.

Em outras palavras, o harness deixa de ser um detalhe de implementação e passa a ser uma decisão de arquitetura. E decisões de arquitetura produzem impactos que vão muito além da qualidade do código.

## Decisões técnicas e trade-offs

Nem todo projeto precisa de um harness ultra complexo. A decisão de arquitetura envolve avaliar o contexto:

- **Para PoCs e chatbots de Q&A simples:** um harness leve — system prompt refinado mais um guardrail básico — costuma ser suficiente. Implementar orquestradores em grafo ou suítes de avaliação pesadas aqui gera complexidade desnecessária e aumenta a latência sem ganho proporcional.
- **Para agentes de produção** — coding agents, automação financeira, logística: o harness completo se torna praticamente obrigatório. Aceita-se maior latência e custo inicial de desenvolvimento em troca de previsibilidade, auditabilidade e segurança.

Um harness mais sofisticado também aumenta a complexidade do sistema. O desafio da engenharia está em equilibrar custo, governança, desempenho e manutenibilidade de acordo com o contexto de cada aplicação — "mais harness" nem sempre é a decisão certa.

## Impacto de negócio, não apenas técnico

Um harness bem projetado não é luxo de engenharia. Quando o contexto justifica seu uso, ele se traduz em resultado operacional mensurável — e, em muitos casos, em economia direta.

**Do erro de diagnóstico à economia real.** Trocar de modelo — migrar para um LLM mais caro em busca de mais precisão — pode inflar a fatura de nuvem em milhares de reais por mês sem resolver a causa raiz da falha. O investimento em harness costuma trazer retorno mais direto:

- **Potencial redução do custo de inferência:** em muitos cenários, um bom harness permite usar modelos menores e mais baratos, porque a camada de validação e contexto compensa parte da "falta de inteligência bruta" do modelo.
- **Economia de horas-engenheiro:** um sensor de feedback automatizado que corrige a saída do agente evita que um desenvolvedor sênior gaste horas por semana revisando manualmente código ou respostas alucinadas.

Além do retorno financeiro, há ganhos operacionais menos óbvios, mas igualmente relevantes:

- **Confiabilidade em produção:** um agente pode funcionar muito bem em demonstrações e ainda assim falhar de forma imprevisível quando entra em produção; com guias e sensores, ele mantém coerência em tarefas longas.
- **Menos retrabalho humano:** quando o harness faz a triagem de erros, a equipe para de revisar linha a linha e passa a atuar em arquitetura e priorização — trabalho de maior valor.
- **Rastreabilidade e governança:** em contextos regulados, o harness registra o que o agente fez, por quê e com qual resultado, o que facilita auditoria e dá visibilidade à liderança.
- **Escalabilidade sem depender de uma pessoa:** um harness maduro replica o mesmo padrão de qualidade entre projetos e equipes, sem depender do conhecimento tácito de um único especialista para garantir a consistência das entregas.

## Próximos passos para quem quer implementar

1. **Documentar o contexto** — criar um `AGENTS.md` com as regras do projeto, os padrões de código e os limites do que o agente pode ou não fazer.
2. **Automatizar os sensores computacionais** — conectar linters, verificadores e testes automatizados ao ciclo de trabalho do agente. Quanto mais rápido o feedback, mais barato o erro.
3. **Distribuir os controles ao longo do ciclo de vida** — checagens rápidas durante o processo, análises mais profundas (revisão por IA) no controle de qualidade final.
4. **Tratar o harness como produto vivo** — todo erro repetido do agente é sinal de que o harness precisa evoluir, não de que o modelo é ruim.

## O ponto central

Prompt engineering resolve a instrução de um comando. Harness engineering resolve o sistema inteiro: restrições, ferramentas, memória, validação e observabilidade. É a diferença entre pedir melhor a uma IA e construir uma arquitetura de software ao redor dela.

Modelos mudam, APIs mudam, provedores mudam. Uma boa arquitetura de harness é o que permite trocar essas peças sem recomeçar do zero — e é isso, cada vez mais, que separa quem usa IA de quem constrói sistemas confiáveis com IA.

Durante anos, a pergunta dominante foi: "qual é o melhor modelo?". Nos próximos anos, a pergunta mais importante provavelmente será outra: quem construiu o melhor harness?

No fim, agentes confiáveis não nascem da escolha do melhor modelo. Eles são resultado de boas decisões de arquitetura.

**Na sua organização, a maior dificuldade hoje está na escolha do modelo ou na construção da arquitetura ao redor dele?**



## Referências

- Birgitta Böckeler (Thoughtworks) — *Harness engineering for coding agent users*, martinfowler.com
- Martin Fowler — *Exploring Generative AI* (série), martinfowler.com
- LangChain — *The Anatomy of an Agent Harness*
- Anthropic — *Harness design for long-running application development*
 














​#ArtificialIntelligence #HarnessEngineering #SoftwareArchitecture #LLM #DataScience
#IA #AI




