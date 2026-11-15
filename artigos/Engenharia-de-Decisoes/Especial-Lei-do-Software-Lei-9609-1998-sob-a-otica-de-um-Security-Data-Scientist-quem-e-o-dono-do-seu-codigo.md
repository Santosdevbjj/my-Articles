## Engenharia de Decisões - Especial

# Lei do Software (Lei 9.609/1998) sob a ótica de um Security Data Scientist: quem é o dono do seu código?


Depois de mais de 15 anos trabalhando com sistemas críticos do setor financeiro, aprendi uma coisa: código também é patrimônio. E todo ativo precisa de titularidade definida, rastreabilidade e mecanismos de proteção.

Entretanto, muitos desenvolvedores e profissionais de dados só percebem a importância dessas questões quando surge uma disputa sobre autoria, uso ou propriedade de uma solução.

Neste artigo, vou analisar a Lei nº 9.609/1998 (Lei do Software) sob a perspectiva de quem trabalha com dados, inteligência artificial e segurança da informação. A proposta é conectar legislação, engenharia de software e governança, com exemplos práticos e um pouco de Python.

Aviso: este texto é educativo e não substitui a orientação de um advogado especialista em Direito Digital.

# 1. Problema de Negócio
Imagine três situações comuns no desenvolvimento de software:

Você desenvolve um modelo de detecção de fraude para uma empresa e, meses depois, surge a dúvida: o código é seu ou da empresa?
Um freelancer entrega o sistema, o cliente deixa de pagar a última parcela e ambos exigem o código-fonte.
Alguém copia seu projeto de portfólio, apresenta como se fosse dele, e você não consegue demonstrar sua autoria.
Em comum, as três situações têm **falta de clareza sobre titularidade e falta de evidências de autoria e integridade.** O prejuízo pode ser financeiro, jurídico e de reputação.

A pergunta que guia este artigo é: **como uma empresa ou profissional pode demonstrar a titularidade, a integridade e a origem de um software quando surge uma disputa?**

# 2. Contexto
No Brasil, programas de computador em si são protegidos pelo regime de direitos autorais, conforme a Lei nº 9.609/1998, e não são considerados invenções patenteáveis por si só, conforme o art. 10, inciso V, da Lei nº 9.279/1996 (Lei de Propriedade Industrial).

Isso não significa que toda solução implementada por software esteja excluída da proteção patentária. Invenções implementadas por computador podem, dependendo de suas características técnicas e dos requisitos legais, receber proteção por patente. Para este artigo, o foco será a proteção autoral do programa de computador.

Duas leis conversam entre si:

**Lei nº 9.609/1998 (Lei do Software):** protege programas de computador e regula sua comercialização.
**Lei nº 9.610/1998 (Lei de Direitos Autorais):** estabelece o regime geral de direitos autorais, cujas disposições são aplicadas aos programas de computador naquilo que for compatível com a legislação específica.
Para o Security Data Scientist, isso importa porque o nosso dia a dia mistura exatamente os pontos sensíveis: código proprietário, modelos treinados, bibliotecas open source, dados de clientes e entregas sob contrato.

# 3. Premissas
Para organizar a análise, adotei estas premissas:

A fonte oficial é o texto da lei, publicado pelo Planalto (links nas referências).
O foco é a **visão prática de quem desenvolve,** não a discussão acadêmica jurídica.
A análise descreve o que a lei diz; **não é parecer jurídico** nem garante o resultado de disputas concretas.
Taxas e procedimentos do INPI mudam com o tempo; confirme sempre no portal oficial antes de agir.
Código, dados e modelos de IA podem estar sujeitos a regimes jurídicos diferentes. A Lei nº 9.609/1998 trata de programas de computador, e não se deve presumir que datasets, pesos de modelos ou features recebam automaticamente a mesma proteção.
# 4. Estratégia da Solução: o que a lei diz, passo a passo
Para compreender a aplicação da legislação, organizei a análise em seis dimensões: proteção autoral, prazo de proteção, registro, titularidade, direitos de exploração e consequências jurídicas das violações.

## 4.1 O que é protegido (e o que não é)
A Lei nº 9.609/1998 protege a **expressão do programa de computador,** materializada no código-fonte e no código-objeto. A documentação técnica e outros materiais associados também podem receber proteção autoral, conforme sua natureza e os requisitos legais aplicáveis.

Por outro lado, ideias abstratas, métodos, sistemas, conceitos matemáticos e algoritmos considerados isoladamente não são protegidos pelo direito autoral como se fossem o próprio código.

