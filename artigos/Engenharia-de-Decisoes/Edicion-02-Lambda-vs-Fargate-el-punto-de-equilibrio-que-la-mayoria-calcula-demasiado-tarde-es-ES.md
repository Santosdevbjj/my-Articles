## Ingeniería de Decisiones

## Edición #02 — Lambda vs. Fargate: el punto de equilibrio que la mayoría calcula demasiado tarde

Esta newsletter analiza decisiones de ingeniería tomadas ante problemas reales. Cada edición presenta el contexto, los criterios de decisión, las alternativas evaluadas, los trade-offs aceptados y las evidencias que respaldan la elección.

Sin tutoriales. Sin listas de herramientas. Decisión, criterio, evidencia, resultado.

Hoy: automatización de pipelines de datos con Lambda y Fargate — y el trade-off financiero entre ejecución bajo demanda y capacidad continua, que muchos equipos solo descubren cuando el volumen crece.

Este no es un benchmark de rendimiento entre Lambda y Fargate. Es un modelo económico basado en premisas explícitas.

## Baseline
La elección entre AWS Lambda y contenedores administrados (ECS/Fargate) suele tratarse como una decisión de estilo de arquitectura. Al inicio de un pipeline —pocas ejecuciones al día y cargas que duran segundos— la diferencia absoluta de costo entre ambos enfoques tiende a ser pequeña, mientras que Lambda reduce la complejidad operacional inicial (despliegue o deploy).

Vale una corrección conceptual antes de continuar: Fargate también es cómputo administrado —AWS aprovisiona y opera la infraestructura, y tú no administras servidores. La diferencia real entre ambos enfoques no es "serverless vs. infraestructura propia". Es serverless orientado a función/evento (Lambda) frente a contenedor administrado con capacidad configurable (ECS/Fargate).

El problema aparece cuando el pipeline pasa a procesar millones de eventos al mes: la decisión que era operacional se convierte en una decisión financiera, y pocos equipos recalculan el modelo de costos cuando el volumen de la carga de trabajo (workload) cambia de orden de magnitud.

## Problema
¿Cómo decidir entre Lambda y contenedor para la automatización de pipelines de datos de forma que la elección siga siendo correcta no solo en el piloto, sino en el volumen de producción esperado de 6 a 12 meses?

## Criterios de Decisión
Costo total proyectado en el volumen real esperado, no en el volumen del piloto
Complejidad operacional que el equipo está dispuesto a absorber
Restricciones técnicas del entorno de ejecución (timeout, memoria, almacenamiento temporal) frente a la necesidad real del pipeline
Existencia de un punto de equilibrio financiero calculable —no estimado por intuición
Premisas del Modelo
Cada número presentado en esta edición depende de estas premisas. Cambiar cualquiera de ellas altera el resultado:

Región: us-east-1 · Arquitectura: x86
Lambda: 2 GB de memoria asignada por ejecución; la CPU es proporcional a la memoria configurada y no es un parámetro independiente
Fargate: 1 vCPU + 2 GB de memoria por tarea; CPU y memoria son parámetros de la configuración de la tarea, sujetos a las combinaciones soportadas por Fargate
2 segundos de duración promedio por ejecución
Carga suficientemente uniforme a lo largo del mes (sin picos concentrados)
730 horas en un mes comercial
El cálculo considera exclusivamente compute + peticiones
Para facilitar la comparación, el modelo no considera créditos o Free Tier, ni CloudWatch, transferencia de datos, NAT Gateway, ECR, Savings Plans o Spot
Modelo de precios: agosto/2026 · us-east-1 · x86 · On-Demand
Alternativas Consideradas
Lambda con paquete ZIP. Empaquetado tradicional del código de la función. Menor fricción en el despliegue inicial.

## Lambda con imagen de contenedor (OCI). Misma arquitectura Lambda/FaaS, empaquetada como imagen Docker —lo que cambia es la forma de empaquetado, no el modelo de cobro ni la naturaleza de la ejecución. Reduce la fricción de portabilidad entre entornos.

