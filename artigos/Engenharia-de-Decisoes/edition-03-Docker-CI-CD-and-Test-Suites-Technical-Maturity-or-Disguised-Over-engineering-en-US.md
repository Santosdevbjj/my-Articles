Decision Engineering
Edition #03 — Docker, CI/CD, and Test Suites: Technical Maturity or Disguised Over-engineering?
This newsletter analyzes engineering decisions made in the face of real-world problems. Each edition presents the context, decision criteria, evaluated alternatives, accepted trade-offs, and evidence supporting the choice.
No tutorials. No lists of tools. Decision, criteria, evidence, result.
Today: the MLOps pipeline—Docker, CI/CD, and automated testing—and the right time to invest in it.
Baseline
There is an almost automatic consensus in the market: "a serious project has Docker, CI/CD, and tests." This consensus makes sense when a model needs to be reproducible, testable, deployable, and operated continuously. The problem begins when these practices stop being responses to concrete risks and start being treated as mandatory requirements from the very first experiment.
The relevant point for this analysis is different: when a model already needs to operate in a recurring, reproducible, and controlled manner, MLOps practices address risks that simply do not exist—or are not yet relevant—during initial exploration. A team can spend three weeks setting up CI/CD pipelines and multi-stage Docker images for a model that has not yet demonstrated predictive value—representing around 360 engineering hours, even before considering maintenance costs, invested in a hypothesis that could be discarded in 48 hours of simple exploration with a notebook and Git.
The MLOps pipeline is neither inherently good nor bad. It is a response to specific risks. Applied at the right time, it represents maturity. Applied too early, it is over-engineering disguised as best practices.
Problem
How do you decide when to invest in Docker, CI/CD, and automated test suites in a Machine Learning project so that the pipeline accelerates delivery instead of delaying the validation of the business hypothesis?
Decision Criteria
The question is not "does every mature team use these tools?" It is:
 * What is the current bottleneck of the project: validating if the model has value, or reliably delivering a model that has already proven its value?
 * Is there a real user, a critical system, or multiple contributors depending on the code?
 * Has environment instability (dependencies, library versions) caused real failures, or is it a hypothetical risk?
 * Is the cost of setting up the pipeline lower than the cost of the risk it mitigates?
These criteria—and not the adoption of tools as a status symbol of seniority—should determine the architecture.
Alternatives Considered
Notebook + Simple Git. Local environment, basic version control, without containerization or automation. Suitable for data exploration and hypothesis validation. Extremely low setup cost, but limited reproducibility outside the development environment.
Structured Scripts + Basic Dockerfile + Contract Tests and Input/Output Validation. The notebook becomes organized Python code. A Dockerfile reduces dependency on the local environment and increases execution reproducibility. Simple tests validate schema, data types, and response format—without covering training or deployment yet. Intermediate friction, still without deployment automation.
Automated Delivery Pipeline: Docker + CI/CD + Test Suite (Unit, Integration, Model Validation). Changes pass through automated validations and can be packaged and promoted across environments according to delivery policies. Requires configuration time and continuous maintenance, but eliminates manual deployment and silent regressions.
Decision
Adopt a gradual evolution across three phases, linking each infrastructure layer to a concrete risk it resolves—rather than treating it as a default architectural requirement:
 * Discovery (PoC): Git and local environment, focusing on validating the hypothesis and signal quality in the data. PoC does not mean a mess: it can include Git, README, virtual environment, requirements.txt, an organized notebook, baseline, metrics, and data validation—without necessarily having Docker, CI, CD, or a registry. Automated tests are introduced only when code begins to be reused or when a logic failure threatens experimentation velocity. Cross-validation evaluates model behavior—it does not replace software testing, which verifies whether a function continues doing what it was designed to do.
 * Operationalization (MVP): The notebook becomes a structured script. A requirements.txt or a basic Dockerfile ensures the code runs outside the source machine. Contract tests validate schema, types, and input/output data formats.
 * Production and Scale (Full MLOps): CI/CD, data tests, model tests, artifact management, automated deployment, and monitoring and rollback mechanisms, aligned with system risks.
