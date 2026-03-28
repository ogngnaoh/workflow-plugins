# Prompt Construction Templates

Complete templates for generating prompts, organized by use case and expertise level.
Read this file when generating prompts in Step 4 of the main skill.

---

## Universal Prompt Skeleton

Every generated prompt follows this structure. Sections may be shortened or omitted based
on complexity tier, but the ORDER is fixed (Claude performs best with context first, task
second, constraints third — Anthropic documentation confirms up to 30% quality improvement
with this ordering).

```
[ROLE/CONTEXT]    — Who Claude should be and why. 1–2 sentences.
[TASK]            — What to do, grounded in user's specific situation. 2–4 sentences.
[CONSTRAINTS]     — Format, length, depth, what to avoid. 1–3 sentences.
[QUALITY BAR]     — What "excellent" looks like for THIS request. 1–2 sentences.
[ANTI-FLUENCY]*   — Generation/test/reflection measure. 1 sentence. (*Learning only)
```

**Length target:** 100–300 words total. Under 100 feels underspecified. Over 300 hits
diminishing returns and increases brittleness. If a prompt genuinely needs more, split into
two sequential prompts.

**Formatting rule:** Use plain prose, not bullet lists or XML in the generated prompt.
Claude 4.x responds well to natural instruction language. Only use XML tags if the prompt
involves structured data input.

---

## Use Case 1: Learn a Skill

### Novice Template

```
You are a patient, encouraging tutor helping me learn [SKILL] from scratch.
I have [STATED_EXPERIENCE]. My goal is to [TARGET_COMPETENCY].

Start with the most fundamental concept I need to grasp and build from there.
Use concrete, everyday examples before introducing any technical terminology.
Break the learning into clear steps, and after each step, give me a small exercise
to practice — don't just explain, make me DO something.
[CONTEXT_CONSTRAINT if any, e.g., "I'll be applying this at work in a team setting."]

Keep explanations short and concrete. If I get something wrong, help me understand
WHY it's wrong rather than just correcting me.
After each major concept, ask me to explain it back in my own words before moving on.
```

### Intermediate Template

```
You are a skilled mentor helping me deepen my [SKILL] abilities.
I already know [STATED_KNOWLEDGE] but want to get to [TARGET_COMPETENCY].

Focus on the gaps between where I am and where I want to be. Don't re-explain
basics — connect new material to what I already understand. Challenge me with
scenarios that are slightly above my current level.
[CONTEXT_CONSTRAINT if any.]

When introducing a new technique or concept, show me how it contrasts with what
I already know. Include practice scenarios that test whether I can apply this in
unfamiliar situations, not just repeat what you've taught.
At key points, ask me what I think the right approach would be BEFORE showing me.
```

### Expert Template

```
You are a peer-level practitioner of [SKILL] helping me sharpen my edge.
I have [STATED_EXPERIENCE] and want to [TARGET_COMPETENCY].

Skip fundamentals entirely. Focus on: edge cases where conventional wisdom breaks
down, techniques that separate good from exceptional practitioners, and areas where
my current approach might have blind spots.
[CONTEXT_CONSTRAINT if any.]

Challenge my assumptions. If I describe my approach, tell me where a world-class
practitioner would do something different and why. Don't make this easy — I learn
best when pushed. Ask me to solve a hard scenario before offering your perspective.
```

---

## Use Case 2: Understand a Concept

### Novice Template

```
You are an expert teacher who makes complex ideas accessible.
Explain [CONCEPT] to me. I have [STATED_BACKGROUND] and need to understand this
[DEPTH: enough to explain it / enough to use it / deeply].

Start with a concrete analogy from everyday life, then build toward the real
explanation. Avoid jargon until I have the intuition; then introduce proper terms
attached to the intuition I've already built.
[PURPOSE if stated, e.g., "I need this for a presentation to non-technical stakeholders."]

After your explanation, give me 2–3 questions to test whether I actually
understand this or just think I do.
```

### Intermediate Template

```
Explain [CONCEPT] at an intermediate level. I already understand [RELATED_KNOWLEDGE]
and want to understand [CONCEPT] [DEPTH: in depth / well enough to apply / at a
technical level].

Build on what I know — connect [CONCEPT] to [RELATED_KNOWLEDGE] explicitly. Don't
start from scratch. Focus on the parts that are genuinely non-obvious or
counterintuitive even for someone with my background.
[PURPOSE if stated.]

Highlight where my existing mental model might be wrong or incomplete. After
explaining, ask me to apply this concept to a scenario I haven't seen before.
```

### Expert Template

```
I have a strong background in [DOMAIN] and want to understand [CONCEPT] at an
expert level — specifically [SPECIFIC_ASPECT if stated].

Skip the standard explanation. Focus on: boundary conditions where the concept
breaks down, ongoing debates among experts, subtle distinctions that most
explanations gloss over, and the most common expert-level misconceptions.
[PURPOSE if stated.]

Where relevant, point out what the standard textbook version gets wrong or
oversimplifies. Challenge me — ask where I think this concept applies and where
it doesn't before giving your analysis.
```

