# LinkedIn Post Creation Prompt Library
## Reviewed, consolidated, and optimized

This document consolidates the original LinkedIn post and infographic prompts into a practical reusable system. Repeated image-generation prompts and overlapping post structures have been merged. Use one master prompt and select a writing style or visual style only when it adds value.

---

## 1. Recommended workflow

For each post:

1. Choose a topic.
2. Decide the post's goal: educate, explain a production problem, share a lesson, compare approaches, or discuss a new technology.
3. Generate the post using the **Master LinkedIn Post Prompt**.
4. If the post benefits from a visual, use one of the **Image Prompt Templates**.
5. Verify technical claims, commands, version-specific details, and any metrics before publishing.

**Recommended default:** Use the problem-led post structure for most DevOps, DevSecOps, Kubernetes, cloud, CI/CD, reliability, observability, and AIOps topics. Use the scenario-led structure for security risks, architecture decisions, and high-stakes operational scenarios.

---

## 2. Master LinkedIn Post Prompt

Copy this prompt and replace `[TOPIC]`.

```text
Create a high-quality LinkedIn post about:

TOPIC: [TOPIC]

AUDIENCE:
DevOps, DevSecOps, SRE, cloud, Kubernetes, platform, and software engineering professionals.

GOAL:
Teach one useful technical idea, explain a real engineering challenge, or help readers make a better technical decision.

Before writing:
- Identify the single most useful angle for this topic.
- Choose a structure that suits the topic instead of forcing every topic into the same template.
- Keep technical details accurate and distinguish established facts from assumptions.
- Do not invent personal experience, production incidents, metrics, benchmarks, customer outcomes, or quotations.
- If the topic is version-specific or time-sensitive, flag details that need verification.

POST STRUCTURE:
Prefer this flow for problem-led posts:
1. HOOK: Open with a concise, specific engineering problem, surprising insight, or realistic scenario.
2. PROBLEM: Explain what goes wrong and why it is difficult to prevent, detect, or resolve. Give this section the most space when the problem is the main point.
3. ROOT CAUSE: Explain the underlying technical reason rather than restating symptoms.
4. IMPACT: Describe only the relevant effects on reliability, security, cost, performance, developer experience, delivery speed, or operational workload.
5. SOLUTION: Explain how [TOPIC] or the relevant approach addresses the root cause. Include 3–5 practical steps, design choices, or technical details when useful.
6. TRADE-OFF: Where relevant, mention a limitation, operational cost, failure mode, or situation where the approach may not fit.
7. TAKEAWAY: End with one memorable engineering lesson.
8. DISCUSSION: Ask one genuine, specific technical question.

Adapt the structure when another format better fits the topic:
- Scenario-led: scenario → risk → solution → 3–5 practical points → lesson → question.
- Step-by-step: context → prerequisites → numbered steps → validation → common pitfalls.
- Comparison: use case → options → trade-offs → recommendation → question.
- Incident analysis: symptoms → evidence → root cause → fix → validation → prevention.
- New technology or feature: problem it addresses → how it works → practical use cases → limitations → takeaway.
- Opinion or lesson learned: position → reasoning → example or evidence → counterpoint → question.

WRITING STYLE:
- Professional, clear, conversational, and technically credible.
- Write for an experienced engineering audience without making the post unnecessarily difficult for beginners.
- Use short paragraphs, whitespace, and concrete language for mobile readability.
- Use bold text sparingly for key ideas; use arrows or bullets only when they improve scanning.
- Use 1–3 relevant emojis at most; do not decorate every paragraph.
- Avoid generic motivational phrases, exaggerated claims, clickbait, filler, and repeated conclusions.
- Explain acronyms the first time if the likely audience may not know them.
- Include a code snippet or command only if it genuinely helps; check syntax and explain what it does.
- Use realistic examples. Label hypothetical scenarios as hypothetical.
- Keep claims proportional to the evidence.

LENGTH:
Aim for 1,200–1,600 characters including spaces unless I request another length. If the topic needs a tutorial or detailed explanation, prioritize clarity over an arbitrary character limit.

ENDING:
Finish with one useful takeaway, one discussion question, and 3–5 relevant hashtags. Avoid asking multiple questions.

OUTPUT:
1. The finished LinkedIn post.
2. A short technical accuracy checklist only if the topic contains commands, security advice, version-specific claims, or potentially risky operational steps.
3. Do not explain your writing process unless asked.

TOPIC: [TOPIC]
```