Em termos práticos: o código que implementa um pipeline de detecção de anomalias pode ser protegido. A ideia de utilizar Isolation Forest para identificar possíveis fraudes, por si só, não garante exclusividade sobre essa abordagem.

## 4.2 Prazo de proteção
Conforme o art. 2º, § 2º, da Lei nº 9.609/1998, a proteção dos direitos relativos ao programa de computador é assegurada por 50 anos, contados a partir de 1º de janeiro do ano seguinte ao da publicação ou, na ausência desta, ao da criação.

## 4.3 Registro no INPI: facultativo, mas estratégico
O registro no INPI é facultativo: a proteção autoral do software independe de registro, conforme o art. 2º, § 3º, da Lei nº 9.609/1998.

Entretanto, o registro oferece maior segurança jurídica ao titular, especialmente em situações que envolvam disputas sobre autoria ou titularidade. Ele é um elemento probatório importante, mas não uma garantia absoluta de autoria nem uma decisão definitiva sobre quem é o titular.

Um aspecto interessante é o mecanismo utilizado pelo INPI: no procedimento eletrônico, o titular gera um **resumo digital criptográfico (hash)** a partir da documentação técnica do programa, como o código-fonte, e informa esse resumo no formulário de registro. O código-fonte ou a documentação técnica utilizada para gerar o hash não precisa ser anexado ao formulário eletrônico. O pedido exige a inclusão dos documentos previstos pelo INPI, como a Declaração de Veracidade e, quando aplicável, a procuração.

O titular permanece responsável pela guarda e preservação da integridade do arquivo original utilizado na geração do hash. Esse cuidado é fundamental para uma eventual verificação técnica em disputa judicial.

O pedido é realizado pelo sistema e-Software e exige certificado digital qualificado emitido por autoridade certificadora credenciada à ICP-Brasil.

É importante compreender que o hash permite verificar a integridade e a correspondência entre documentos, mas não prova isoladamente quem efetivamente criou o programa.

## 4.4 De quem é o software? (Titularidade)
A titularidade depende das circunstâncias em que o software foi desenvolvido.

Conforme o art. 4º da Lei nº 9.609/1998, salvo estipulação em contrário, os direitos relativos ao programa pertencem exclusivamente ao empregador, contratante de serviços ou órgão público quando o desenvolvimento ocorre durante a vigência do vínculo e decorre de uma destas hipóteses, que são alternativas e não cumulativas:

Contrato expressamente destinado à pesquisa e desenvolvimento;
Atividade de desenvolvimento prevista nas atribuições do profissional;
Desenvolvimento decorrente da própria natureza dos encargos do vínculo.
Entretanto, o § 2º estabelece uma exceção: quando o programa é desenvolvido sem relação com o contrato e sem utilização de recursos, informações tecnológicas, segredos industriais ou comerciais, materiais, instalações ou equipamentos do contratante, os direitos pertencem exclusivamente ao profissional.

O § 3º do mesmo artigo também estende essas regras aos programas desenvolvidos por bolsistas, estagiários e profissionais em situações assemelhadas.

Portanto, a análise de titularidade não deve considerar apenas a existência de vínculo empregatício, mas também a natureza das atividades, as condições contratuais e os recursos utilizados no desenvolvimento.

Traduzindo para a realidade de quem trabalha com dados: desenvolver um modelo de Machine Learning durante um projeto empresarial não significa automaticamente que você poderá utilizá-lo comercialmente em seu portfólio. É necessário analisar o contrato, as atividades envolvidas, os recursos utilizados e os direitos efetivamente atribuídos a cada parte.

Isso é especialmente relevante para quem desenvolve projetos paralelos, mantém repositórios públicos no GitHub ou trabalha como consultor independente.

#4.5 Direitos morais expressamente ressalvados x direitos patrimoniais
**Critério**

**Direitos morais ressalvados**

**Direitos patrimoniais**

Natureza

Direitos ligados à paternidade e à proteção da honra ou reputação do autor

Direitos de exploração econômica

Exemplos

Reivindicar a paternidade e opor-se às alterações prejudiciais nas condições legais

Reprodução, licenciamento e comercialização

Tratamento jurídico

Ressalvados expressamente pelo art. 2º, § 1º

Podem ser objeto de cessão ou licença, conforme a legislação

Diferentemente do regime geral dos direitos autorais, a Lei do Software estabelece uma proteção específica e limitada para os direitos morais do autor.

