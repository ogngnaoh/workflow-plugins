# Interview Protocol Reference

Complete question bank, branching logic, and stopping rules for the adaptive interview.
Read this file when conducting Complicated or Complex tier interviews.

---

## The Frame-Based Elicitation Pattern

Every interview follows the frame-activation model: the user's request activates a frame
(use case), each frame has named slots, empty slots become candidate questions, and slots
with reliable defaults get skipped. The interview fills slots in priority order until the
P×C threshold is met (probability of being wrong × cost of being wrong is low enough to
proceed).

---

## Universal Protocol (All Use Cases)

### Always Ask First: The Expertise Probe

This is the single most important question. The expertise reversal effect means everything
else — scaffolding level, technique selection, prompt structure — cascades from this answer.

**Template:** "What's your current experience with [topic]? Have you worked with this
before, or is this new territory?"

**Classification from response:**
- **Novice signals:** "I'm new to this," "never done it," "I don't know where to start,"
  no domain vocabulary used, asks what terms mean
- **Intermediate signals:** Uses some domain vocabulary correctly, has tried something but
  hit a wall, can name specific sub-topics, references prior experience with caveats
- **Expert signals:** Uses precise domain vocabulary fluently, names specific tools/methods/
  frameworks, describes nuanced problems, references extensive prior experience

**Branching:**
- If expertise is clearly identifiable from the initial request (e.g., uses advanced domain
  vocabulary), skip this question and classify silently.
- If unsure, ask. Misclassifying expertise is the most costly error.

### Always Ask Second: Success Criteria

**Template:** "What does a great outcome look like for you?"

**Variants by use case:**
- Learn a skill: "What will you be able to DO after this that you can't do now?"
- Understand a concept: "How deep do you need to go — cocktail party explanation, or deep
  enough to teach it?"
- Execute a task: "What does 'done well' look like?"
- Research for a decision: "What will you know after this that lets you make the call?"

---

## Use Case 1: Learn a Skill

### Frame Slots (priority order)

| Slot | Default | Question Trigger |
|---|---|---|
| Current knowledge level | None (must ask) | Always |
| Target competency | "Functional competence" | Ask if vague or very broad skill |
| Learning context | Self-directed | Ask if context affects approach (team, deadline) |
| Available practice environment | General | Ask for procedural/hands-on skills |
| Known blockers | None | Ask if user mentions frustration or failed attempts |

### Question Bank

**Complicated tier (pick 1–2):**
1. Expertise probe (universal)
2. "What specifically do you want to be able to do with [skill]?" — narrows scope

**Complex tier (pick 3–5 across 2 turns):**

*Turn 1 (max 3):*
1. Expertise probe (universal)
2. "What specifically do you want to be able to do with [skill]? Give me a concrete example
   of what success looks like."
3. "Have you tried learning this before? What worked or didn't work?"

*Turn 2 (max 3, selected by gap detection):*
4. "Will you be practicing this in a specific context?" — only if skill is procedural
5. "Is there a timeline or milestone you're working toward?" — only if user mentions urgency
6. "What's the hardest part so far — where do you get stuck?" — only if user has prior attempts
7. "Do you prefer learning by doing, by reading/watching, or by having concepts explained
   first?" — only if no learning style signal in prior responses

### Branching Logic

