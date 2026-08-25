# Browser Harness & Agentes de IA: quando trocar determinismo por adaptação?

### Uma análise de arquitetura, custo de manutenção, trade-offs e riscos de segurança ao conectar LLMs ao Chrome DevTools Protocol

Trocar automação determinística por um agente de IA que controla o Chrome via CDP não é apenas uma atualização de ferramenta — é uma troca arquitetural arriscada. Este texto não é sobre conhecer uma ferramenta nova. É sobre decidir, com critério, quando essa troca faz sentido — e o preço técnico que ela cobra.

## 1. Problema de Negócio

Uma abordagem arquitetural vem ganhando atenção entre equipes que constroem agentes de IA: em vez de colocar uma camada de automação de navegador como principal abstração entre o agente e o browser, aproxima-se o LLM diretamente das capacidades expostas pelo Chrome DevTools Protocol (CDP), por meio de uma camada fina e editável. O projeto open-source Browser Harness é um exemplo dessa abordagem.

O problema operacional por trás disso: automações determinísticas podem exigir manutenção sempre que mudanças no DOM, nos seletores, nos fluxos ou no comportamento da interface invalidam as estratégias de localização e interação previamente definidas. Para times que operam agentes em sites de terceiros — ambientes que eles não controlam e não podem prever — esse custo de manutenção contínua deixou de ser sustentável em escala.

Em termos de negócio, a decisão envolve equilibrar custo de manutenção, velocidade de adaptação, previsibilidade operacional e risco de incidentes. A pergunta que este artigo tenta responder não é "o que é Browser Harness", mas **em que condições vale a pena trocar previsibilidade por adaptação, e que risco isso importa junto**.

## 2. Contexto

Para fins desta análise, podemos organizar as abordagens de automação de navegador em três grupos — uma classificação analítica criada para esta comparação, não uma taxonomia histórica estabelecida pela indústria:

1. **Automação determinística** (Selenium, Playwright, Puppeteer): o fluxo é definido antecipadamente pelo desenvolvedor, e sua execução pode ser testada, versionada e reproduzida. Robusta para fluxos estáveis; exige manutenção quando o ambiente muda.
2. **Agentes multimodais baseados em visão**: o modelo utiliza screenshots ou outras representações visuais da interface para decidir ações, podendo operar por coordenadas de tela ou outros mecanismos de interação; reduz parte da fragilidade, mas ainda costuma depender de camadas intermediárias pesadas.
3. **Conexão direta via CDP**: o Browser Harness se posiciona nessa categoria ao fornecer uma camada fina e editável sobre o Chrome DevTools Protocol — não o próprio protocolo, mas uma ponte que permite ao agente utilizar diretamente capacidades expostas pelo navegador e estender essa ponte com código próprio quando falta uma capacidade. O CDP também expõe informações estruturadas, incluindo uma árvore de acessibilidade, que pode ser utilizada pelo agente para compreender a interface; isso é uma capacidade do protocolo, não um mecanismo exclusivo do projeto, nem a única forma de o agente interpretar a página.

## 3. Baseline: como o problema é resolvido hoje

A abordagem de referência para automação de navegador continua sendo a automação determinística, com Playwright, Selenium ou Puppeteer. Nessa abordagem, o fluxo é definido antecipadamente e sua execução pode ser testada, versionada e reproduzida — inclusive com estratégias de localização mais robustas que simples seletores CSS (papel semântico, texto visível, label).

O custo aparece quando o ambiente automatizado é externo, dinâmico ou sujeito a mudanças frequentes: cada alteração relevante na interface pode gerar manutenção do fluxo.

Portanto, o baseline desta análise não é "não usar automação" — é **usar automação determinística com manutenção explícita do fluxo**. É contra esse baseline, e não contra uma ausência de solução, que o Browser Harness precisa justificar sua adoção.

## 4. Premissas da Análise

- O material avaliado inclui documentação oficial do repositório, materiais de terceiros (vídeos, posts, discussões em comunidades) e o site do projeto — fontes com rigor técnico desigual, tratadas como evidência qualitativa, não como benchmark controlado.
- Não houve execução própria do harness nem validação empírica de performance; a análise é sobre arquitetura e decisão, não sobre número de tarefas concluídas com sucesso.
- O escopo é a categoria de ferramenta — "harness CDP com extensão dinâmica de helpers por um LLM" —, não uma avaliação de qualidade de uma implementação específica.