O art. 2º, § 1º, afasta a aplicação das disposições gerais sobre direitos morais ao programa de computador, ressalvando dois direitos:

reivindicar a paternidade do programa;
opor-se a alterações não autorizadas que impliquem deformação, mutilação ou outra modificação prejudicial à honra ou à reputação do autor.
Essa distinção é importante porque a autoria pessoal e a titularidade econômica do programa não são necessariamente da mesma pessoa.

## 4.6 Licença de uso, transferência de tecnologia e SaaS
**Licença de uso**

O titular continua detendo os direitos patrimoniais e autoriza o uso dentro das condições do contrato (prazo, número de usuários, funcionalidades e responsabilidades das partes), observada a lei.
Sem contrato de licença, o documento fiscal da aquisição ou do licenciamento da cópia servirá para comprovar a regularidade do uso (art. 9º, parágrafo único).
O art. 8º obriga quem comercializa o programa a assegurar aos usuários, durante o prazo de validade técnica da versão comercializada, a prestação de serviços técnicos complementares relacionados ao seu funcionamento adequado, observadas as condições legais.
O parágrafo único do art. 8º prevê que essa obrigação persiste se o programa for retirado de circulação comercial nesse prazo, salvo justa indenização dos eventuais prejuízos causados a terceiros.
## Transferência de tecnologia (art. 11)

Nos casos de transferência de tecnologia de programa de computador, os respectivos contratos devem ser registrados no INPI para produzir efeitos perante terceiros.
Para esse registro, o fornecedor deve entregar ao receptor a documentação técnica completa necessária à absorção da tecnologia: código-fonte comentado, memorial descritivo, especificações funcionais internas, diagramas, fluxogramas e outros dados técnicos, conforme o parágrafo único do artigo.
## SaaS (Software as a Service)

O cliente normalmente acessa funcionalidades de um software hospedado e administrado pelo fornecedor, sem necessariamente receber uma cópia do programa para instalação.
A relação contratual pode envolver prestação de serviços, licenciamento de software e outras obrigações, conforme sua estrutura.
Por isso, devem ser avaliados disponibilidade, SLA, segurança, proteção de dados, portabilidade, continuidade e encerramento do serviço.
O acesso a um serviço SaaS não implica, por si só, cessão da titularidade do software ou entrega do código-fonte. Esses direitos e obrigações devem ser analisados conforme o contrato e a estrutura da solução.

## 4.7 Penalidades (art. 12 e art. 14)
O art. 12 da Lei nº 9.609/1998 estabelece as seguintes penalidades:

Violação de direitos de autor de programa de computador: detenção de 6 meses a 2 anos ou multa.
Reprodução total ou parcial, para fins de comércio, sem autorização expressa do autor ou titular dos direitos: reclusão de 1 a 4 anos e multa.
Comercialização e outras condutas previstas no § 2º envolvendo cópias produzidas com violação de direitos autorais: aplicação da mesma pena prevista no § 1º.
Além da esfera criminal, o prejudicado pode propor ação para impedir a continuidade da violação. Conforme o art. 14, § 1º, essa ação pode ser cumulada com pedido de perdas e danos pelos prejuízos decorrentes da infração. A aplicação dessas disposições depende da configuração dos requisitos legais de cada situação concreta.

O § 3º do art. 12 estabelece que, como regra, os crimes previstos nesse artigo somente são processados mediante queixa. A lei prevê exceções quando a infração é praticada em prejuízo de entidade de direito público, autarquia, empresa pública, sociedade de economia mista ou fundação instituída pelo poder público, ou quando dela resultam sonegação fiscal, perda de arrecadação tributária ou crimes contra a ordem tributária ou as relações de consumo.

# 5. Insights: o olhar de Security Data Scientist
Aqui entra a visão que une direito e segurança. A lei dá o enquadramento; a engenharia dá a evidência.

**Insight 1: autoria exige evidências; hash é uma ferramenta de integridade.** Uma função hash SHA-256 gera uma impressão digital criptográfica do conteúdo de um arquivo. Se o conteúdo mudar, o hash normalmente será diferente. Mas o hash representa o conteúdo submetido à função: ele não identifica quem criou o arquivo, quem é seu proprietário, nem comprova quando foi criado. Uma pessoa pode gerar o hash de um arquivo que não criou. Veja um exemplo em Python:

```

import hashlib
from pathlib import Path


def sha256_arquivo(caminho: str) -> str:
    arquivo = Path(caminho)

    if not arquivo.is_file():
        raise FileNotFoundError(f"Arquivo não encontrado: {arquivo}")

    hash_sha256 = hashlib.sha256()

    with arquivo.open("rb") as f:
        for bloco in iter(lambda: f.read(8192), b""):
            hash_sha256.update(bloco)

    return hash_sha256.hexdigest()


arquivo = "meu_modelo_deteccao_fraude.py"

print(f"Arquivo: {arquivo}")
print("Algoritmo: SHA-256")
print(f"Hash: {sha256_arquivo(arquivo)}")
```



O exemplo utiliza SHA-256 para produzir um resumo digital do arquivo. Em um ambiente de produção, esse processo pode ser integrado ao pipeline de CI/CD, permitindo registrar a integridade de versões, modelos e artefatos publicados.

Uma função hash criptográfica não é um mecanismo de criptografia reversível. Ela gera um resumo digital utilizado para verificar a integridade de determinado conteúdo. Para construir uma trilha de evidências mais robusta, é necessário combinar hash, versionamento, identificação dos responsáveis, registros temporais confiáveis e preservação dos artefatos originais, associando cada hash à versão correspondente do artefato e armazenando os registros em um ambiente protegido contra alterações indevidas.

**Insight 2: seu repositório é uma trilha de evidências, não uma prova absoluta de autoria.** Commits, tags, releases e histórico de desenvolvimento ajudam a demonstrar a evolução de um projeto. Entretanto, datas e informações de autoria em commits Git podem ser alteradas, e o histórico pode ser reescrito. Para aumentar a confiabilidade, vale combinar:

Commits assinados digitalmente.
Releases identificadas por tags.
Registro de hashes dos artefatos.
Armazenamento de evidências em ambiente protegido.
Registros temporais confiáveis.
Backups e trilhas de auditoria.
A assinatura de commits permite verificar se o conteúdo foi assinado por uma chave criptográfica associada a determinado signatário e se o conteúdo assinado permaneceu íntegro. Entretanto, a associação da chave a uma identidade real depende de sua validação, do controle de acesso e da segurança de sua custódia. A assinatura, isoladamente, não comprova quem efetivamente escreveu o código nem o momento real de sua criação. Para fortalecer a evidência temporal, podem ser utilizados serviços confiáveis de carimbo do tempo e registros de auditoria protegidos contra alterações.

A combinação desses mecanismos aumenta a capacidade de reconstruir a evolução do software e identificar alterações não autorizadas.

**Insight 3: open source também tem regras, e a cadeia de suprimentos de software importa.** Bibliotecas de terceiros vêm com licenças (MIT, Apache, GPL...). Em um projeto de ciência de dados, uma biblioteca aparentemente simples pode trazer obrigações de atribuição, preservação de avisos de licença ou disponibilização de código derivado, dependendo da licença e da forma de distribuição. A aplicação das obrigações depende da licença específica, da relação entre os componentes e do modo como o software é distribuído.

Por isso, é importante manter um inventário de dependências, identificar suas licenças e avaliar a compatibilidade entre elas e o modelo de distribuição do produto. Padrões de SBOM, como CycloneDX e SPDX, permitem representar e documentar os componentes utilizados em um software, incluindo informações relevantes sobre dependências e licenças. Esses inventários podem ser integrados aos processos de segurança e governança da cadeia de suprimentos de software.

**Insight 4: contrato é um controle de governança e segurança.** Cláusulas de propriedade intelectual, confidencialidade (NDA), proteção de dados pessoais em conformidade com a LGPD e reversibilidade (devolução ou transferência do código, dos dados e da documentação no encerramento do contrato) funcionam como controles que reduzem riscos jurídicos e operacionais.

Assim como controles técnicos reduzem a exposição a ameaças, controles contratuais estabelecem responsabilidades, limites de utilização e condições para proteger os ativos digitais.

**Insight 5: IA e dados ampliam a superfície de risco jurídico e técnico.** Em projetos de Machine Learning, o código-fonte não é o único ativo que merece proteção. Também precisamos considerar datasets, notebooks, features, parâmetros, pesos dos modelos e documentação técnica. Entretanto, cada elemento pode estar sujeito a regimes jurídicos diferentes, e não devemos presumir que todos são automaticamente protegidos pela Lei do Software.

**Ativo**

**Questão de proteção**

Código de treinamento

Pode receber proteção autoral como software

Notebook

Pode conter código e conteúdo autoral protegível

Dataset

Depende da natureza dos dados, da organização e dos direitos envolvidos

Pesos do modelo

A proteção não é automática nem necessariamente se enquadra integralmente na Lei do Software