- User describes specific failed attempts → probe what went wrong ("What happened when you
  tried that?") — this reveals misconceptions to address
- User gives very broad skill → ladder down ("That's a big area — which part matters most
  right now?")
- User specifies concrete practice context → skip question 4, mark slot filled
- User gives crisp competency target → skip question 5

### Stopping Rule

Stop interviewing when you can confidently fill these three critical slots:
1. Expertise level (novice/intermediate/expert)
2. Specific competency target (what they'll be able to do)
3. At least one contextual constraint (timeline, practice environment, or known blocker)

---

## Use Case 2: Understand a Concept

### Frame Slots (priority order)

| Slot | Default | Question Trigger |
|---|---|---|
| Current mental model | None (must ask) | Always |
| Desired depth | "Working understanding" | Ask if ambiguous |
| Purpose of understanding | General interest | Ask if it affects approach |
| Connected knowledge | None assumed | Ask if user references related topics |
| Known misconceptions | None | Probe if user states something that seems off |

### Question Bank

**Complicated tier (pick 1–2):**
1. Expertise probe (universal)
2. "How deep do you need to go — enough to explain it to someone, or enough to apply it
   in practice?"

**Complex tier (pick 3–5 across 2 turns):**

*Turn 1 (max 3):*
1. Expertise probe (universal)
2. "What's your current understanding of [concept]? Even a rough mental model helps."
3. "What will you use this understanding for — is this for a project, a conversation, a
   decision, or pure curiosity?"

*Turn 2 (max 3, selected by gap detection):*
4. "What related topics do you already understand well?" — only if concept has prerequisites
5. "Is there a specific part that confuses you, or is it the whole thing?" — only if user
   signals partial understanding
6. "What have you read or heard about this already?" — only if user seems to have partial
   exposure
7. "Are there specific questions you want answered, or do you want a structured overview?"
   — only if user's goal is still vague

### Branching Logic

- User states a misconception → DON'T correct immediately. Note it. The generated prompt
  should address it. Ask: "Interesting — what makes you think that?" to probe depth.
- User says "I need to explain this to someone else" → adjust success criteria to teach-back
  competency. The prompt must include a "now explain this to a beginner" requirement.
- User references related concepts → use these as anchoring points in the prompt ("Build on
  their understanding of X to explain Y").
- User says "everything confuses me" → classify as novice, use analogy-first approach.

### Stopping Rule

Stop when you can fill:
1. Expertise level
2. Desired depth (surface / working / deep / expert)
3. Purpose (why they need to understand this)

---

## Use Case 3: Execute a Task

### Frame Slots (priority order)

| Slot | Default | Question Trigger |
|---|---|---|
| Output specifications | None (must infer or ask) | Ask if format/structure unclear |
| Constraints | None assumed | Ask if stakes seem high or context-dependent |
| Quality bar | "Competent" | Ask if user signals high standards or specific audience |
| Context/audience | General | Ask if output targets specific recipients |
| Edge cases | None | Ask only for Complex tier |

### Question Bank

**Complicated tier (pick 1–2):**
1. "Who's the audience for this?" OR "What format do you need?" — pick whichever is the
   bigger gap
2. "Anything specific you want included or avoided?"

**Complex tier (pick 3–5 across 2 turns):**

*Turn 1 (max 3):*
1. "Can you describe what the final output should look like? Even roughly."
2. "Who will see or use this, and what do they care about?"
3. "Are there constraints I should know about — length, tone, format, deadlines?"

*Turn 2 (max 3, selected by gap detection):*
4. "What does 'done well' vs. 'done poorly' look like for this?" — only if quality bar is
   unclear
5. "Are there edge cases or tricky parts I should flag?" — only if task has conditional logic
6. "Do you have examples of something similar done well?" — only if user hasn't provided
   reference points
7. "What have you tried so far, and what was wrong with it?" — only if user mentions
   iteration

### Branching Logic

- User provides a very specific brief → minimal questions. Fill slots from stated info,
  generate immediately.
- User provides vague brief with high-stakes signal ("this is for my boss," "client
  presentation") → probe quality bar and constraints more deeply.
- User provides an example of what they want → use it as a few-shot anchor in the prompt.
- User mentions prior failed attempts → probe what was wrong to build negative constraints
  ("avoid doing X").

### Stopping Rule

Stop when you can fill:
1. Output format/structure
2. Primary constraint (the one that matters most)
3. Quality bar OR audience (either one anchors "good enough")

---

## Use Case 4: Research for a Decision

### Frame Slots (priority order)

| Slot | Default | Question Trigger |
|---|---|---|
| Specific decision | None (must ask) | Always |
| Evaluation criteria | None (must ask) | Always |
| Current leaning | None | Ask to enable debiasing |
| Timeline/reversibility | Not urgent / reversible | Ask if stakes seem high |
| Constraints | None | Ask if context-dependent |
| Disconfirmation criteria | None | Ask for Complex tier |

### Question Bank

**Complicated tier (pick 1–2):**
1. "What's the specific decision you need to make?"
2. "What matters most to you in choosing — what are the top 2–3 criteria?"

**Complex tier (pick 3–7 across 2–3 turns):**

*Turn 1 (max 3):*
1. "What's the specific decision? Not the topic — the actual choice you're facing."
2. "What would you most regret getting wrong about this?"
3. "Do you have a leaning already? If so, what's pulling you that direction?"

*Turn 2 (max 3, selected by gap detection):*
4. "What are your constraints — budget, timeline, team, existing commitments?"
5. "What would change your mind about your current leaning?" — only if leaning was stated
6. "How reversible is this? Can you change course later, or is this a one-way door?"
7. "Who else is affected by this decision?"

*Turn 3 (if needed, max 2):*
8. "What have you already researched or tried?"
9. "Is there a specific comparison you want — X vs. Y — or should I help you identify the
   options?"

### Branching Logic

- User states a strong leaning → MUST ask disconfirmation question ("What would change your
  mind?"). Without this, the prompt will anchor on the leaning and produce confirmation bias.
- User says "I don't even know my options" → shift to exploration first, then comparison.
  Prompt should identify options before evaluating them.
- User specifies a direct comparison ("X vs Y") → skip option identification, go straight
  to evaluation criteria.
- High stakes + low reversibility → deeper interview, include risk analysis in the prompt.

### Stopping Rule

Stop when you can fill:
1. The specific decision (not topic — the actual choice)
2. Top 2–3 evaluation criteria
3. Current leaning (including "none") for debiasing

---

## General Branching Heuristics (All Use Cases)

These four heuristics govern branching decisions across all use cases:

1. **Information gain:** Ask the question that most reduces uncertainty about the user's
   intent. The first 3–4 questions capture ~90% of actionable information.
2. **Confidence threshold:** When you're ~85% confident about a slot's value from context,
   skip the question.
3. **P×C rule:** Focus questioning where (probability you're wrong) × (cost of being wrong)
   is highest. A wrong expertise classification is very costly; a wrong tone preference is
   cheap.
4. **Scaffolded branching:**
   - Clear response → mark slot filled, move to next
   - Vague response → ladder down ("What specifically do you mean by...?")
   - Contradictory response → probe the contradiction ("You mentioned X but also Y —
     help me understand how those fit together")
   - New use case emerges → re-classify and switch protocols

---

## Transition to Output

Always make the transition from interview to output explicit. Use natural phrasing:

- "Got it — here's a prompt built for your situation:"
- "I have enough context. Here's what I'd recommend:"
- "Great — let me put this together for you:"

Never transition silently. The user should know the interview is over and the output is
coming.
