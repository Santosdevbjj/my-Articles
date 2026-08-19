   # Engenharia de Decisões
### Edição #01 — Como decidir entre SQL e grafos em um problema de fraude Pix


Esta newsletter analisa decisões de engenharia tomadas diante de problemas reais. Cada edição apresenta o contexto, os critérios de decisão, as alternativas avaliadas, os trade-offs aceitos e as evidências que sustentam a escolha.

Sem tutorial. Sem lista de ferramentas. Decisão, critério, evidência, resultado.

Hoje: **FraudGraph Brasil**, um agente de investigação de fraudes digitais baseado em Neo4j Aura e IA generativa.



## Baseline

Instituições financeiras brasileiras enfrentam perdas crescentes com fraudes estruturadas em redes de contas laranja. Em muitas instituições financeiras, a detecção de fraude ainda é baseada em regras por CPF e análise individual de transações: limite de valor, frequência, score individual — analisadas em filas isoladas por cliente.

Esse modelo funciona para fraude óbvia. Ele falha quando João, Maria e Pedro — três CPFs aparentemente independentes — usam o mesmo aparelho físico para transferir dinheiro ao mesmo destino Pix em minutos de diferença. Cada transação, olhada isoladamente, parece legítima. O alerta, quando chega, chega horas depois — quando o dinheiro já foi pulverizado.

O problema nunca foi detectar uma transação suspeita. Foi enxergar a rede.

## Problema

Como identificar padrões de fraude coordenada — não transações isoladas, mas estruturas de rede entre CPFs, dispositivos e contas de destino — em tempo hábil para bloqueio, sem inflar falsos positivos que travam clientes legítimos?

## Critérios de Decisão

Antes de comparar as alternativas, era necessário definir o que caracterizaria uma boa solução para esse problema. A pergunta nunca foi "qual banco de dados é melhor?" — foi:

- Capacidade de navegar múltiplos relacionamentos (CPF → dispositivo → conta destino) sem degradação de performance
- Baixa taxa de falso positivo em cenários de triangulação
- Tempo de resposta compatível com decisão em tempo real
- Facilidade de traduzir o padrão técnico em decisão de negócio para quem não lê Cypher ou SQL

Esses critérios, não preferência por tecnologia, é que orientaram a comparação seguinte.

## Alternativas Consideradas

**Regras estáticas + scoring tradicional.** Baixo custo de infraestrutura, implementação rápida. Mas é reativo: captura padrões já catalogados, não descobre estrutura de rede nova.

**PostgreSQL com JOINs entre tabelas de clientes, dispositivos e transações.** Tecnicamente viável, mas para cruzar três vetores (CPF, hardware, destino) em milhões de registros e tempo real, o custo computacional de JOINs sucessivos aumenta significativamente o custo das consultas e dificulta atender requisitos de resposta em tempo real.

**Neo4j Aura + agente de IA (GPT-4o via LangChain).** Modelagem nativa de relacionamento — o dado já nasce como grafo, não como tabela a ser cruzada. Consultas de triangulação respondem em uma única travessia, e a camada de IA traduz o subgrafo técnico em parecer executivo legível por quem decide o bloqueio.

## Decisão

Neo4j Aura como camada de detecção, com um agente GPT-4o (via LangChain) para interpretação contextual dos clusters suspeitos.

A escolha não foi "grafo é superior a relacional" de forma genérica — foi específica ao critério definido acima: fraude Pix estruturada é, por natureza, um problema de topologia de rede, e bancos de grafo existem para responder exatamente esse tipo de pergunta com custo controlado.

## Trade-offs Aceitos

- **Curva de aprendizado em Cypher** e escassez relativa de profissionais especializados em grafo, frente à abundância de talento em SQL — aceito porque o ganho de expressividade na modelagem de rede compensa o custo de ramp-up.
- **Custo por token e latência da chamada ao GPT-4o**, frente a uma consulta pura de banco — aceito porque o ganho em acessibilidade da decisão (parecer em linguagem natural para quem não lê grafo) supera o custo de infraestrutura adicional, mitigável com cache de pareceres para padrões idênticos.
- **Interface Streamlit em vez de frontend dedicado** — menor flexibilidade de UI, aceito em troca de velocidade de entrega e foco no diferencial real: a detecção via grafo, não a interface.

## Evidências

**Evidência arquitetural — a modelagem em grafo**

A query que sustenta a decisão — o coração técnico do projeto — exige três condições simultâneas: mesmo dispositivo físico, mesmo destino Pix, três ou mais CPFs distintos.

```cypher
MATCH (c:Cliente)-[:UTILIZA]->(d:Dispositivo),
      (c)-[:TRANSFERIU]->(p:ContaDestino)
WITH d, p, collect(c.nome) AS clientes, count(c) AS total_cpfs
WHERE total_cpfs >= 3
RETURN d.device_id   AS dispositivo,
       d.ip          AS ip_dispositivo,
       p.pix         AS pix_destino,
       p.banco       AS banco_destino,
       clientes,
       total_cpfs
```

Em uma modelagem relacional, responder essa mesma pergunta normalmente exige múltiplos JOINs entre tabelas de clientes, dispositivos e transações. Em um banco de grafos, a consulta pode ser expressa naturalmente como uma travessia entre relacionamentos.

```
   [ Analista / Interface Web ]
              ↓
       [ Streamlit UI ]
              ↓
     [ Aura Agent (IA) ]
       GPT-4o + LangChain
              ↓
      [ Neo4j Aura Cloud ]
     Query Cypher de Triangulação
              ↓
       [ Massa de Dados ]
    Clientes · Dispositivos · Contas
```

**Evidência operacional — o cluster encontrado**

Na massa de dados de validação, a query isolou um cluster real: três CPFs distintos, mesmo aparelho físico, mesmo destino Pix, mesma janela de tempo — padrão que, em fila SQL isolada por cliente, apareceria como três transações independentes sem sinal de alerta.

Nenhuma dessas transações, analisada individualmente, ultrapassava os limites tradicionais de detecção. O comportamento suspeito surgiu apenas quando os relacionamentos foram analisados em conjunto.

**Evidência de negócio — por que isso importa**

Esse tipo de padrão é exatamente o que sistemas tradicionais de fila-por-cliente não capturam a tempo: o risco não está em nenhuma transação isolada, está na coincidência estrutural entre elas.

## Impacto

Em investigação tradicional, correlacionar esses três registros manualmente entre sistemas legados consome minutos a horas de trabalho analítico. Com a triangulação em grafo, o padrão é identificado em uma única consulta — e o agente de IA já entrega o parecer formatado para decisão de bloqueio, sem exigir que o analista leia JSON ou Cypher.

> **O ganho não é apenas velocidade. É visibilidade.**

Conexões que existiam nos dados, mas eram invisíveis ao modelo relacional, passam a ser o próprio critério de detecção.



## Próxima edição

AWS Lambda vs. contêiner: a decisão por trás da automação serverless de um pipeline de dados — e o trade-off que a maioria ignora até o dia em que o volume cresce.



*Engenharia de Decisões — arquitetura, trade-offs e evidências reais em Cloud, IA e Dados.*




