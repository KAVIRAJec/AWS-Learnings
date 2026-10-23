# Choosing How to Adapt a Foundation Model

A foundation model that is good at general tasks often falls short on *your* tasks: your jargon, your output format, your policies, your data. There is a ladder of ways to close that gap, from cheap and reversible to expensive and permanent. The skill is picking the lowest rung that actually solves the problem.

The deciding question is **what is missing**: *knowledge* the model doesn't have, or *behavior* it hasn't learned.

---

## The ladder

**1. Prompting.** Give clear instructions, a role, a format, and a few examples in the prompt. Costs nothing to try and changes instantly. Limited by the context window and by how consistently the model follows instructions.

**2. Retrieval-augmented generation (RAG).** Fetch relevant documents at question time and put them in the prompt. This supplies **knowledge**: current facts, private documents, things too big or too fast-changing to train in. It does not teach the model new habits. A model that doesn't know your formatting convention still won't follow it just because documents are in the prompt.

**3. Fine-tuning.** Train the model further on examples of the behavior you want (input → ideal output), which **changes its weights**. This teaches **behavior and patterns**: a domain's writing style, a rigid output format, specialised terminology used correctly, consistent handling of niche scenarios. It needs good labeled examples and an evaluation to prove it helped.

**4. Parameter-efficient fine-tuning (PEFT, e.g. LoRA).** Fine-tuning where the base model is **frozen** and only a small set of extra weights (an *adapter*) is trained. Same goal as fine-tuning at a fraction of the compute, and the result is a small separate artifact instead of a whole new model. Detail: [LoRA adapter lifecycle](../04-SageMaker-for-GenAI/01-LoRA-Adapter-Lifecycle.md).

**5. Continued pre-training.** Keep training on a very large amount of **unlabeled** text so the model absorbs a new language or domain distribution. Heavy compute and long runs; for a broad shift in what the model knows, not for targeted behavior tweaks.

**6. Distillation.** Use a large "teacher" model's outputs to fine-tune a smaller, faster, cheaper "student" so it approaches the teacher's quality on your task. Chosen for **cost and latency**, not for adding new knowledge.

Training from scratch is almost never justified.

| Approach | Changes the model? | What it fixes | Data needed | Cost and speed | Updating |
|---|---|---|---|---|---|
| Prompting | No | Instructions, format, light style | A few examples | Free, instant | Edit the prompt |
| RAG | No | Missing or changing knowledge | Documents | Low; index and retrieval cost | Re-sync the index |
| Full fine-tuning | Yes, all weights | Learned behavior, domain patterns | Labeled examples | High; new full model each time | Retrain and redeploy a whole model |
| LoRA / PEFT | Yes, small adapter | Same as fine-tuning | Labeled examples | Low; trains a few weights | Swap a small adapter |
| Continued pre-training | Yes, all weights | Broad domain/language shift | Very large unlabeled corpus | Very high; slow | Rare, major releases |
| Distillation | Trains a smaller model | Cost and latency at similar quality | Prompts (teacher generates answers) | Medium | New student per teacher/task |

---

## Reading a scenario

Look for the words that say what is wrong:
- "needs current / private facts", "docs change daily" → **RAG**.
- "inconsistent output for niche scenarios", "domain-specific language and format", "learned patterns rather than context injection" → **fine-tuning**.
- Add "update frequently, roll back safely, multiple variants, minimal compute" → **LoRA adapters**, because the adapter is small, versioned independently, and swappable while the base model stays untouched.
- "unlabeled domain corpus", "new language or vocabulary at scale" → **continued pre-training**.
- "reduce cost and latency while keeping quality" → **distillation** or a smaller model.

These can combine: a fine-tuned model that also uses RAG for facts is common, because they fix different things.

> [!WARNING]
> Fine-tuning does not reliably teach facts and can drift on things it wasn't trained on. If the problem is "it doesn't know X", use RAG; if it is "it doesn't act the way we need", tune.

---

## Where this lives on AWS

- **Amazon Bedrock**: supervised fine-tuning, reinforcement fine-tuning, and distillation for supported models, plus importing models trained elsewhere. See [Bedrock Model Customization](../02-Amazon-Bedrock/07-Model-Customization/01-Bedrock-Model-Customization.md).
- **Amazon SageMaker AI**: full control, including LoRA/adapters, versioning in Model Registry, and hosting many adapters on one endpoint. See [LoRA Adapter Lifecycle](../04-SageMaker-for-GenAI/01-LoRA-Adapter-Lifecycle.md).
- **RAG**: [Knowledge Bases](../02-Amazon-Bedrock/02-Knowledge-Bases/README.md).
