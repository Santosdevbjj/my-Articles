## Engineering Decisions

## Issue #03 — Docker, CI/CD, and Test Suites: Technical Maturity or Disguised Over-Engineering?

This newsletter examines engineering decisions made in response to real-world problems. Each issue presents the context, decision criteria, alternatives considered, accepted trade-offs, and the evidence supporting the choice.

No tutorial. No tool list. Decision, criteria, evidence, outcome.

Today: the MLOps pipeline — Docker, CI/CD, and automated testing — and when it is the right time to invest in it.

Baseline

There is an almost automatic consensus in the market: “serious projects have Docker, CI/CD, and tests.” This consensus makes sense when a model needs to be reproducible, testable, deployable, and continuously operated. The problem begins when these practices stop being responses to concrete risks and start being treated as mandatory requirements from the very first experiment.

The relevant point for this analysis is different: when a model already needs to operate repeatedly, reproducibly, and under control, MLOps practices begin to address risks that simply do not exist — or are not yet relevant — during the initial exploration phase. A team can spend three weeks configuring CI/CD pipelines and multi-stage Docker images for a model that has not yet demonstrated predictive value — roughly 360 engineering hours, even before considering maintenance costs, invested in a hypothesis that could have been discarded after 48 hours of simple exploration with a notebook and Git.

The MLOps pipeline is neither inherently good nor bad. It is a response to specific risks. Applied at the right time, it is maturity. Applied too early, it is over-engineering disguised as good practice.

Problem

How do you decide when to invest in Docker, CI/CD, and an automated test suite in a Machine Learning project, so that the pipeline accelerates delivery instead of delaying validation of the business hypothesis?

Decision Criteria

The question is not “does every mature team use these tools?” It is:

- What is the project's current bottleneck: validating whether the model has value, or reliably delivering a model that has already proven its value?

- Is there a real user, a critical system, or multiple collaborators depending on the code?

- Has environment instability (dependencies, library versions) already caused real failures, or is it only a hypothetical risk?

- Is the cost of setting up the pipeline lower than the cost of the risk it mitigates?

These criteria — rather than adopting tools as a symbol of seniority — should determine the architecture.

Alternatives Considered

Notebook + simple Git. Local environment, basic version control, with no containerization or automation. Suitable for data exploration and hypothesis validation. Very low setup cost, but limited reproducibility outside the development environment.

Structured scripts + basic Dockerfile + contract tests and input/output validation. The notebook becomes organized Python code. A Dockerfile reduces dependency on the local environment and increases execution reproducibility. Simple tests validate schemas, data types, and response formats — without yet covering training or deployment. Intermediate friction, still without automated deployment.

Automated delivery pipeline: Docker + CI/CD + test suite (unit, integration, and model validation). Changes go through automated validations and can be packaged and promoted across environments according to delivery policies. It requires setup time and continuous maintenance, but eliminates manual deployment and silent regressions.

Decision

Adopt a gradual evolution in three phases, associating each infrastructure layer with a concrete risk it addresses — rather than with the project phase by architectural convention:

1. Discovery (PoC): Git and a local environment, with a focus on validating the hypothesis and the quality of the signal in the data. PoC does not mean a mess: it may include Git, a README, a virtual environment, "requirements.txt", an organized notebook, a baseline, metrics, and data validation — without necessarily having Docker, CI, CD, or a registry. Automated tests are introduced only when the code starts being reused or when a logic failure threatens the speed of experimentation. Cross-validation evaluates model behavior — it does not replace software tests, which verify that a function continues to do what it is supposed to do.

2. Operationalization (MVP): the notebook becomes a structured script. A "requirements.txt" or basic Dockerfile ensures that the code can run outside the original machine. Contract tests validate data schemas, types, and input/output formats.

3. Production and scale (more complete MLOps): CI/CD, data tests, model tests, artifact management, automated deployment, and monitoring and rollback mechanisms, according to the system's risks.

The decision was not “MLOps always” or “MLOps never” — it was specific to the criterion defined above: each component of the pipeline exists to control a risk, and a risk that has not yet been identified or materialized may not justify the mechanism designed to control it.

