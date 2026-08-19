## Ingeniería de Decisiones

## Edición #01 — Cómo decidir entre SQL y grafos en un problema de fraude Pix

Esta newsletter analiza decisiones de ingeniería tomadas ante problemas reales. Cada edición presenta el contexto, los criterios de decisión, las alternativas evaluadas, los trade-offs aceptados y las evidencias que sustentan la elección.

Sin tutoriales. Sin listas de herramientas. Decisión, criterio, evidencia, resultado.

Hoy: FraudGraph Brasil, un agente de investigación de fraudes digitales basado en Neo4j Aura e IA generativa.

## Baseline
Las instituciones financieras brasileñas enfrentan pérdidas crecientes por fraudes estructurados en redes de cuentas testaferro (contas laranja). En muchas entidades financieras, la detección de fraude aún se basa en reglas por CPF y análisis individual de transacciones: límite de monto, frecuencia, scoring individual — analizadas en colas aisladas por cliente.

Este modelo funciona para el fraude obvio. Falla cuando João, Maria y Pedro —tres CPFs aparentemente independientes— usan el mismo dispositivo físico para transferir dinero al mismo destino Pix con minutos de diferencia. Cada transacción, analizada de forma aislada, parece legítima. La alerta, cuando llega, lo hace horas después —cuando el dinero ya ha sido pulverizado.

El problema nunca fue detectar una transacción sospechosa. Fue ver la red.

## Problema
¿Cómo identificar patrones de fraude coordinado —no transacciones aisladas, sino estructuras de red entre CPFs, dispositivos y cuentas de destino— a tiempo para su bloqueo, sin inflar falsos positivos que bloqueen a clientes legítimos?

Criterios de Decisión
Antes de comparar las alternativas, era necesario definir qué caracterizaría a una buena solución para este problema. La pregunta nunca fue "¿qué base de datos es mejor?" —fue:

Capacidad de navegar múltiples relaciones (CPF → dispositivo → cuenta de destino) sin degradación de rendimiento.
Baja tasa de falsos positivos en escenarios de triangulación.
Tiempo de respuesta compatible con la toma de decisiones en tiempo real.
Facilidad para traducir el patrón técnico en una decisión de negocio para quien no lee Cypher o SQL.
Estos criterios, y no la preferencia por una tecnología, fueron los que orientaron la siguiente comparación.

Alternativas Consideradas
Reglas estáticas + scoring tradicional. Bajo costo de infraestructura, implementación rápida. Pero es reactivo: captura patrones ya catalogados, no descubre nuevas estructuras de red.

PostgreSQL con JOINs entre tablas de clientes, dispositivos y transacciones. Técnicamente viable, pero para cruzar tres vectores (CPF, hardware, destino) en millones de registros y en tiempo real, el costo computacional de JOINs sucesivos aumenta significativamente el costo de las consultas y dificulta cumplir los requisitos de respuesta en tiempo real.

Neo4j Aura + agente de IA (GPT-4o vía LangChain). Modelado nativo de relaciones: el dato nace como un grafo, no como una tabla que debe ser cruzada. Las consultas de triangulación responden en un solo recorrido, y la capa de IA traduce el subgrafo técnico en un informe ejecutivo legible para quien decide el bloqueo.

Decisión
Neo4j Aura como capa de detección, junto con un agente GPT-4o (vía LangChain) para la interpretación contextual de los clusters sospechosos.

La elección no fue un "los grafos son superiores a las bases relacionales" de forma genérica —fue específica para el criterio definido anteriormente: el fraude Pix estructurado es, por naturaleza, un problema de topología de red, y las bases de datos de grafos existen para responder exactamente a este tipo de preguntas con un costo controlado.

Trade-offs Aceptados
Curva de aprendizaje en Cypher y relativa escasez de profesionales especializados en grafos, frente a la abundancia de talento en SQL —aceptado porque la ganancia de expresividad en el modelado de red compensa el costo de aprendizaje (ramp-up).
Costo por token y latencia de la llamada a GPT-4o, frente a una consulta pura de base de datos —aceptado porque la ganancia en accesibilidad de la decisión (informe en lenguaje natural para quien no lee grafos) supera el costo de infraestructura adicional, mitigable con caché de informes para patrones idénticos.
Interfaz Streamlit en lugar de un frontend dedicado —menor flexibilidad de UI, aceptado a cambio de velocidad de entrega y enfoque en el diferencial real: la detección mediante grafos, no la interfaz.
Evidencias
Evidencia arquitectónica — el modelado en grafo

La consulta que sustenta la decisión —el corazón técnico del proyecto— exige tres condiciones simultáneas: mismo dispositivo físico, mismo destino Pix, tres o más CPFs distintos.

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
En un modelado relacional, responder a esta misma pregunta normalmente requiere múltiples JOINs entre tablas de clientes, dispositivos y transacciones. En una base de datos de grafos, la consulta se puede expresar de forma natural como un recorrido entre relaciones.

   [ Analista / Interfaz Web ]
              ↓
       [ Streamlit UI ]
              ↓
     [ Agente Aura (IA) ]
       GPT-4o + LangChain
              ↓
      [ Neo4j Aura Cloud ]
  Consulta Cypher de Triangulación
              ↓
     [ Conjunto de Datos ]
    Clientes · Dispositivos · Cuentas
Evidencia operacional — el cluster encontrado

En el conjunto de datos de validación, la consulta aisló un cluster real: tres CPFs distintos, el mismo dispositivo físico, el mismo destino Pix, la misma ventana de tiempo —un patrón que, en una cola SQL aislada por cliente, aparecería como tres transacciones independientes sin ninguna señal de alerta.

Ninguna de estas transacciones, analizada individualmente, superaba los límites tradicionales de detección. El comportamiento sospechoso surgió únicamente cuando las relaciones se analizaron en su conjunto.

Evidencia de negocio — por qué esto importa

Este tipo de patrón es exactamente lo que los sistemas tradicionales de cola por cliente no capturan a tiempo: el riesgo no está en ninguna transacción aislada, está en la coincidencia estructural entre ellas.

Impacto
En una investigación tradicional, correlacionar estos tres registros manualmente entre sistemas heredados consume de minutos a horas de trabajo analítico. Con la triangulación en grafo, el patrón se identifica en una sola consulta —y el agente de IA ya entrega el informe formateado para la decisión de bloqueo, sin requerir que el analista lea JSON o Cypher.

La ganancia no es solo velocidad. Es visibilidad.

Las conexiones que existían en los datos, pero eran invisibles para el modelo relacional, pasan a ser el propio criterio de detección.

Próxima edición
AWS Lambda vs. contenedor: la decisión detrás de la automatización serverless de un pipeline de datos —y el trade-off que la mayoría ignora hasta el día en que el volumen crece.

Ingeniería de Decisiones — arquitectura, trade-offs y evidencias reales en Cloud, IA y Datos.