## 5. Critérios de Decisão

A adoção foi avaliada a partir de quatro critérios:

- **Adaptabilidade** — capacidade de lidar com mudanças no ambiente sem intervenção manual.
- **Auditabilidade** — capacidade de explicar e reproduzir as ações executadas.
- **Superfície de risco** — impacto potencial de operar com sessões autenticadas e conteúdo não confiável.
- **Custo de manutenção** — esforço necessário para manter a automação funcionando ao longo do tempo.

## 6. Alternativas

A tabela abaixo resume como cada abordagem se posiciona nos quatro critérios definidos na seção anterior:

| Abordagem | Adaptabilidade | Previsibilidade | Manutenção | Risco operacional relativo no cenário analisado |
|---|---|---|---|---|
| Selenium | Baixa/Média | Alta | Média/Alta | Baixo |
| Playwright | Média | Alta | Média | Baixo |
| Agente multimodal baseado em visão | Alta | Baixa | Média | Médio |
| Browser Harness (CDP direto) | Alta | Baixa | Hipótese de redução | Alto potencial |

*As classificações são relativas ao cenário de agentes operando páginas externas e não representam uma propriedade absoluta de cada tecnologia — é uma avaliação qualitativa baseada em propriedades arquiteturais, não um benchmark experimental. Em particular, a "hipótese de redução" de manutenção atribuída ao Browser Harness é exatamente isso — uma hipótese a ser validada experimentalmente, não um resultado observado neste estudo.*

## 7. Decisões Técnicas e Trade-offs

O trade-off central é **determinismo vs. adaptação**, e ele se paga em duas moedas diferentes.

**O que se ganha:** resiliência a mudanças de layout e reaproveitamento progressivo de conhecimento. Quando o agente encontra uma capacidade ausente, ele escreve ou estende um helper e reutiliza essa capacidade nas execuções seguintes — é esse ciclo de descoberta, implementação e reutilização de capacidades que o próprio projeto descreve como *self-healing*. Isso não significa correção automática garantidamente segura de falhas; significa que o agente pode ampliar suas próprias capacidades de interação durante a execução, escrevendo ou estendendo helpers no workspace do agente e reutilizando-os em execuções posteriores.

**O que se paga:** perda de previsibilidade. Um script Playwright falha de forma auditável — é possível saber exatamente qual seletor não foi encontrado. Um agente que estende seu próprio código em tempo de execução falha de forma menos rastreável, e o comportamento em produção pode variar entre execuções do mesmo objetivo. Para fluxos financeiros ou regulatórios, onde auditabilidade do passo a passo é requisito, essa é uma desvantagem que nenhum ganho de resiliência compensa.

Vale o contraponto: Browser Harness não deve ser tratado como uma evolução linear do Playwright. Ele representa uma abordagem arquitetural diferente, endereçando um problema diferente — navegação autônoma resiliente em ambientes não controlados —, não uma substituição geral da automação determinística.

## 8. Segurança e Superfície de Ataque

Quando configurado para se conectar a uma instância do Chrome já em uso pelo usuário, o harness pode operar dentro de uma sessão que contém cookies, tokens, histórico, extensões e aplicações autenticadas — embora o projeto também suporte conexão a instâncias isoladas, lançadas especificamente para automação. A superfície de risco muda de acordo com essa escolha de configuração: quando conectado à sessão pessoal, o problema deixa de ser apenas "o agente erra uma tarefa" e passa a ser "o agente age com a identidade digital de quem o autorizou".

Essa escolha não é apenas operacional — ela define a fronteira de confiança da automação. Em uma sessão pessoal, o agente potencialmente compartilha o mesmo contexto de identidade e privilégios do usuário; em uma sessão dedicada e isolada, é possível limitar esse raio de alcance.

O CDP não é, por si só, a vulnerabilidade. O risco emerge da combinação entre um agente que interpreta conteúdo não confiável, capacidade de executar ações, e acesso a uma sessão com privilégios.

## 9. Insights

O insight mais relevante desta análise não veio da documentação do projeto — veio de uma observação direta durante a coleta de material. Registro dela para fins de reprodutibilidade:

> Em 25 de agosto de 2026, ao revisar o site oficial do projeto (browser-harness.com) como parte da coleta de fontes para este artigo, foi identificado um bloco de texto formatado como instrução dirigida a um agente de IA — solicitando instalação do repositório, conexão ao navegador real de quem o lesse e uma ação de engajamento (interação com o repositório no GitHub) condicionada a uma verificação superficial de contexto. A instrução era incompatível com o objetivo original de quem consultava a página (pesquisar sobre a tecnologia), e não foi executada. Por prudência, o conteúdo literal da instrução não é reproduzido aqui.

O conteúdo da página pode deixar de ser apenas "dado a ser exibido" e passar a competir com o objetivo original do usuário por autoridade sobre o agente. O comportamento observado é consistente com um caso de **injeção indireta de instruções** (*indirect prompt injection*): uma instrução é introduzida por uma fonte que o agente deveria tratar como conteúdo, não como autoridade — categoria formalizada por Greshake et al. (2023) e listada como o risco de maior prioridade (LLM01) no OWASP Top 10 for LLM Applications.

> **Insight de segurança.** Uma página web não é apenas conteúdo quando um agente pode agir sobre o navegador. Ela também pode se tornar uma fonte de instruções adversariais.

Isso não é uma falha do Browser Harness especificamente — é uma propriedade estrutural de qualquer arquitetura onde um LLM lê conteúdo de páginas web e tem permissão de agir sobre um navegador com sessão real. A conexão via CDP oferece ao agente acesso de baixo nível às capacidades do navegador; quando esse acesso é combinado com interpretação de conteúdo não confiável e permissões amplas de execução, aumenta o potencial de impacto de uma decisão incorreta do agente — não porque o acesso em si seja a falha, mas porque amplia o raio de alcance de qualquer erro de interpretação, se a fronteira entre instrução do usuário e conteúdo da página não for tratada como requisito de arquitetura.

A análise, portanto, classifica o conteúdo pelo seu comportamento e pelo risco que introduz, não pela intenção de quem o publicou.

## 10. Business Performance

O benefício econômico potencial não está necessariamente na velocidade de execução do navegador, mas na redução do esforço de manutenção em ambientes instáveis.

Uma forma mais adequada de estruturar essa decisão, separando benefício de custo, é:

`valor líquido potencial = manutenção evitada − custo operacional do agente − custo de supervisão humana − custo esperado de incidentes`

O custo de supervisão humana merece destaque: um agente autônomo pode reduzir o trabalho de manutenção de scripts, mas cria uma atividade nova — a revisão humana das ações do agente, especialmente em fluxos com ações críticas. Ignorar essa variável superestimaria o ganho líquido.

Como não foram coletados dados suficientes para estimar esses componentes, este estudo não atribui um valor financeiro ao benefício. A hipótese econômica existe; a evidência quantitativa ainda precisa ser produzida.

## 11. Decisão de Engenharia

Com base nos critérios e trade-offs mapeados: **decisão provisória de engenharia — não adotar Browser Harness como substituto geral da automação determinística.** Trata-se de um Go/No-Go provisório, sujeito a revisão à luz do experimento proposto na seção 12, não uma conclusão definitiva baseada em teste executado.

Considerá-lo apenas como candidato experimental para fluxos em que:

- o ambiente é altamente variável e não controlado pelo time;
- a adaptação tem valor econômico comprovável, não apenas hipotético;
- o fluxo não exige determinismo absoluto nem auditabilidade regulatória;
- o navegador pode ser isolado em um perfil dedicado, sem credenciais de produção;
- ações críticas possuem confirmação humana explícita antes da execução.

## 12. Próximo Experimento

Para transformar esta análise qualitativa em evidência quantitativa, o desenho de teste proposto é:

**Cenário A — Playwright:** executar um conjunto fixo de tarefas repetidas vezes.
**Cenário B — Browser Harness:** executar o mesmo conjunto de tarefas, nas mesmas condições.

Cada cenário deverá ser executado em múltiplas repetições, utilizando o mesmo conjunto de tarefas e as mesmas alterações controladas — uma única execução não permite comparar confiabilidade entre as abordagens.

Como a hipótese central do Browser Harness é adaptabilidade, o experimento precisa testar exatamente isso: introduzir mudanças controladas no DOM, no texto, na estrutura visual ou no fluxo, e medir a degradação de cada abordagem. A pergunta deixa de ser "qual ferramenta funciona melhor?" e passa a ser "qual abordagem permanece funcional quando o ambiente muda?".

