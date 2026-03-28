# Cognitive Foundations Reference

Condensed framework reference explaining WHY design choices were made. Read this file when
you need to understand the rationale behind a protocol decision, or when making a judgment
call not explicitly covered by the main skill or other reference files.

---

## The Three Core Principles and Their Sources

### Principle 1: Expertise Level Is the Master Variable

**Source:** Expertise Reversal Effect (Kalyuga & Sweller, 2003; strong meta-analytic evidence)

Instructional supports effective for novices become actively harmful for experts. Worked
examples help novices build schemas but waste experts' time and interfere with their
existing efficient strategies. This is not a preference — it's a cognitive mechanism.

**Design implication:** Diagnose expertise BEFORE any other design decision. If you get
expertise wrong, every downstream choice (scaffolding level, technique selection, prompt
structure) is miscalibrated. This is why the expertise probe is the first question in
every interview protocol.

**Connected framework:** Schema Acquisition (Chase & Simon, 1973). Experts have rich schemas
that compress many facts into single retrievable units. Adding scaffolding to an expert
means adding extraneous load to someone whose schemas already handle the intrinsic load
efficiently.

### Principle 2: High Complexity in Content, Low Complexity in Structure

**Sources:**
- Prompt brittleness research (Sclar et al. 2024, Mizrahi et al. 2024): Trivial formatting
  changes significantly affect LLM output. Elaborate prompts optimize for noise.
- Levy, Jacoby & Goldberg (2024): Reasoning performance degrades around 3,000 tokens.
  Sweet spot is 150–300 words.
- Baker (2015) "Stupid Tutoring Systems, Intelligent Humans": The most widely deployed ITS
  are dramatically simpler than research envisions. Effectiveness comes from domain models,
  not sophisticated pedagogical engines.
- ITS failures (Knewton, AltSchool): Cognitive science language was used to sell products
  whose effectiveness barely exceeded simpler alternatives.

**Design implication:** Keep the adaptive protocol structurally simple (frame activation →
slot filling → generate). Invest complexity in the CONTENT of diagnostic questions and
prompt templates, not in multi-layered architectural logic. A simple prompt with rich,
targeted content outperforms an elaborate prompt with generic content.

### Principle 3: Generation Over Recognition

**Sources:**
- Retrieval Practice / Testing Effect (Roediger & Karpicke, 2006): The only learning
  technique rated "high utility" in Dunlosky et al.'s 2013 comprehensive review. Actively
  recalling information strengthens memory more than re-studying.
- Six metacognitive failure patterns in LLM-mediated learning:
  1. Fluency illusion — clear explanation feels like understanding
  2. Copy-paste comprehension — following steps without being able to adapt
  3. Recognition vs. recall confusion — selecting right answers vs. generating them
  4. Question-answer loops without synthesis — individual answers understood, structure lost
  5. Premature abstraction — jumping to principles without concrete grounding
  6. Surface-level Socratic engagement — minimal effort to get through dialogue

**Design implication:** Every learning/understanding prompt must include at least one measure
that forces the user to generate, not just consume: self-test, teach-back, novel application,
or reflection prompt. SocraticAI research (2025) found reflection prompts at session
boundaries are the single most effective countermeasure.

---

## Supporting Frameworks

### Cognitive Load Theory (Sweller, 1988)

Working memory has limited capacity (~7±2 chunks). Three types of load compete:
- **Intrinsic:** Inherent complexity of the material (element interactivity)
- **Extraneous:** Unnecessary processing caused by poor instruction design
- **Germane:** Processing directed at building schemas (contested — may not be independent)

**Operationalization:** Minimize extraneous load in prompts (clear structure, no unnecessary
complexity). Segment complex material. Use pre-training (foundations before integration).

**Controversy:** Germane load's independence from intrinsic load is contested (Sweller 2010).
For our purposes, this doesn't matter — the practical advice (reduce extraneous, manage
intrinsic through segmentation) holds regardless.

### Zone of Proximal Development (Vygotsky; Wood/Bruner/Ross 1976)

Learning occurs in the zone between what the learner can do independently and what they can
do with guidance. Too easy = no growth. Too hard = frustration.

**Operationalization:** Keep tasks slightly above the user's current level. This is WHY
expertise diagnosis matters — you need to know their current level to target the zone.

**Limitation:** ZPD boundaries are inherently vague and hard to operationalize precisely.
The practical proxy: if the user signals confusion ("I'm lost"), you've gone too far. If
they signal boredom ("I know this already"), you haven't gone far enough.

### Bloom's Revised Taxonomy (Anderson & Krathwohl, 2001)

Two dimensions: Knowledge Type (Factual, Conceptual, Procedural, Metacognitive) ×
Cognitive Process Level (Remember → Understand → Apply → Analyze → Evaluate → Create).