---

## 3. Alternative post styles

Use one style only when it better fits the topic. The master prompt already includes these choices, so do not paste all of them into every request.

### Style A — Problem-led technical post
Best for production issues, Kubernetes failures, CI/CD problems, security gaps, reliability, and observability.

**Flow:** Hook → Problem → Root cause → Impact → Solution → Trade-off (if relevant) → Takeaway → One question.

Instruction:
```text
Focus strongly on the problem and its root cause. Show why common fixes may only treat symptoms. Provide 3–5 practical, technically sound actions and end with one memorable lesson and one specific question.
```

### Style B — Scenario-led post
Best for security, enterprise risk, architecture, and incident prevention.

**Flow:** Hypothetical scenario → Why it matters → Risk → Solution → 3–5 practical points → Lesson → Question.

Instruction:
```text
Open with a clearly hypothetical but realistic scenario. Explain the operational or security risk, then provide 3–5 practical controls or design decisions. Avoid presenting the scenario as a real personal incident.
```

### Style C — Step-by-step tutorial
Best for implementation guides, commands, pipelines, and configuration.

**Flow:** Goal → Prerequisites → Steps → Validation → Common mistakes → Takeaway.

Instruction:
```text
Write a practical step-by-step guide. State prerequisites, show safe commands or configuration where useful, explain expected results, and include validation and cleanup steps when applicable. Warn before destructive actions.
```

### Style D — Comparison or decision guide
Best for tool comparisons, architecture choices, and trade-offs.

**Flow:** Decision to make → Options → Comparison criteria → Trade-offs → Recommendation by use case → Question.

Instruction:
```text
Compare the options fairly using criteria that matter in practice. Explain where each option fits, its operational trade-offs, and what evidence would change the recommendation. Do not declare a universal winner without context.
```

### Style E — New feature or technology
Best for product releases, new versions, emerging tools, and technical trends.

**Flow:** Problem → What changed → How it works → Use cases → Limitations → Takeaway → Question.

Instruction:
```text
Explain what the feature or technology does, what problem it addresses, where it is useful, and what its limitations are. Verify current or version-specific facts before making claims.
```

### Style F — Engineering lesson or opinion
Best for lessons learned, engineering principles, and thoughtful opinions.

**Flow:** Clear position → Reasoning → Example or evidence → Trade-off/counterpoint → Lesson → Question.

Instruction:
```text
Present a clear, defensible engineering point of view. Support it with reasoning or evidence, acknowledge a meaningful counterpoint, and finish with a practical lesson rather than generic inspiration.
```

---

## 4. Master Image-Generation Prompt

Use this for a technical infographic when a visual genuinely helps explain the topic. Replace the bracketed fields and remove instructions that do not apply.

