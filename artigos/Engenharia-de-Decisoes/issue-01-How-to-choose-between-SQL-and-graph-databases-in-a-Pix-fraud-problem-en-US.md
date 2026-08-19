## Decision Engineering

## Issue #01 — How to choose between SQL and graph databases in a Pix fraud problem

This newsletter analyzes engineering decisions made in the face of real-world problems. Each issue presents the context, decision criteria, evaluated alternatives, accepted trade-offs, and the evidence supporting the choice.

No tutorials. No lists of tools. Decision, criteria, evidence, results.

Today: FraudGraph Brazil, a digital fraud investigation agent based on Neo4j Aura and generative AI.

Baseline
Brazilian financial institutions face growing losses from structured fraud involving money mule account networks. In many financial institutions, fraud detection is still based on CPF-level rules and individual transaction analysis: transaction value limits, frequency, individual scores — analyzed in isolated client queues.

This model works for obvious fraud. It fails when João, Maria, and Pedro — three seemingly independent CPFs — use the same physical device to transfer money to the same Pix destination within minutes of each other. Each transaction, looked at in isolation, appears legitimate. When the alert arrives, it comes hours later — after the money has already been dispersed.

The problem was never detecting a suspicious transaction. It was seeing the network.

Problem
How do we identify coordinated fraud patterns — not isolated transactions, but network structures connecting CPFs, devices, and destination accounts — in time to block them, without inflating false positives that freeze legitimate clients?

Decision Criteria
Before comparing alternatives, it was necessary to define what would characterize a good solution to this problem. The question was never "which database is better?" — it was:

Ability to navigate multiple relationships (CPF → device → destination account) without performance degradation
Low false-positive rate in triangulation scenarios
Response time compatible with real-time decision-making
Ease of translating technical patterns into business decisions for stakeholders who do not read Cypher or SQL
These criteria, rather than technology preferences, guided the comparison below.

Alternatives Considered
Static rules + traditional scoring. Low infrastructure cost, fast implementation. However, it is reactive: it captures previously cataloged patterns but fails to discover new network structures.

PostgreSQL with JOINs across clients, devices, and transaction tables. Technically feasible, but to cross-reference three vectors (CPF, hardware, destination) across millions of records in real time, the computational cost of successive JOINs significantly increases query overhead and makes meeting real-time response requirements difficult.

Neo4j Aura + AI agent (GPT-4o via LangChain). Native relationship modeling — data is naturally structured as a graph rather than tables requiring cross-referencing. Triangulation queries respond in a single traversal, and the AI layer translates the technical subgraph into a readable executive assessment for decision-makers managing account blocks.

Decision
Neo4j Aura as the detection layer, paired with a GPT-4o agent (via LangChain) for contextual interpretation of suspicious clusters.

The choice was not a generic "graph is superior to relational" judgment — it was specific to the criteria defined above: structured Pix fraud is, by nature, a network topology problem, and graph databases exist to answer precisely this type of query with controlled computational costs.

Accepted Trade-offs
Learning curve for Cypher and a relative scarcity of graph specialists compared to the abundance of SQL talent — accepted because the gains in expressiveness for network modeling outweigh the ramp-up costs.
Cost per token and latency of GPT-4o calls compared to pure database queries — accepted because the gain in decision accessibility (natural language reports for non-technical stakeholders) outweighs the additional infrastructure cost, which can be mitigated by caching reports for identical patterns.
Streamlit interface instead of a dedicated frontend — reduced UI flexibility, accepted in exchange for delivery speed and maintaining focus on the core differentiator: graph-based detection, not the user interface.
Evidence
Architectural Evidence — Graph Modeling

The query driving this decision — the technical core of the project — requires three simultaneous conditions: same physical device, same Pix destination, three or more distinct CPFs.

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
In a relational model, answering this same question typically requires multiple JOINs across clients, devices, and transaction tables. In a graph database, the query can be expressed naturally as a traversal across relationships.

   [ Analyst / Web Interface ]
              ↓
       [ Streamlit UI ]
              ↓
     [ Aura Agent (AI) ]
       GPT-4o + LangChain
              ↓
      [ Neo4j Aura Cloud ]
   Triangulation Cypher Query
              ↓
       [ Data Payload ]
    Clients · Devices · Accounts
Operational Evidence — Identified Cluster

In the validation dataset, the query isolated a real cluster: three distinct CPFs, the same physical device, the same Pix destination, and the same timeframe — a pattern that, in a SQL queue isolated by client, would appear as three independent transactions with no red flags.

None of these transactions, analyzed individually, exceeded traditional detection thresholds. Suspicious behavior emerged only when the relationships were analyzed collectively.

Business Evidence — Why This Matters

This pattern is precisely what traditional client-by-client queue systems fail to capture in time: the risk resides not in any single isolated transaction, but in the structural coincidence connecting them.

Impact
In traditional investigations, manually correlating these three records across legacy systems takes minutes to hours of analytical work. With graph-based triangulation, the pattern is identified in a single query — and the AI agent delivers a formatted assessment for blocking decisions without requiring the analyst to read JSON or Cypher.

The gain is not just speed. It is visibility.

Connections that existed within the data but were invisible to relational models become the detection criteria itself.

Next Issue
AWS Lambda vs. Containers: the decision behind automating a serverless data pipeline — and the trade-off most teams ignore until the day volume scales.

Decision Engineering — architecture, trade-offs, and real-world evidence across Cloud, AI, and Data.





 