The decision was neither "always MLOps" nor "never MLOps"—it was specific to the criterion defined above: each pipeline component exists to control a risk, and a risk that has not yet been identified or materialized may not justify the mechanism that controls it.
Before investing in operational automation, there is a prerequisite: the model must demonstrate measurable value worth operationalizing—typically compared to a baseline, a simple business rule, or a previous version. This does not mean all infrastructure must wait for the baseline: a concrete risk regarding reproducibility, collaboration, or security may justify controls before that. Production infrastructure is not the first investment in maturity; it is the response to a problem whose value and risks already justify the control.
Accepted Trade-offs
 * Lower infrastructure rigor during the discovery phase — Accepted because, without hypothesis validation, any investment in Docker or CI/CD is effort spent on a project that might be discarded in days.
 * Rewriting Dockerfiles and pipelines when migrating phases — Accepted because attempting to anticipate production architecture during experimentation creates friction at the exact moment the team needs speed to test hypotheses.
 * Higher operational complexity in the production phase (pipeline maintenance, monitoring, container management) — Accepted because, at this stage, the cost of an environment failure or an undetected regression is higher than the cost of maintaining the pipeline.
 * Cautious treatment of model metric thresholds — A previously defined quality metric can act as a CI gate, but it should not be applied so rigidly as to reject a model with superior business performance simply because it missed a single isolated indicator.
Decision Rule
Every engineering control carries a cost—not just for implementation, but for ongoing maintenance: Docker requires image updates and security patches; CI requires maintaining and monitoring the pipeline; tests require writing, updating, and reviewing; CD requires managing secrets, permissions, and rollback process observability. The mistake is not implementing controls. The mistake is ignoring the cost of maintaining them.
This allows us to formalize the decision as a simple rule:
> Introduce an engineering layer when the expected cost of the risk it reduces is higher than the cost of implementing and maintaining that layer. Otherwise, keep it simple.
> 
This rule makes the decision auditable—it does not depend on how "professional" an architecture looks, but on an explicit comparison between the cost of the risk and the cost of the control.
Evidence
Evidence of Provider Convergence — The Maturity Model in Levels
Google Cloud, AWS, Microsoft Azure, and Red Hat use different taxonomies for MLOps, but they converge on a common principle: automation must be progressive, scaling up as the needs for reliability, repeatability, and large-scale operations grow.
 * Google Cloud and AWS: Define 3 explicit levels—Level 0 (manual process), Level 1 (automated pipeline with continuous training), and Level 2 (full end-to-end CI/CD).
 * Microsoft Azure: Adopts a more granular 5-level technical capability model—from "No MLOps" to "Full MLOps automated operations"—and details an intermediate level, "DevOps, but no MLOps", where application code builds and testing are automated, but model training and deployment remain manual. This level aligns with part of the MVP phase described in this analysis, especially when application code has automation while the model training and deployment cycle remains manual. Microsoft also explicitly states that the model should be used to progress gradually: organizations can exhibit characteristics from multiple levels simultaneously in a continuum, rather than through a rigid sequence of isolated steps.
 * Red Hat: Categorizes both the model lifecycle stages and maturity level by automation, describing three levels of progression from manual workflows to full CI/CD.