```text
Create a premium, technically accurate LinkedIn infographic about:

TOPIC: [TOPIC]
VISUAL FORMAT: [workflow / architecture diagram / comparison / troubleshooting flow / exploded-view diagram]
TARGET AUDIENCE: DevOps, DevSecOps, SRE, cloud, Kubernetes, and software engineering professionals.
KEY MESSAGE: [ONE main message]
REQUIRED COMPONENTS: [list only components relevant to this topic]
FLOW OR RELATIONSHIPS: [describe sequence, dependencies, and direction of data/control flow]
LABELS: [list essential short labels]

LAYOUT:
- Use a clear visual hierarchy with one dominant title, a central diagram, and a small number of supporting callouts.
- Arrange components in a logical left-to-right workflow or clearly separated architectural layers.
- Use directional arrows that accurately represent the sequence and relationships.
- Distinguish security controls, trust boundaries, human approvals, failure paths, and feedback loops where relevant.
- Keep labels short, legible, correctly spelled, and attached to the correct components.
- Use only topic-relevant technologies and components. Do not add generic or unrelated icons.

VISUAL STYLE:
Premium enterprise SaaS aesthetic; modern technical editorial design; clean spacing; restrained glassmorphism only where it improves clarity; realistic depth without visual clutter; polished lighting and crisp edges.
Palette: 70% white or very light neutral, 25% deep navy (#0B1F3A), and 5% electric blue (#2563EB). Maintain strong contrast and readable text.
Typography: clean sans-serif, consistent sizes, clear hierarchy, no tiny paragraphs.
Composition: landscape 16:9, high resolution, suitable for a LinkedIn feed, with safe margins around all text.

TECHNICAL ACCURACY:
- Preserve correct architecture, component relationships, dependencies, and flow direction.
- Do not invent features, integrations, protocols, or security guarantees.
- Do not imply that a tool provides a capability unless it actually does.
- Avoid fake dashboards, meaningless code, duplicated components, crossed arrows, and decorative elements that obscure the design.
- If exact text rendering is unreliable, keep labels concise and use a clean layout that can be recreated with editable text.

OUTPUT:
One finished infographic. No mockup of a social-media post, no device frame, no watermark, no unrelated logos, and no extra explanatory text outside the graphic.
```

**Tip:** Image generators may misspell technical labels. For diagrams with many exact labels, generate the visual layout with minimal text and add labels afterward in a design tool.

---

## 5. Image styles for different technical topics

Choose **one** style per image. The original prompt collection repeated similar premium isometric, cinematic, glassmorphism, and 3D instructions; these have been consolidated here.

### A. Workflow infographic
Use for CI/CD, GitOps, incident response, and deployment pipelines.

```text
Show a clear step-by-step workflow from [START] to [END]. Use numbered stages, directional arrows, concise labels, and visible decision points. Show security checks, approvals, failure handling, and rollback only where they belong in the actual workflow.
```

### B. Architecture diagram
Use for AWS, EKS, networking, observability, and cloud-native systems.

```text
Show the actual architecture of [TOPIC] with accurate boundaries, components, connections, trust zones, and traffic/data flows. Group related components into layers. Use leader lines and concise labels. Prioritize architectural correctness over decorative 3D effects.
```

### C. Cinematic 3D exploded view
Use when the layered structure or internal components are the point of the post.

```text
Create a premium cinematic 3D exploded-view infographic of [TOPIC], separating its major components into clearly labeled architectural layers. Show accurate dependencies, data/control flows, networking, security, integrations, and key workflows. Use an isometric perspective, leader lines, organized spacing, restrained blue/cyan accents, realistic materials, and cinematic lighting. Keep the layout technically precise and uncluttered.
```

### D. Troubleshooting flow
Use for ImagePullBackOff, CrashLoopBackOff, OIDC errors, failed deployments, and similar issues.

```text
Visualize troubleshooting for [PROBLEM] as a decision tree: symptom → diagnostic check → evidence → likely cause → safe fix → validation. Distinguish observations from possible causes. Include only correct, non-destructive commands where they add value. Show a clear branch for cases where the first fix does not work.
```

### E. Before-and-after comparison
Use for architecture improvements, security controls, reliability, and cost optimization.

```text
Create a side-by-side comparison of [BEFORE] versus [AFTER]. Use the same criteria on both sides, show the specific architectural or operational differences, and highlight the intended benefit without inventing numerical improvements. Keep trade-offs visible where relevant.
```

### F. Minimal technical visual
Use for a simple concept, a single principle, or a short LinkedIn post.

```text
Create a minimal technical editorial graphic explaining [TOPIC] with one central visual metaphor, a concise headline, and no more than three supporting labels. Use a white background, deep navy, and electric-blue accents. Prioritize immediate understanding and legibility at mobile size.
```

---

## 6. Topic-specific example: End-to-End DevSecOps CI/CD Pipeline

This is a ready-to-copy example of the image prompt tailored to a specific topic.