Features

Podem envolver código, dados, métodos e informações confidenciais

Dados pessoais

Estão sujeitos às regras aplicáveis de proteção de dados, especialmente a LGPD

Existe uma distinção importante: a Lei nº 9.610/1998 admite proteção autoral para bases de dados cuja seleção, organização ou disposição do conteúdo constitua criação intelectual (art. 7º, XIII). Entretanto, essa proteção não se estende automaticamente aos dados ou materiais em si mesmos, conforme o § 2º do mesmo artigo. Por isso, é necessário analisar separadamente a estrutura da base, a origem dos dados, os direitos de terceiros e a existência de dados pessoais.

Antes de treinar, utilizar ou publicar um modelo, algumas perguntas são fundamentais:

Qual é a origem dos dados?
A licença do dataset permite o uso pretendido?
Existem dados pessoais sujeitos à LGPD?
O código foi desenvolvido pelo profissional ou pertence ao contratante?
Existem restrições contratuais sobre os pesos e artefatos do modelo?
O modelo pode ser publicado sem revelar informações confidenciais?
Na perspectiva de Security Data Science, a proteção dos ativos exige combinar governança de dados, segurança da informação, rastreabilidade e análise de direitos de propriedade intelectual.

**Impacto no negócio: quanto custa não saber de quem é o código?**

A incerteza sobre titularidade, licenças ou autoria pode se converter em risco financeiro e operacional:

Uma disputa envolvendo uso não autorizado de software pode gerar custos jurídicos, afetar a continuidade de operações e levar à adoção de medidas judiciais para impedir a prática do ato infrator. Conforme o caso, também pode haver pedido de perdas e danos, nos termos do art. 14, § 1º, além de eventual responsabilização criminal, observados os requisitos do art. 12.
Dependências com licenças incompatíveis podem obrigar a refazer componentes ou renegociar a forma de distribuição do produto.
Sem evidências de integridade e autoria, é mais difícil se defender em uma disputa.
O impacto financeiro depende de cada caso. O raciocínio, porém, é comparar os custos potenciais de uma disputa (honorários, tempo da equipe, possíveis interrupções operacionais e eventuais indenizações) com o investimento preventivo em contratos claros, inventário de licenças e preservação de evidências.

**Exemplo de aplicação em um pipeline de desenvolvimento seguro (proposta)**

Imagine um projeto de detecção de fraudes desenvolvido com Python e Machine Learning. Antes de disponibilizar uma nova versão, o pipeline de CI/CD pode executar controles como:

Verificar dependências e licenças utilizadas, gerando uma SBOM.
Executar verificações de vulnerabilidades nas dependências.
Gerar hashes dos artefatos produzidos.
Assinar digitalmente os artefatos ou suas declarações de proveniência.
Registrar versão, responsável, data e evidências de execução.
Armazenar os registros em ambiente protegido contra alterações indevidas.
O objetivo não é apenas entregar um modelo funcional, mas também manter rastreabilidade sobre os componentes utilizados, a origem dos artefatos e as alterações realizadas ao longo do ciclo de vida do software. Trata-se de uma proposta de aplicação, e não de um pipeline implementado neste artigo.

# 6. Recomendações práticas: transformando a análise em controles
As conclusões da análise se traduzem em ações que um profissional pode implementar. Este é o checklist de quem se posiciona como resolvedor de problemas:

**Analise o contrato antes de iniciar o desenvolvimento.** Esclareça as regras de titularidade e exploração do software: verifique se estão definidos os direitos sobre os ativos preexistentes, as entregas específicas do projeto, as condições de cessão ou licenciamento e os limites de utilização. Não presuma que quem escreveu o código será necessariamente o titular dos direitos patrimoniais: a lei estabelece critérios próprios para essas situações.
**Separe os ativos preexistentes dos desenvolvidos para o cliente.** O chamado background IP compreende os conhecimentos, códigos, bibliotecas, componentes e demais ativos de propriedade ou sob controle legítimo de uma das partes antes do projeto. Esses elementos devem ser identificados e tratados contratualmente. Componentes de terceiros e bibliotecas open source precisam ser inventariados separadamente, respeitando suas respectivas licenças. O contrato deve distinguir os ativos preexistentes, os componentes incorporados e aquilo que foi desenvolvido especificamente para o cliente.
**Defina contratualmente as condições de entrega e transferência de direitos.** Estabeleça marcos de pagamento, critérios de aceite, prazos de entrega e o momento em que ocorrerá a cessão ou concessão de licença de uso do software. Essas condições devem ser negociadas e formalizadas entre as partes.
**Mantenha um inventário de dependências e licenças** do projeto, de preferência em formato de SBOM.
**Gere e guarde evidências de integridade e rastreabilidade:** histórico no Git, commits assinados, releases, hashes dos artefatos principais.
**Avalie o registro no INPI** para projetos de valor comercial relevante.
**Busque orientação jurídica quando houver riscos relevantes.** A revisão é especialmente importante em contratos que envolvam cessão de propriedade intelectual, exclusividade, dados pessoais, tecnologia proprietária, transferência de tecnologia ou responsabilidades financeiras significativas.
Benefícios esperados: maior previsibilidade contratual, redução da exposição a disputas de propriedade intelectual, melhoria da rastreabilidade e fortalecimento da governança dos ativos de software.

