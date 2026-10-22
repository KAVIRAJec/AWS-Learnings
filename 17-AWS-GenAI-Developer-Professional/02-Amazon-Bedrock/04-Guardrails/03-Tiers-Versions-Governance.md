# Guardrails: Tiers, Versions, and Governance

How a guardrail's detection quality is chosen, how changes are released safely, and how an organization makes sure every model call actually uses one.

---

## Safeguard tiers

Some policies (content filters and prompt attacks, denied topics, contextual grounding) come in two tiers that differ in detection quality, language coverage, and requirements. Choosing a tier lets you adopt improvements on your own schedule.

| | Classic | Standard |
|---|---|---|
| Quality | Established behavior | More robust, more consistent |
| Languages | English, French, Spanish | Many more languages |
| Code content | Not covered | Content filters, prompt attacks, and denied topics also catch harmful content inside code (comments, names, strings) |
| Prompt leakage detection | No | Yes |
| Denied-topic definition length | 200 characters | 1,000 characters |
| Grounding | Strict (only what the source states) | Handles reasoning and conversational answers |
| Cross-Region inference | Not supported | **Required** |

The default is **Classic** if you don't set `tierConfig`. Standard requires a guardrail profile for cross-Region inference (`crossRegionConfig.guardrailProfileIdentifier`), which routes guardrail evaluation across Regions in a geography. Standard grounding is available in fewer Regions than other Standard policies; elsewhere the request fails with a validation error.

Pick **Standard** when you serve many languages, handle code, need higher accuracy, or Classic grounding blocks valid summaries and conversational answers. Stay on **Classic** for English/French/Spanish workloads, or while you re-test thresholds you tuned on Classic.

> [!WARNING]
> `UpdateGuardrail` replaces the whole policy configuration. If you update a guardrail that uses Standard grounding and omit `tierConfig`, it silently resets to Classic. Thresholds tuned for one tier give different results on the other, so re-test before switching.

---

## Versions

A guardrail has a working copy and immutable releases.

- **DRAFT**: the mutable working version. Edit and test here.
- **Numbered version** (1, 2, …): created from the draft with `CreateGuardrailVersion`; cannot be changed afterwards.

Production should reference a **numbered version**, so editing the draft never changes live behavior. To release a change: edit DRAFT, test (ideally in detect mode), create a new version, then point the application at it. To roll back, point back to the older version.

---

## Making guardrails mandatory

Teams can forget the `guardrailConfig` or point at a weaker guardrail. Enforce it with IAM instead of trusting each application.

- The condition key **`bedrock:GuardrailIdentifier`** applies to `InvokeModel`, `InvokeModelWithResponseStream`, `Converse`, and `ConverseStream`.
- An explicit **Deny** on those actions when the request's guardrail does not match the approved one makes any other guardrail (or version) fail with access denied, whatever other permissions the caller has.
- A condition checking that the key is **null** denies calls made with no guardrail at all.
- An account-level enforced guardrail configuration also exists (`PutEnforcedGuardrailConfiguration`), for applying a guardrail without relying on per-request settings.

This is the answer to "apply controls automatically and centrally": policy changes happen in the guardrail, and IAM guarantees nobody bypasses it.

---

## Permissions

| Who needs it | Actions |
|---|---|
| Admins who manage guardrails | `CreateGuardrail`, `CreateGuardrailVersion`, `UpdateGuardrail`, `GetGuardrail`, `ListGuardrails`, `DeleteGuardrail` (plus `bedrock:TagResource` if passing tags) |
| Apps calling models with a guardrail | `InvokeModel` / `InvokeModelWithResponseStream` on the model, plus `bedrock:ApplyGuardrail` on the guardrail ARN |
| Apps using only `ApplyGuardrail` (e.g. third-party models) | Just `bedrock:ApplyGuardrail` |

Guardrails are encrypted with an AWS managed key by default; you can supply a customer managed KMS key at creation.

---

## Operational notes

- **Roll out in detect mode first**: set actions to `NONE`, read the trace, tune strengths and thresholds, then switch to block.
- **Blocked content appears in plain text** in Bedrock model invocation logs if logging is on. Disable logging or protect the logs if that is a concern.
- **Cost tracking**: responses report usage units per policy.
- Guardrails work with supported Bedrock models (check the model's card); `ApplyGuardrail` extends them to models outside Bedrock.