```text
Create a premium landscape 16:9 technical infographic for LinkedIn titled:
“END-TO-END DEVSECOPS CI/CD PIPELINE”

KEY MESSAGE:
Security is integrated throughout delivery, from source code to production—not added only at the end.

WORKFLOW, LEFT TO RIGHT:
1. Developer commits code to GitHub.
2. Source control triggers the CI pipeline.
3. Secret scanning with Gitleaks.
4. Static analysis with Semgrep and SonarQube.
5. Unit tests and coverage checks.
6. Dependency and software composition analysis.
7. Infrastructure-as-Code scanning with Checkov.
8. Container build.
9. Container image scanning with Trivy.
10. SBOM generation with Syft.
11. Image signing with Cosign.
12. Push the verified image to Amazon ECR.
13. GitOps configuration update.
14. Argo CD deploys to Kubernetes/Amazon EKS.
15. Runtime policy enforcement and workload security.
16. Monitoring with Prometheus and Grafana, plus alerting and feedback to engineering.

VISUAL REQUIREMENTS:
- Show the pipeline as connected stages with clear arrows.
- Group stages into Source, CI & Testing, Security & Supply Chain, Artifact Registry, GitOps Deployment, and Runtime Observability.
- Show failures in quality or security gates stopping promotion until resolved.
- Show image provenance and signing as supply-chain controls.
- Show human approval only if the depicted deployment policy requires it.
- Show a feedback loop from production monitoring back to engineering.
- Do not imply every tool is mandatory in every pipeline or that a scan guarantees complete security.

STYLE:
Premium enterprise technical infographic, clean white background, deep navy (#0B1F3A), electric blue (#2563EB), restrained cyan highlights, crisp sans-serif typography, subtle 3D depth, generous spacing, strong contrast, and minimal clutter. Keep labels readable on mobile. Landscape 16:9, high resolution, safe margins, no watermark, no unrelated logos.
```

---

## 7. Quality checklist before publishing

### LinkedIn post
- [ ] The opening is specific and relevant to the intended audience.
- [ ] The post teaches one main idea rather than several unrelated ideas.
- [ ] Root cause and impact are technically credible.
- [ ] Recommendations are practical and appropriately qualified.
- [ ] Any commands, versions, statistics, and security claims have been checked.
- [ ] No personal incident, result, metric, or experience has been fabricated.
- [ ] The ending contains one useful takeaway and one genuine question.
- [ ] There are 3–5 relevant hashtags and no unnecessary hashtag stuffing.
- [ ] The post is readable on mobile and contains no repeated sections.

### Infographic
- [ ] The title and key message are immediately understandable.
- [ ] Every component belongs to the stated topic.
- [ ] Arrows and dependencies are technically accurate.
- [ ] Text is readable at feed size.
- [ ] Color contrast is strong and the palette is consistent.
- [ ] No incorrect labels, fake metrics, or decorative clutter remain.

---

## 8. What was consolidated from the original prompt collection

- Merged the three overlapping post structures into one master prompt plus optional styles.
- Consolidated repeated 3D/isometric/glassmorphism prompts into one master image prompt and six focused visual styles.
- Kept the useful 60/30/10 and 70/25/5 palette ideas, choosing **70% light neutral, 25% navy, 5% electric blue** as the default.
- Added a topic-adaptive rule so simple topics do not get forced into a long incident format.
- Added trade-offs, technical verification, safe command handling, and a rule against fabricated metrics or personal experiences.
- Clarified that visuals should prioritize accurate technical relationships over decorative effects.
- Preserved the original problem → root cause → impact → solution → takeaway approach as the recommended default.

## 9. Quick-use request

For everyday use, you can simply send:

```text
Create a LinkedIn post and a matching infographic prompt.

Topic: [TOPIC]
Audience: DevOps / DevSecOps / SRE / Cloud engineers
Post style: Choose the most suitable style
Length: 1,200–1,600 characters
Visual style: Choose the most suitable infographic style
Requirements: Technically accurate, practical, concise, no invented metrics or experience.
```
