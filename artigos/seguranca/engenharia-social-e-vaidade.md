# Engenharia social e vaidade: o que a FCA 200-2 ensina sobre o elo humano na cibersegurança


Quando falamos em cibersegurança, a imagem que vem à mente costuma ser a de um hacker diante de uma tela cheia de código. Mas um documento institucional da Força Aérea Brasileira (FAB) aponta para outro lugar: o problema, muitas vezes, não começa no computador. Começa na conversa, na publicação, no desejo de parecer importante.

Neste artigo, analiso o tema como alguém que trabalha com dados e sistemas críticos: separando o que o material afirma do que ele comprova, e transformando o diagnóstico em decisões práticas.

## 1. Problema de Negócio

Organizações que protegem informações sensíveis investem em firewalls, antivírus, criptografia e controle de acesso. Mesmo assim, continuam expostas a incidentes que não são prevenidos apenas por controles técnicos.

O desafio é entender **como o comportamento humano pode abrir brechas que a tecnologia sozinha não fecha,** e como reduzir esse risco sem culpar pessoas, mas corrigindo processos e controles.

## 2. Contexto

Segundo [reportagem publicada pela Revista Sociedade Militar em outubro de 2026](https://www.sociedademilitar.com.br/2026/10/engenharia-social-vaidade-militar-ciberseguranca-fab-reis.html), a FCA 200-2, documento do Comando da Aeronáutica dedicado à Mentalidade de Segurança, é relevante para a discussão sobre a proteção de informações sensíveis. Esta análise considera a reportagem e o [texto integral da edição de 2008](https://www.sislaer.fab.mil.br/TerminalWebCENDOC/acervo/detalhe/3132), elaborada pelo Centro de Inteligência da Aeronáutica (CIAER) e aprovada pela Portaria nº 3/CIAER, de 19 de dezembro de 2008. A norma dedica uma seção à engenharia social e aborda comportamentos que podem comprometer a salvaguarda de informações sensíveis.

A reportagem cita dois episódios para contextualizar o risco:

- Em 2020, o grupo Digital Space expôs dados de 1.117 alunos da Escola Preparatória de Cadetes do Ar (EPCAR).
- Em setembro de 2026, novas alegações de vazamento, atribuídas a uma conta de cibercrime, voltaram a gerar alerta.

Segundo o texto da norma (item 2.3), as falhas de segurança decorrem de características de comportamento como **vaidade, confiança nas pessoas, entusiasmo e ignorância**, além de outros motivos, como a perda do senso crítico causada por bebida alcoólica. A seção dedicada à vaidade afirma que ela seria responsável por cerca de 90% das indiscrições. O documento, porém, não apresenta nessa passagem uma base empírica, um período de observação ou uma metodologia que permita verificar a proporção. Portanto, o número deve ser tratado como uma afirmação registrada na norma, e não como uma estimativa comprovada da frequência de incidentes de cibersegurança.

## 3. Premissas da análise

Antes de concluir qualquer coisa, deixo claras as premissas, porque é aqui que muita análise se perde:

- **O "90%" consta da norma, mas não é uma estatística verificada.** Nem a norma nem a reportagem apresentam base de dados, período ou metodologia. Além disso, a afirmação da norma refere-se a 90% das *indiscrições*, enquanto o enquadramento jornalístico amplia a discussão para a cibersegurança. Esses conceitos não representam necessariamente o mesmo universo de ocorrências.
- **Alegação não é incidente confirmado.** As alegações de setembro de 2026 devem ser avaliadas com base em evidências verificáveis e, quando disponíveis, em informações de fontes oficiais. A publicação de arquivos por uma conta de cibercrime, isoladamente, não comprova a origem, a data ou o contexto da exposição.
- **Exposição de dados não prova engenharia social.** Para atribuir a causa de um vazamento, é preciso investigação técnica: vetor de entrada, credenciais, vulnerabilidades, registros de atividade.
- **Esta análise busca padrões e boas práticas**, e não uma reprodução da norma. A FCA 200-2 é de 2008. A necessidade de conhecer, a mesa limpa, a criptografia em comunicações sensíveis e a conscientização aparecem nela; MFA resistente a phishing, menor privilégio, monitoramento e indicadores são recomendações desta análise.

## 4. Estratégia da solução

A abordagem que adoto é a de um analista de dados diante de um problema de risco:

1. Entender o que a fonte afirma e o que ela comprova.
2. Separar conceitos que costumam ser misturados: indiscrição, engenharia social, vazamento e invasão técnica.
3. Mapear como cada fator comportamental se transforma em risco real.
4. Traduzir o diagnóstico em controles concretos, em camadas.
5. Definir como medir se a organização está de fato reduzindo o risco.

Este é um artigo analítico com recomendações, não um projeto de dados executado com resultados medidos. O resultado desta análise é uma estrutura conceitual para avaliar riscos associados a comportamentos, processos e controles de segurança. Essa estrutura poderá ser desenvolvida e testada posteriormente com dados sintéticos, deixando explícito que os dados são fictícios e que os resultados não representam incidentes reais.

### Os quatro fatores comportamentais

**Vaidade.** É a necessidade de demonstrar conhecimento, prestígio ou acesso privilegiado. Exemplo: alguém publica uma foto com uma tela ao fundo para mostrar que participa de um projeto importante, e revela sem perceber nomes de sistemas.

**Confiança inadequada.** É confundir familiaridade com autorização. Exemplo: um colega pede um arquivo interno por aplicativo pessoal, o nome é conhecido, e o documento é enviado sem verificar o canal ou a autorização. A conta do colega pode, inclusive, estar comprometida.

**Entusiasmo.** A empolgação reduz o senso crítico. Exemplo: durante uma apresentação, o profissional explica detalhes de implementação e acaba revelando uma limitação técnica ou um cronograma interno.

**Ignorância.** É não perceber o valor de um dado. Exemplo: uma planilha com identificadores pessoais é enviada para uma ferramenta externa sem aprovação.

A norma menciona ainda outros motivos, como a perda do senso crítico causada por bebida alcoólica, que ficam fora desta análise.

Esses fatores se combinam. Uma mesma situação pode envolver vaidade (querer mostrar), confiança (acreditar no interlocutor), entusiasmo (falar mais) e ignorância (não notar a sensibilidade do que foi dito).

### Indiscrição não é engenharia social

| Conceito | O que acontece |
| --- | --- |
| Indiscrição | Alguém revela informação indevidamente, sem que ninguém precise manipulá-lo. |
| Engenharia social | Um agente ativo manipula a pessoa para obter dados, acesso ou uma ação. |
| Vazamento | A informação fica exposta a quem não deveria ter acesso, por qualquer causa. |
| Invasão técnica | Acesso não autorizado por meios técnicos, como uma falha de software. |

Um vazamento pode nascer de engenharia social, mas também de configuração incorreta na nuvem, conta comprometida ou falha de fornecedor.

### O problema da agregação

Informações aparentemente pouco sensíveis podem adquirir valor estratégico quando combinadas. Uma foto de instalação, um comentário sobre a data de uma atividade e a identificação de um fornecedor em documento público podem, juntos, revelar aspectos que nenhum desses fragmentos mostraria isoladamente. É o princípio por trás da inteligência de fontes abertas (OSINT): a coleta e a análise de informações disponíveis em fontes abertas podem ter usos legítimos quando realizadas de forma lícita e ética, respeitando os limites aplicáveis de privacidade e segurança. As mesmas técnicas também podem ser exploradas para espionagem, fraude ou preparação de ataques.

## 5. Insights

**Insight 1: tecnologia sem comportamento seguro perde valor.** Senha anotada em papel no monitor, credencial compartilhada e regra burlada para ganhar prazo anulam controles automatizados. A tecnologia pode funcionar conforme projetado e, ainda assim, não impedir um incidente quando os processos permitem compartilhamento indevido, as permissões são excessivas ou os controles não cobrem aquele cenário.

**Insight 2: o princípio da necessidade de conhecer reduz a superfície de exposição.** Se 500 pessoas têm acesso integral a uma base, o comprometimento de uma conta pode ampliar significativamente a exposição. Quando cada pessoa acessa apenas os dados e as operações necessários à sua função, o impacto potencial de uma credencial comprometida tende a ser menor. Somado ao menor privilégio (quais operações essa identidade realmente precisa executar), forma uma defesa simples e poderosa. A necessidade de conhecer está expressa na própria FCA 200-2, que restringe o acesso a assuntos sigilosos a pessoas credenciadas que precisem conhecê-los (item 3.1).

**Insight 3: "elo mais fraco" simplifica demais.** Pessoas erram, mas sistemas também são projetados por pessoas. Uma arquitetura com privilégios excessivos, um processo que incentiva atalhos ou a falta de registros de atividade também causam incidentes. A pergunta madura não é "quem errou?", e sim "por que o erro foi possível e por que os controles não o impediram?"

A própria norma reconhece que segurança envolve pessoas, processos e tecnologias, mas também chama o ser humano de elo mais fraco e conclui, ao analisar um cenário típico de escritório, que o caminho é educação e treinamento (itens 2.2 e 2.4.1). A leitura atual acrescenta que o ambiente também precisa ser projetado para que o erro humano não se converta em incidente.

**Insight 4: treinamento realizado não é risco reduzido.** Uma organização pode ter 100% de cursos concluídos e continuar vulnerável. O que importa é medir comportamento e controles.

## 6. Recomendações e indicadores de avaliação

As medidas a seguir são recomendações derivadas da análise, não resultados de uma implementação. Sua eficácia precisaria ser avaliada por meio de implementação, indicadores e acompanhamento dos resultados.

A partir da análise, recomendo organizar a prevenção em cinco frentes, em defesa em profundidade, em que nenhuma barreira é perfeita, mas a falha de uma não derruba todas:

1. **Pessoas e comportamento:** treinar com cenários realistas e criar um canal simples para reportar suspeitas, sem constranger quem relata um erro.
2. **Identidade e acesso:** autenticação multifator, preferencialmente resistente a phishing (como FIDO/WebAuthn) nos acessos críticos; revisão periódica de permissões; contas administrativas separadas das comuns.
3. **Proteção dos dados:** classificação por sensibilidade, restrição de exportações, canais aprovados e criptografia. Criptografia protege o caminho, mas não resolve o envio para o destinatário errado.
4. **Monitoramento e resposta:** registro de eventos, detecção de comportamento anômalo, e procedimentos para revogar acessos e preservar evidências.
5. **Governança e melhoria contínua:** responsáveis definidos, riscos avaliados e causas de incidentes corrigidas na raiz.

A mesa limpa, prevista na norma (item 3.2.2), também faz sentido: segurança é digital e física. Um documento esquecido numa impressora compartilhada expõe dados sem invadir sistema algum.

### Como medir se está funcionando

- Taxa de reporte de mensagens suspeitas.
- Tempo médio para reportar um incidente.
- Percentual de contas críticas com MFA resistente a phishing.
- Número de contas com privilégios excessivos.
- Tempo para revogar credenciais comprometidas.
- Incidentes recorrentes pela mesma causa.

Esses indicadores precisam de contexto. Um aumento nos relatos de phishing pode significar mais ameaça, mas também pessoas reconhecendo melhor as tentativas. E uma taxa alta de cliques em simulações não prova negligência: a dificuldade da simulação e as condições de trabalho influenciam o resultado ([NIST](https://www.nist.gov/news-events/news/2020/09/phish-scale-nist-developed-method-helps-it-staff-see-why-users-click)).

É importante distinguir indicadores de cobertura dos controles, como o percentual de contas protegidas por MFA resistente a phishing ([CISA](https://www.cisa.gov/sites/default/files/publications/fact-sheet-implementing-phishing-resistant-mfa-508c.pdf)), de indicadores de resultado, como a recorrência de incidentes relacionados a credenciais comprometidas. Nenhum deles, isoladamente, comprova a eficácia global do programa de segurança.

Para permitir comparações ao longo do tempo, cada indicador deve ter definição operacional, população de referência, período de medição, responsável e critério de interpretação. Sempre que possível, a organização deve acompanhar a tendência histórica e investigar mudanças relevantes, em vez de interpretar um valor isolado como prova de sucesso ou fracasso.

## 7. Aprendizados: o olhar de Ciência de Dados

Para quem trabalha com dados, o tema traz uma lição de método. Imagine uma organização que quer entender por que certos incidentes se repetem. Em vez de presumir que a causa é vaidade ou desatenção, ela pode estruturar uma análise com campos como tipo de incidente, canal utilizado, tipo de informação, controle que falhou, tempo até a detecção e causa confirmada após investigação.

Uma análise exploratória bem feita pode mostrar, por exemplo, se os incidentes se concentram em processos de compartilhamento de arquivos ou em situações de urgência. Não é preciso começar com um modelo de machine learning: um modelo só se justifica quando há necessidade concreta, dados adequados e evidência de que agrega valor.

E há um limite ético claro: esses dados não devem servir para rotular pessoas como "vaidosas" ou "perigosas". O foco é risco, processo e controle.

O que aprendi ao estudar o material:

- Alegação, incidente e causa são três coisas diferentes.
- Correlação não é causa, e proporção citada por uma norma não é distribuição comprovada de incidentes reais.
- A escolha da ferramenta vem depois da compreensão do problema. Antes de propor um modelo, um dashboard ou uma automação, é preciso verificar a qualidade das evidências, entender os limites dos dados e definir qual decisão precisa ser melhorada.

## 8. Próximos passos

- Acompanhar a confirmação, ou não, das alegações de setembro de 2026 por fontes oficiais.
- Consultar outras publicações do CIAER sobre segurança orgânica, como o RCA 205-1 citado na FCA 200-2, na medida em que sejam de acesso público.
- Construir um projeto de portfólio com dados sintéticos de incidentes, aplicando análise exploratória e indicadores de risco, deixando explícito que os dados são fictícios.
- Aprofundar o uso de MFA resistente a phishing e menor privilégio em arquiteturas de nuvem.

## Conclusão

O ensinamento mais sólido do artigo não é que a vaidade cause 90% das falhas. Com o material disponível, essa proporção não pode ser tratada como estatística comprovada sobre a FAB.

O ensinamento sólido é este: proteger a informação depende da tecnologia, das decisões das pessoas e das condições em que elas trabalham. A vaidade pode levar à exposição indevida. A confiança pode dispensar verificações. O entusiasmo pode provocar revelações excessivas. O desconhecimento pode fazer alguém subestimar um dado.

A resposta não é só cobrar mais atenção individual. É combinar controles técnicos, procedimentos claros, acesso restrito, treinamento e uma cultura que permita comunicar erros rapidamente.

Maturidade em segurança não é presumir que ninguém vai errar. É projetar processos e sistemas capazes de prevenir, detectar e limitar as consequências quando alguém errar.

---

**Fontes e referências:** [Revista Sociedade Militar (outubro de 2026)](https://www.sociedademilitar.com.br/2026/10/engenharia-social-vaidade-militar-ciberseguranca-fab-reis.html); [FCA 200-2/2008, Mentalidade de Segurança, CIAER (acervo SISLAER/CENDOC)](https://www.sislaer.fab.mil.br/TerminalWebCENDOC/acervo/detalhe/3132); Portaria nº 3/CIAER, de 19/12/2008 (BCA nº 013, de 21/01/2009); [CISA, Implementing Phishing-Resistant MFA](https://www.cisa.gov/sites/default/files/publications/fact-sheet-implementing-phishing-resistant-mfa-508c.pdf); 

[NIST, The Phish Scale: NIST-Developed Method Helps IT Staff See Why Users Click on Fraudulent Emails](https://www.nist.gov/news-events/news/2020/09/phish-scale-nist-developed-method-helps-it-staff-see-why-users-click).

*Que decisões de segurança da sua organização dependem hoje de as pessoas simplesmente não errarem?*