Before investing in operational automation, there is an earlier prerequisite: the model needs to demonstrate that there is measurable value to be operationalized — normally compared against a baseline, a simple business rule, or a previous version. This does not mean that all infrastructure must wait for the baseline: a concrete risk involving reproducibility, collaboration, or security may justify controls before that point. Production infrastructure is not the first investment in maturity; it is the response to a problem whose value and risks already justify the control.

Accepted Trade-offs

- Less infrastructure rigor during the discovery phase — accepted because, without hypothesis validation, any investment in Docker or CI/CD is effort spent on a project that may be discarded within days.

- Rewriting Dockerfiles and pipelines when moving between phases — accepted because trying to anticipate the production architecture during experimentation creates friction precisely when the team most needs speed to test hypotheses.

- Greater operational complexity during production (pipeline maintenance, monitoring, container management) — accepted because, at this stage, the cost of an environment failure or an undetected regression is greater than the cost of maintaining the pipeline.

- Model metric thresholds treated with caution — a predefined quality metric can function as a CI gate, but it should not be applied so rigidly that a model with superior business performance is rejected simply because it fails to meet a single isolated indicator.

Decision Rule

Every engineering control has a cost — not only implementation cost, but also continuous maintenance: Docker requires image updates and security patches; CI requires maintaining and monitoring the pipeline; tests must be written, updated, and reviewed; CD requires secrets management, permissions, and observability of the rollback process. The mistake is not implementing controls. The mistake is ignoring the cost of maintaining them.

This allows the decision to be formalized as a simple rule:

«Introduce an engineering layer when the expected cost of the risk it reduces is greater than the cost of implementing and maintaining that layer. Otherwise, keep things simple.»

This rule is what makes the decision auditable — it does not depend on how “professional” an architecture looks, but on an explicit comparison between the cost of the risk and the cost of the control.

Evidence

Evidence of Convergence Among Providers — The Maturity-Level Model

Google Cloud, AWS, Microsoft Azure, and Red Hat use different taxonomies for MLOps, but converge on a common principle: automation should be progressive, increasing as the needs for reliability, repeatability, and operation at scale grow.

- Google Cloud and AWS: define 3 explicit levels — Level 0 (manual process), Level 1 (automated pipeline with continuous training), and Level 2 (full end-to-end CI/CD).

- Microsoft Azure: uses a more granular model with 5 levels of technical capability — from “No MLOps” to “Full MLOps automated operations” — and details an intermediate level, “DevOps but no MLOps,” in which application code builds and tests are already automated, while model training and deployment remain manual. This level is compatible with part of the MVP phase described in this analysis, especially when application code already has automation but the model training and deployment lifecycle remains manual. Microsoft also explicitly states that the model should be used for gradual progression: organizations may exhibit characteristics of more than one level simultaneously, as a continuum rather than as a rigid sequence of isolated stages.

- Red Hat: categorizes both model lifecycle stages and maturity by level of automation, describing three levels of progression from manual workflows to full CI/CD.

These models do not describe maturity as a direct leap to maximum automation: they present a progression of capabilities, from manual operation to increasing levels of automation and control. This convergence supports the decision to evolve the pipeline as risks and operational needs increase — not as definitive proof of the decision, but as evidence consistent with it.

Evidence of Risk — Each Component Controls a Specific Problem

Real Project Risk| MLOps Mechanism| When is the investment justified?
Inconsistent environment across machines| Docker| Execution across multiple environments or by more than one person.
Undetected code regression| CI + Unit Tests| Code is reused and subject to frequent changes.
Manual deployment error| Automated CD| Recurring deployments or deployments with significant operational impact.
New model with worse performance| Automated Validation| The model already competes with a baseline or previous version, using predefined metrics.
Change in data distribution| Drift Monitoring| The model receives real-world data continuously.
Need to revert a version| Registry + Rollback| Failure of the new version has significant business impact.

This is the difference between “having the tool” and “controlling the risk”: a Dockerfile in a project that does not yet have a concrete need for reproducibility across environments may not be controlling a risk proportional to its maintenance cost — it may simply be adding complexity too early. This does not mean Docker requires production, an API, or a real user: even in a research project with none of these three, differences in library versions across different machines are already a legitimate reproducibility risk in their own right.