ECS/Fargate. Cobro por los recursos solicitados por la tarea (vCPU, memoria y, cuando aplique, almacenamiento) durante el tiempo en que la tarea permanece activa; el costo no está indexado directamente al número de eventos procesados en ese período.

Modelo de Costos
Lambda: costo asociado a peticiones + duración × memoria (GB-segundo).

Fargate: costo asociado a los recursos solicitados por la tarea (vCPU y memoria) × tiempo de ejecución.

El modelo de costos pasa de un cobro predominantemente por ejecución (Lambda) a un cobro por capacidad asignada durante el período en que la tarea está activa (Fargate). Ambos pueden crecer con la carga de trabajo, pero la función de costo está determinada por variables distintas: duración y memoria por ejecución en Lambda; recursos y tiempo de ejecución de las tareas en Fargate —con autoscaling y paralelismo, Fargate no necesariamente crece de forma lineal con el volumen mensual.

También vale señalar: Lambda cuenta con tiered pricing —10% de descuento entre 6 y 15 mil millones de GB-segundos/mes y 20% por encima de eso, por arquitectura. En el escenario de esta edición, el consumo se ubica en 40 millones de GB-segundos —muy por debajo del primer tramo de descuento, por lo que el tiered pricing no altera el escenario base.

Escenario Base
Pipeline de ingestión que valida, limpia y transforma archivos JSON, con 10 millones de ejecuciones/mes: Lambda configurado con 2 GB de memoria y duración promedio de 2 segundos por ejecución; Fargate configurado con 1 vCPU y 2 GB de memoria por tarea. Lambda se modela por ejecución; Fargate se modela por capacidad continua en este escenario —son unidades de comparación distintas, no configuraciones equivalentes.

Costo en Lambda: peticiones (10 millones × $0,20/millón = $2,00) + compute (40.000.000 GB-s × $0,0000166667 = $666,67) = $668,67/mes.

Costo de la capacidad Fargate en el modelo: asumiendo que cada tarea procesa una unidad de trabajo a la vez, la carga representa aproximadamente 7,6 vCPU-equivalentes de capacidad promedio (20.000.000 segundos-CPU ÷ 730 h en segundos). Esto corresponde a aproximadamente 7,6 tareas de 1 vCPU en ejecución continua. Redondeando a 8 tareas activas 24/7: 8 tareas × 730 h × US0,04937/h = **US288,32/mes**.

Importante: este escenario no representa una arquitectura ECS/Fargate completa de producción. Representa una unidad económica de capacidad continua, utilizada para comparar la función de costo con Lambda —si una tarea procesara múltiples unidades de trabajo en paralelo, el número de tareas necesarias cambiaría.

Nota: los valores en USD representan precios de referencia de us-east-1 para el escenario definido y pueden cambiar según la región, arquitectura, contrato y actualización de la tabla de precios de AWS.

Break-even
Bajo estas premisas, el costo unitario estimado por ejecución en Lambda, incluyendo petición y compute, es de aproximadamente US0,00006687, mientras que una tarea Fargate de 1 vCPU + 2 GB mantenida 24/7 cuesta aproximadamente US36,04/mes. Por lo tanto:

US36,04 ÷ US0,00006687 ≈ 540 mil ejecuciones/mes

El break-even no es un número fijo; es una función: costo mensual de la capacidad Fargate ÷ costo Lambda por ejecución. Alterar la memoria, duración, throughput o distribución de carga desplaza este punto —el resultado anterior describe el escenario calculado, no una regla universal entre Lambda y Fargate.

Análisis de Sensibilidad
(carga uniforme y capacidad Fargate continua — mismas premisas del escenario base)

El volumen mensual por sí solo no determina al ganador —pero muestra la tendencia bajo las premisas fijadas. Los valores de Fargate representan la capacidad continua mínima necesaria bajo la hipótesis de carga uniforme y una unidad de trabajo a la vez por tarea; no representan necesariamente el costo de una arquitectura optimizada para cargas intermitentes. El número de tareas está redondeado hacia arriba.