---

## Use Case 3: Execute a Task

### Novice Template

```
You are an experienced [ROLE relevant to task]. Help me [TASK].
Here's my situation: [CONTEXT from interview].

Walk me through this step by step. For each step, explain what I'm doing and why.
Include an example or template I can follow.
[CONSTRAINTS: format, length, tone, audience, things to avoid.]

The output should [QUALITY_BAR]. [SPECIFIC_REQUIREMENTS if any.]
```

### Intermediate Template

```
You are a [ROLE]. Help me [TASK].
Context: [CONTEXT]. Audience: [AUDIENCE].

I know the basics — don't over-explain. Focus on getting the details right and
catching things I might miss. [CONSTRAINTS.]
[SPECIFIC_REQUIREMENTS if any.]

Aim for [QUALITY_BAR]. Flag anything that seems like it could go wrong or that
I should double-check.
```

### Expert Template

```
[TASK]. Context: [CONTEXT]. Constraints: [CONSTRAINTS].

[SPECIFIC_REQUIREMENTS if any.] Aim for [QUALITY_BAR].

Don't scaffold or over-explain — just produce excellent work. If you see an
opportunity to do something I didn't ask for that would make this significantly
better, do it and note why.
```

---

## Use Case 4: Research for a Decision

### Standard Template (Expertise Matters Less Here)

```
Help me make a decision: [SPECIFIC_DECISION].

My situation: [CONTEXT_AND_CONSTRAINTS from interview].
What matters most to me: [TOP_CRITERIA, ranked].
[CURRENT_LEANING if stated: "My current leaning is toward [X] because [reason]."]

Analyze this systematically. For each viable option, evaluate against my stated
criteria. Be concrete — don't just say "it depends," tell me what it depends ON
and how each factor tips the balance for my specific situation.

[DEBIASING MEASURE — include ONE of the following based on interview:]
- "Steelman the option I'm NOT leaning toward. What's the strongest case for it?"
- "What am I most likely to be wrong about in my current thinking?"
- "What would someone who chose differently than my leaning say about why?"

End with a clear recommendation grounded in my criteria, not a wishy-washy
"it depends on your priorities" — you KNOW my priorities.
```

### High-Stakes Variant (Add When Reversibility Is Low)

Add to the standard template:
```
This is a high-stakes, hard-to-reverse decision. Include: the single biggest risk
of each option, what I'd need to validate before committing, and what a "minimum
viable test" would look like before going all-in.
```

---

## Anti-Fluency-Illusion Measures

For Learn and Understand use cases, select ONE measure and append it to the prompt.
Choose based on the user's stated success criteria:

| User's Goal | Best Measure | Prompt Addition |
|---|---|---|
| "I need to explain this to others" | Teach-back | "After explaining, ask me to teach this concept back to you as if I'm explaining it to a complete beginner." |
| "I need to apply this" | Novel application | "After explaining, give me a scenario I haven't seen and ask me to apply this concept to it." |
| "I want deep understanding" | Self-test | "After explaining, give me 2–3 questions that test genuine understanding — not recall, but whether I can reason with this concept." |
| "I'm not sure what I need" (default) | Reflection boundary | "Before we finish, ask me: what did you learn, what's still fuzzy, and what would you struggle to explain?" |
| "I want to get better at this" | Generation | "At key points, ask me what I think the answer or approach is BEFORE you show me. Make me generate, not just consume." |

---

## Domain Adaptation Notes

The templates above are domain-general structures. When filling them in, adapt content
based on what the user revealed during the interview:

- **Use the user's vocabulary.** If they say "customers," don't write "stakeholders." If
  they say "UX," don't write "user experience design."
- **Reference their stated context.** "In your role as [their role]" rather than generic
  framing.
- **Anchor to their stated knowledge.** "Building on your experience with [thing they
  mentioned]" makes the prompt feel personalized.
- **Address stated misconceptions.** If the user expressed a misconception during the
  interview, include a targeted instruction: "Pay special attention to [topic of
  misconception] — make sure the explanation addresses why [common wrong belief] is wrong."

The skill cannot anticipate domain-specific threshold concepts. If the user mentions key
concepts in their domain, build around them. If not, the prompt should instruct Claude to
identify and explain the most important threshold concepts in the domain as part of its
response.

---

## Prompt Length Calibration

| Complexity Tier | Target Length | Rationale |
|---|---|---|
| Clear | 50–100 words | Sufficient constraints already present |
| Complicated | 100–200 words | Need role, task, and key constraints |
| Complex | 200–300 words | Need full skeleton + anti-fluency + debiasing |

If a prompt exceeds 300 words, split it:
- Prompt 1: Setup and core task
- Prompt 2: "Now that you've done [X], let's go deeper on [Y]"

Tell the user: "This is a two-part approach — run the first prompt, then follow up with
the second."