These models do not describe maturity as an immediate leap to maximum automation: they present a progression of capabilities, moving from manual operations to increasing levels of automation and control. This convergence supports the decision to evolve the pipeline as risks and operational needs grow—not as definitive proof of the decision, but as evidence consistent with it.
Evidence of Risk — Each Component Controls a Specific Problem
| Real Project Risk | MLOps Mechanism | When is the investment justified? |
|---|---|---|
| Inconsistent environment across machines | Docker | Execution across multiple environments or by more than one person. |
| Unnoticed code regression | CI + Unit Tests | Code is reused and subject to frequent changes. |
| Manual deployment error | Automated CD | Recurring deployment or significant operational impact. |
| New model with worse performance | Automated Validation | Model already competes against a baseline or previous version using pre-defined metrics. |
| Data distribution drift | Drift Monitoring | Model continuously receives real-world data. |
| Need to revert versions | Registry + Rollback | Failure of the new version has significant business impact. |
This is the difference between "having the tool" and "controlling the risk": a Dockerfile in a project that does not yet face concrete needs for cross-environment reproducibility may not be controlling a risk proportional to its maintenance cost—it might just be adding premature complexity. This does not mean Docker requires production, an API, or a real user: even in a research project without any of those three, library version drift across different machines is, by itself, a legitimate reproducibility risk.
Illustrative Scenario — The Cost of Inverting the Order
Consider a hypothetical churn prediction scenario: a team spends weeks setting up Docker, CI/CD, and automated validation before the first training run. Upon running the model for the first time in week four, they discover that the available historical data lacks sufficient predictive signal—the model performs worse than the historical average used as a baseline. Result: the entire created infrastructure generates maintenance costs but zero value delivered to the business, because the project is discontinued before any model comes close to beating the baseline. The correct order would be the reverse: validate the data signal in days using a notebook and Git before writing a single line of a Dockerfile.
Limitations of the Analysis
This analysis does not argue that small projects should remain without testing or reproducibility. The decision point is not project size, but the relationship between risk, cost of failure, change frequency, number of environments involved, and maintenance cost of controls. A small project with high change frequency and multiple contributors can justify CI earlier than a larger project running alone in a single environment without frequent changes. The central thesis is not "PoCs do not need quality"—it is that quality and automation must be proportional to real risk, not to project size or the appearance of maturity.
In ML systems, risk is not merely operational: data quality, leakage, bias, security, regulatory requirements, and financial impact can also justify additional controls even before large-scale deployment.
Validation Checklist Before Your First Dockerfile
Before writing the first line of your Dockerfile or configuring .github/workflows, answer:
> [ ] 1. Does the model already outperform a measurable baseline?
> [ ] 2. Is the code already reused, frequently altered, or shared by more than one person?
> [ ] 3. Is there a concrete risk of incompatibility or difficulty in reproducing the environment?
> [ ] 4. Will the model be integrated into a real operational flow or system?
> [ ] 5. Is the cost of automating lower than the expected cost of the failures automation aims to prevent?
> 
The more YES answers you have, the stronger the case for introducing engineering controls. However, there is no magic number of affirmative answers: the decision depends on the risk each control mitigates.
Impact
The benefit of treating the MLOps pipeline as a response to risk—rather than a mandatory checklist—is twofold: it prevents weeks of infrastructure effort on unvalidated hypotheses and increases the likelihood that automation is introduced when its cost becomes lower than the risk it helps control.
> The right question is never "do I need CI/CD?" It is "what risk am I trying to control, and does this risk already exist in my project?"
> 
This reframing turns tool selection into an auditable decision—the same logic of Baseline, Criteria, Alternatives, and Trade-offs that powers the first two editions of this newsletter, now applied to how an ML project is built and delivered.
Next Edition
Model monitoring and data drift: how to know if a model that passed all CI/CD tests is still accurate—and why a flawless pipeline might be automatically delivering incorrect predictions.
Sources and References
 * Google Cloud — MLOps: Continuous delivery and automation pipelines in machine learning — Maturity levels 0, 1, and 2
 * AWS — What is MLOps? — Practices to automate and standardize development, testing, integration, release, and infrastructure
 * Databricks — MLOps vs DevOps: A Practical Guide — Definition of MLOps combining DevOps, DataOps, and ModelOps
 * Microsoft Azure — MLOps Maturity Model — 5-level model (0 to 4) and incremental evolution
 * Red Hat — What is MLOps? — MLOps as an evolution of DevOps and automation levels
Maturity classifications cited in this edition reflect the official provider documentation as of the publication date—refer to the sources for any updates.
Decision Engineering — architecture, trade-offs, and real evidence in Cloud, AI, and Data.