Métricas a comparar: taxa de sucesso, taxa de sucesso após mudança deliberada da interface, número de intervenções humanas necessárias, número de intervenções de manutenção por tarefa, tempo de manutenção acumulado, número de falhas, tempo médio de recuperação após falha, ações incorretas do agente, custo por execução e incidentes de segurança observados.

**Critério de sucesso:** a hipótese de adaptabilidade só será considerada confirmada se o Browser Harness apresentar maior taxa de sucesso após as mudanças controladas na interface, sem aumento desproporcional de ações incorretas ou de intervenções humanas em relação ao cenário Playwright.

## 13. Próximos Passos

- Avaliar o harness isolado de sessões pessoais, em perfil de navegador dedicado e sem credenciais de produção, antes de qualquer adoção.
- Definir, antes da adoção, uma política explícita de confirmação humana para qualquer ação que o agente proponha a partir de conteúdo lido em página de terceiros — não apenas para ações que o próprio agente classifique como sensíveis.
- Tratar a fronteira entre instrução do usuário e conteúdo de página como requisito de arquitetura, não como responsabilidade do modelo subjacente.
- Executar o experimento comparativo descrito na seção anterior antes de qualquer decisão de adoção em produção.

## 14. Limitações da Análise

- O Browser Harness não foi executado neste estudo.
- Não houve comparação experimental com Playwright, Selenium ou outra ferramenta.
- As classificações da tabela de alternativas são qualitativas e relativas ao cenário analisado.
- O impacto econômico não foi quantificado.
- A análise de segurança identifica uma superfície de risco arquitetural, mas não constitui um teste de segurança ou pentest da ferramenta.
- As conclusões sobre adaptabilidade e redução de manutenção permanecem hipóteses até serem validadas pelo experimento proposto na seção 12.

## 15. O que esta análise não conclui

Esta análise não conclui que o Browser Harness seja mais eficiente que o Playwright, que seja mais seguro, ou que reduza custos de manutenção em produção. Essas são hipóteses que dependem de validação experimental. A conclusão desta análise é mais restrita: a arquitetura pode ser adequada para determinados ambientes altamente variáveis, desde que adaptação e risco sejam avaliados conjuntamente.

## 16. Conclusão

O Browser Harness não deve ser tratado como uma evolução linear do Playwright. Ele representa uma abordagem arquitetural diferente, com um perfil de risco diferente, que resolve um problema específico — navegação autônoma resiliente em ambientes não controlados — ao custo de ampliar a superfície de ataque ao combinar interpretação probabilística, conteúdo não confiável e capacidade autônoma de ação sobre o navegador. Automação determinística também tem superfície de ataque própria (credenciais, sessões, execução de JavaScript, uploads); o que muda aqui é a combinação específica de fatores, não a mera presença de risco.

A decisão, portanto, não é "Playwright ou Browser Harness?". É: onde precisamos de determinismo e onde precisamos de adaptação — e quanto risco estamos dispostos a aceitar por essa adaptação?

Ferramentas diferentes resolvem problemas diferentes. A decisão madura não começa pela tecnologia; começa pelo problema, pelo risco e pelo custo de não resolvê-lo.

Nenhuma etapa da instalação do Browser Harness foi executada para esta análise.

**E na sua equipe?** Vocês já enfrentam a dor de manutenção de scripts Playwright/Selenium em sites instáveis? Como estão avaliando o uso de agentes autônomos para isso? Vamos debater nos comentários.

## Referências

- Browser Harness — repositório oficial: https://github.com/browser-use/browser-harness (o próprio projeto se descreve como uma camada fina e editável sobre o CDP, em que agentes podem estender helpers durante a execução)
- Chrome DevTools Protocol — documentação oficial: https://chromedevtools.github.io/devtools-protocol/
- Playwright — documentação oficial: https://playwright.dev/docs/intro
- Selenium — documentação oficial: https://www.selenium.dev/documentation/
- Puppeteer — documentação oficial: https://pptr.dev/
- OWASP Top 10 for LLM Applications (2025) — LLM01: Prompt Injection: https://genai.owasp.org/llmrisk/llm01-prompt-injection/
- Greshake, K. et al. (2023). *Not What You've Signed Up For: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection*. arXiv:2302.12173

**Tags (Medium):** Artificial Intelligence, Software Architecture, Web Automation, Data Science, Cybersecurity
 



