## 7. Próximos Passos
Se eu tivesse mais tempo, aprofundaria:

Implementar um pipeline de CI/CD com verificação automatizada de integridade dos artefatos.
Gerar uma SBOM para inventariar dependências e licenças.
Integrar assinatura digital e rastreabilidade de versões.
Criar controles de governança para datasets, modelos e artefatos de Machine Learning.
Desenvolver um estudo de caso sobre evidências digitais de autoria e integridade de software.
Elaborar cláusulas modelo de propriedade intelectual para contratos de desenvolvimento de software e de ciência de dados.
## Aprendizados
O maior aprendizado ao estudar esse tema foi perceber que proteção jurídica e proteção técnica precisam caminhar juntas.

A legislação estabelece os direitos e as responsabilidades. A engenharia contribui com os mecanismos de integridade, rastreabilidade, versionamento e auditoria que ajudam a preservar evidências. Para quem trabalha com dados, inteligência artificial e segurança, compreender essa relação é parte da responsabilidade profissional.

E existe uma pergunta que todo profissional de tecnologia deveria fazer antes de iniciar um projeto: **quem é o titular do que estou construindo, e como posso demonstrar isso?**

Compartilhe sua experiência nos comentários: desenvolvedores, cientistas de dados e líderes técnicos, vocês já passaram por armadilhas contratuais em projetos de tecnologia? Já tiveram dúvidas sobre a titularidade de um código, conjunto de dados ou modelo de Machine Learning?

## Referências
**BRASIL. Lei nº 9.609, de 19 de fevereiro de 1998.** Dispõe sobre a proteção da propriedade intelectual de programa de computador e sua comercialização no País. https://www.planalto.gov.br/ccivil_03/leis/l9609.htm
**BRASIL. Lei nº 9.610, de 19 de fevereiro de 1998.** Altera, atualiza e consolida a legislação sobre direitos autorais. https://www.planalto.gov.br/ccivil_03/leis/l9610.htm
**BRASIL. Lei nº 9.279, de 14 de maio de 1996.** Regula direitos e obrigações relativos à propriedade industrial. https://www.planalto.gov.br/ccivil_03/leis/l9279.htm
**BRASIL. Lei nº 13.709, de 14 de agosto de 2018.** Lei Geral de Proteção de Dados Pessoais (LGPD). https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm
*#BRASIL. Decreto nº 2.556, de 20 de abril de 1998.** Regulamenta o registro previsto no art. 3º da Lei nº 9.609/1998. https://www.planalto.gov.br/ccivil_03/decreto/d2556.htm
**INPI. Guia Básico: Registro de Programa de Computador.** https://www.gov.br/inpi/pt-br/servicos/programas-de-computador/guia-basico. Acesso em: 15 nov. 2026.
**INPI. Programas de Computador: Perguntas Frequentes.** https://www.gov.br/inpi/pt-br/acesso-a-informacao/perguntas-frequentes/programas-de-computador. Acesso em: 15 nov. 2026.
**INPI. Programa de Computador: Mais Informações.** https://www.gov.br/inpi/pt-br/servicos/programas-de-computador/guia-completo-de-programa-de-computador. Acesso em: 15 nov. 2026.
**CycloneDX. Especificação oficial.** https://cyclonedx.org/specification/overview/
**SPDX. Especificações oficiais.** https://spdx.dev/specifications/
**Observação:** orientações e procedimentos do INPI podem ser atualizados; consulte as páginas indicadas na data de publicação.






#LeiDoSoftware #PropriedadeIntelectual #SecurityDataScience #CyberSecurity #CienciaDeDados #GovernancaDeDados #LGPD #Python




