Illustrative Scenario — The Cost of Doing Things in the Wrong Order

Consider a hypothetical churn prediction scenario: a team spends weeks configuring Docker, CI/CD, and automated validation before the first training run. When the model is finally trained in the fourth week, the team discovers that the available historical data does not contain sufficient predictive signal — the model performs worse than the historical average used as the baseline. The result: all the infrastructure created generates maintenance costs but delivers zero business value, because the project is discontinued before any model comes close to outperforming the baseline. The correct order would be the reverse: validate the signal in the data within days, using a notebook and Git, before writing a single line of Dockerfile.

Limitations of the Analysis

This analysis does not argue that small projects should remain without tests or reproducibility. The decision point is not project size, but the relationship between risk, cost of failure, change frequency, number of environments involved, and the maintenance cost of the controls. A small project with a high change frequency and multiple collaborators may justify CI before a larger project that runs independently in a single environment without frequent changes. The central thesis is not “PoCs do not need quality” — it is that quality and automation should be proportional to actual risk, not to project size or perceived maturity.

In ML systems, risk is not purely operational: data quality, leakage, bias, security, regulatory requirements, and financial impact may also justify additional controls even before large-scale deployment.

Validation Checklist Before the First Dockerfile

Before writing the first line of your Dockerfile or configuring ".github/workflows", answer:

«[ ] 1. Does the model already outperform a measurable baseline?»

«»

«[ ] 2. Is the code already reused, frequently modified, or shared by more than one person?»

«»

«[ ] 3. Is there a concrete risk of incompatibility or difficulty reproducing the environment?»

«»

«[ ] 4. Will the model be integrated into a real system or operational workflow?»

«»

«[ ] 5. Is the cost of automation lower than the expected cost of the failures the automation is intended to prevent?»

The more answers are YES, the stronger the case for introducing engineering controls. But there is no magic number of answers: the decision depends on the risk each control reduces.

Impact

Treating the MLOps pipeline as a response to risk rather than as a mandatory checklist has a dual benefit: it prevents weeks of infrastructure effort from being spent on hypotheses that have not yet been validated, and increases the likelihood that automation will be introduced when its cost becomes lower than the risk it helps control.

«The right question is never “Do I need CI/CD?” It is “What risk am I trying to control, and does that risk already exist in my project?”»

This reframing transforms tool selection into an auditable decision — the same logic of Baseline, Criteria, Alternatives, and Trade-offs that supports the first two issues of this newsletter, now applied to the very way an ML project is built and delivered.

Next Issue

Model monitoring and data drift: how to know whether a model that passed all CI/CD tests is still correct — and why a flawless pipeline can be delivering incorrect predictions automatically.

Sources and References

- "Google Cloud — MLOps: Continuous delivery and automation pipelines in machine learning" (https://docs.cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning?hl=pt-br) — maturity levels 0, 1, and 2

- "AWS — What is MLOps?" (https://aws.amazon.com/pt/what-is/mlops/) — practices for automating and standardizing development, testing, integration, release, and infrastructure

- "Databricks — MLOps vs DevOps: A Practical Guide" (https://www.databricks.com/br/blog/mlops-vs-devops) — definition of MLOps combining DevOps, DataOps, and ModelOps

- "Microsoft Azure — MLOps Maturity Model" (https://learn.microsoft.com/pt-br/azure/architecture/ai-ml/guide/mlops-maturity-model) — 5-level model (0 to 4) and incremental evolution

- "Red Hat — What is MLOps?" (https://www.redhat.com/pt-br/topics/ai/what-is-mlops) — MLOps as an evolution of DevOps and levels of automation

Maturity classifications cited in this issue reflect the providers' official documentation as of the publication date — consult the sources for any updates.

Engineering Decisions — architecture, trade-offs, and real-world evidence in Cloud, AI, and Data.Essa versão procura manter o significado técnico e a lógica argumentativa do original, em vez de fazer uma tradução palavra por palavra. Se a intenção for publicar no LinkedIn/Medium internacionalmente, também posso fazer uma segunda passada editorial em inglês nativo, preservando suas ideias, mas ajustando expressões para soarem como uma newsletter escrita originalmente por um profissional de Data/ML Engineering em inglês.