**Operationalization:** Use verb classification as a complexity signal. "Define" signals
lower complexity than "design." The four knowledge types help ensure prompts address all
dimensions — not just factual but also procedural ("how to") and metacognitive ("how experts
think about this").

**Limitation:** The hierarchy is a classification, not a validated learning sequence. Action
verbs do not reliably predict cognitive level (empirical analysis of 940 items found this).
Use it as a loose heuristic, not a rigid rule.

### Desirable Difficulties (Bjork, 1994)

Strategic difficulty enhances long-term retention: spacing, interleaving, generation. Making
learning easy in the moment can undermine long-term transfer.

**Operationalization:** For intermediate and expert learners, resist making everything easy.
Include challenge questions, require generation before revealing answers, and interleave
related concepts rather than presenting them in neat isolation.

**Constraint:** Must match learner readiness. Desirable difficulties for experts become
undesirable difficulties for novices. This circles back to Principle 1.

### Elaborative Interrogation (Pressley et al., 1987)

Generating "why" explanations activates prior knowledge and strengthens encoding. More
effective with higher prior knowledge.

**Operationalization:** Include "why?" questions in learning prompts, especially for
intermediate and expert learners. For novices, elaborative interrogation is less effective
because they lack the prior knowledge base to generate meaningful explanations.

### Socratic Questioning (Paul & Elder, 2006)

Six categories: clarification, probing assumptions, probing evidence, examining viewpoints,
exploring implications, questioning the question.

**Operationalization:** Use as a question design framework for learning prompts. Strongest
for metacognitive development. Limited controlled experimental evidence — use judiciously.

**Limitation:** Khanmigo's experience shows that Socratic questioning can become burdensome
when direct explanation would be more efficient. The skill should use Socratic elements
within prompts, not make every interaction Socratic.

---

## The Complexity Detection Framework

### Cynefin Framework (Snowden)

Four domains with different response strategies:
- **Clear:** Cause-effect obvious. Best practice exists. → Zero elicitation.
- **Complicated:** Cause-effect discoverable through analysis. Expert knowledge needed.
  → Minimal elicitation to identify which expertise.
- **Complex:** Cause-effect only visible in retrospect. Emergent. → Deep elicitation,
  probe context, stakeholders, tradeoffs.
- **Chaotic:** No cause-effect relationship discoverable. → Immediate action, deferred
  elicitation.

**The catastrophic fold:** Complacent simplicity can cause failure when a complex situation
is pattern-matched to a clear one. Design implication: when in doubt, round UP one
complexity tier.

### The Six Lightweight Signals

These replace sophisticated complexity models with heuristics that work nearly as well
(Duolingo's two-variable logistic regression processes 1.25 billion daily exercises):

1. **Verb type** (strongest single signal): Map to Bloom's levels
2. **Noun phrase count:** Maps to element interactivity
3. **Conditional language:** Signals context-dependency
4. **Constraint completeness:** Whether who/what/when/where/why/how are specified
5. **Interpretation ambiguity:** Whether multiple valid responses exist
6. **Request length:** U-shaped — very short and very long both need clarification

---

## The Frame-Based Interview Model

**Source:** Minsky (1974) frame-based knowledge representation, adapted through knowledge
engineering interview methodology.

A frame is a stereotyped situation with named slots, default values, and constraints. When
the user makes a request, the skill activates a frame (the use case). Each empty slot is a
candidate question. Default values determine which questions to skip. The P×C stopping rule
determines when to stop asking: focus questions where (probability of being wrong) × (cost
of being wrong) is highest; stop when P×C for all remaining slots is below threshold.

**Why this model over alternatives:** Frame-based elicitation is structurally simpler than
Bayesian Experimental Design (the academic state-of-the-art from Cambridge's ICLR 2025
paper) while capturing most of the diagnostic value. It's the "Duolingo approach" to
interview design — simple enough to be robust, rich enough to be useful.

---

## Domain Generality: What Transfers and What Doesn't

**Transfers across domains:**
- Diagnostic question TYPES (probing misconceptions, testing transfer, laddering)
- Interview protocol STRUCTURE (orientation → scope → probing → validation)
- Scaffolding PRINCIPLES (concrete→abstract, fade with competence, target ZPD)

**Does NOT transfer:**
- Which misconceptions to probe for (domain-specific)
- What "understanding" looks like (proof vs. replication vs. judgment)
- Threshold concepts (quantum superposition, Maillard reaction — inherently domain-specific)
- Evidence standards and argumentation norms

**Resolution:** Domain-general question types instantiated with domain-specific content
inferred from the user's responses during the interview. The skill cannot anticipate
domain-specific content — it must elicit it.