Ejecuciones/mes	Lambda	Fargate	Mejor costo	Diferencia mensual
100 mil	$6,69	$36,04 (1 tarea mínima)	Lambda	$29,35
500 mil	$33,43	$36,04	Lambda (cerca del break-even)	$2,61
1 millón	$66,87	$36,04	Fargate (inversión)	$30,83
5 millones	$334,33	$144,16 (4 tareas)	Fargate	$190,17
10 millones	$668,67	$288,32 (8 tareas)	Fargate	$380,35
50 millones	$3.343,34	$1.405,56 (39 tareas)	Fargate	$1.937,78
Lo que la tabla evidencia es una función de decisión, no un número aislado: por debajo de 540 mil ejecuciones/mes, el costo mínimo de mantener una tarea Fargate activa pesa más que pagar por ejecución en Lambda; por encima de eso, la curva se invierte.

Observación: el costo de Fargate se incrementa en escalones porque el modelo redondea la capacidad necesaria a tareas enteras de 1 vCPU —Lambda crece de forma aproximadamente lineal por ejecución, mientras que Fargate crece en saltos por tarea agregada. En una arquitectura real, el dimensionamiento dependería también del throughput (tasa de procesamiento) por tarea, el paralelismo y la estrategia de autoscaling.

Trade-offs Aceptados
En el escenario calculado, por debajo del break-even de ~540 mil ejecuciones/mes: aceptar el modelo de cobro por invocación de Lambda, cambiando el potencial de eficiencia de costo en cargas de trabajo de alta utilización por una menor responsabilidad operacional y la capacidad de escalar a cero.
Por encima de ese umbral, con tráfico constante: aceptar la mayor complejidad operacional de orquestar tareas, capacidad y autoscaling en ECS/Fargate, a cambio de una reducción potencial en el costo de cómputo. El costo podría reducirse aún más con Compute Savings Plans, disponibles tanto para Lambda como para Fargate cuando exista un compromiso justificable (el descuento exige comprometer volumen por un plazo determinado, no incorporado en el escenario base), o con Fargate Spot para cargas de trabajo tolerantes a interrupciones (descuento de hasta un 70% sobre el precio on-demand, según AWS).
Timeout máximo de 15 minutos y sistema de archivos local efímero en Lambda (/tmp escribible, configurable de 512 MB a 10.240 MB): aceptable para pipelines de eventos cortos; puede requerir la descomposición del procesamiento o el uso de otros servicios cuando una etapa individual supera este límite.
Limitaciones del Análisis
Este modelo asume una distribución temporal uniforme y capacidad Fargate mantenida continuamente activa. Cargas en ráfaga (bursty), Savings Plans, Fargate Spot, cold starts, paralelismo, overhead de inicialización y costos auxiliares (red, CloudWatch, NAT Gateway, ECR, free tier) no incorporados al escenario base desplazan el punto de equilibrio y deben calcularse caso por caso. El modelo asume además que cada ejecución representa una unidad de trabajo independiente y que el throughput del contenedor es suficiente para procesar esas unidades a la tasa considerada.

Importante: esto no es un benchmark de rendimiento entre Lambda y Fargate —es un modelo económico. El volumen mensual, por sí solo, no equivale a la capacidad necesaria: la distribución temporal de la carga es la variable que decide el resultado.

Decisión
No existe un ganador único —existe un punto de equilibrio que debería calcularse para la carga de trabajo real, no asumirse por defecto arquitectónico. Por debajo de él, Lambda tiende a ganar en costo y simplicidad operacional. Por encima, el contenedor administrado tiende a ganar en costo, a precio de una gestión adicional —y ambas lecturas dependen de la distribución temporal de la carga, no solo del volumen mensual.

La decisión correcta, por lo tanto, no es "Lambda o Fargate"; es "¿qué función de costo representa mejor la carga de trabajo que tendremos?"

Regla de decisión de este modelo:

< ~540 mil ejecuciones/mes: Lambda tiende a ser económicamente favorecido.
≈ 540 mil: zona de equilibrio.
> ~540 mil, carga uniforme: Fargate tiende a presentar un menor costo de compute en el modelo.
Carga en ráfaga (bursty): recalcular —el volumen mensual por sí solo no determina la capacidad.
Resultado del Modelo
Bajo las premisas de este modelo, el punto de equilibrio ocurre en aproximadamente 540 mil ejecuciones/mes para una tarea Fargate de 1 vCPU + 2 GB mantenida continuamente activa.

En 10 millones de ejecuciones/mes, el modelo estima US668,67/mes para Lambda frente a US288,32/mes para Fargate —una diferencia de aproximadamente US$380/mes.

El resultado no significa que Fargate sea "mejor". Significa que, para este perfil de carga de trabajo y estas premisas, la función de costo cambia de lado.

Impacto Potencial
En el escenario analizado, la elección entre ambas arquitecturas representa una diferencia estimada de aproximadamente US380/mes, o cerca de US4,6 mil/año.

Este valor no representa un ahorro real ejecutado en producción. Es el resultado de un modelo bajo premisas explícitas. El objetivo no es afirmar que Fargate siempre será más barato, sino mostrar que la arquitectura debería reevaluarse cuando la carga, el patrón temporal o el costo unitario cambian.

El error no es elegir Lambda o contenedor. Es no recalcular la elección cuando el volumen cambia de orden de magnitud —y el volumen mensual, por sí solo, tampoco basta: la distribución temporal de la carga determina cuánta capacidad debe mantener el contenedor.

Actualización Tecnológica
En noviembre de 2025, AWS lanzó Lambda Managed Instances: funciones Lambda que se ejecutan sobre capacidad EC2 aprovisionada y administrada por el propio servicio Lambda, incluyendo el aprovisionamiento, escalado, parches y ciclo de vida. El modelo utiliza precios de EC2, permite aplicar Savings Plans y Reserved Instances al compute subyacente y añade una tarifa de gestión del 15% sobre el precio On-Demand de la instancia EC2. El servicio también comenzó a proporcionar registros del sistema del proveedor de capacidad en CloudWatch, ampliando la visibilidad sobre eventos relacionados con el ciclo de vida y la capacidad administrada.

La diferencia arquitectónica importante es que Managed Instances no sustituye simplemente Fargate por Lambda: permite múltiples invocaciones concurrentes en el mismo entorno de ejecución, según la configuración y el runtime, alterando justamente la función de costo analizada en esta edición.

Esto no invalida el cálculo de esta edición —pero desplaza la frontera de la decisión. Lambda Managed Instances no es simplemente una tercera opción equivalente entre el Lambda tradicional y Fargate: es la experiencia de desarrollo serverless combinada con el modelo de precios y capacidad de EC2, especialmente relevante para cargas de trabajo de estado estable y predecible —justamente uno de los perfiles que hace que esta discusión económica sea aún más relevante. Por ello, es contexto esencial para cualquier equipo que rehaga este análisis a partir de ahora: la tecnología evoluciona, pero la necesidad de calcular la función de costo antes del despliegue sigue siendo exactamente la misma.

Fuentes y Referencias
AWS Lambda Pricing — precios de peticiones y duración
AWS Fargate Pricing — precios de vCPU/hora, memoria/hora y Fargate Spot
AWS Lambda — Tiered Pricing — descuentos por volumen de GB-segundos
AWS Lambda Managed Instances — modelo de ejecución, concurrencia y precios
Valores de referencia: agosto/2026. Los precios y porcentajes de descuento de AWS están sujetos a cambios —consulte la documentación oficial para conocer los valores vigentes.

Próxima edición
Docker, CI/CD y suite de pruebas: cuándo esta canalización de MLOps es madurez técnica —y cuándo es sobreingeniería disfrazada de buenas prácticas en un proyecto que aún no ha llegado a producción.

Ingeniería de Decisiones — arquitectura, trade-offs y evidencias reales en Cloud, IA y Datos.







