# Flow Node Types

Every node has a name, type, typed inputs (each with an expression), typed outputs, and node-specific configuration. Node types fall into logic nodes (control the path) and data nodes (do work).

---

## Logic Nodes

| Node | Input name → type | Output name → type | Rules |
|---|---|---|---|
| **Input** | none | `document` → Any | Exactly **one**; flow must start here; takes `content` from `InvokeFlow` |
| **Output** | `document` → Any | none | Several allowed (one per branch); returned in `flowOutputEvent.content` |
| **Condition** | Any, Any (multiple inputs) | one path per condition | Evaluated **in order**; earlier match wins; needs a `default` condition connection |
| **Iterator** | `array` → Array | `arrayItem` → Any, `arraySize` → Number | Items processed **one by one, not in parallel** |
| **Collector** | `arrayItem` → Any, `arraySize` → Number | `collectedArray` → Array | Gathers iterated results downstream of an Iterator |
| **DoWhile loop** | `loopInput` → Any | `loopOutput` → Any, `iterationCount` → Number | Runs body **at least once**, then checks `condition`; `maxIterations` default **10** (positive number) |

### Condition operators

| Type | Operators | Data types |
|---|---|---|
| Relational | `==` `!=` | String, Number, Boolean (type must match for `==`) |
| Relational | `>` `>=` `<` `<=` | Number |
| Logical | `and` `or` `not` | combine expressions; use parentheses |

Compare an input to another input or a constant, e.g. `profit > expenses`, `profit <= 1000`.

---

## Data Nodes

| Node | Inputs | Output | Key config | Gotcha |
|---|---|---|---|---|
| **Prompt** | one input per `{{variable}}` | `modelCompletion` → String | `promptArn` (Prompt Management) **or** inline prompt; optional `guardrailConfiguration` | Inline prompt = no reuse/versioning |
| **Agent** | `agentInputText`, `promptAttributes`, `sessionAttributes` | `agentResponse` → String | `agentAliasArn` | Needs **alias** ARN; supports multi-turn |
| **Knowledge Base** | `retrievalQuery` → String | `retrievalResults` (Array) **or** `outputText` (String) | `knowledgeBaseId`; optional `modelId`, `guardrailConfiguration` | No `modelId` = raw retrieved chunks; with `modelId` = generated answer; guardrail only in generate mode |
| **Lambda** | `codeHookInput` (multiple allowed) | `functionResponse` → Any | `lambdaArn` | Event has `messageVersion`, `flow`, `node.inputs[].value` (already resolved by expression) |
| **Inline code** | up to **5** named inputs | `response` → Any | Python 3.12 (`Python_3`) | Preview; see limits below |
| **S3 storage** | `content`, `objectKey` | `s3Uri` → String | `bucketName` | Writes to S3 |
| **S3 retrieval** | `objectKey` → String | `s3Content` | `bucketName` | Content must be **UTF-8 string** |
| **Lex** | `inputText`, `requestAttributes`, `sessionAttributes` | `predictedIntent` → String | `botAliasArn`, `localeId` | **No multi-turn**; one utterance per node |

### Inline code node limits

| Limit | Value |
|---|---|
| Status | **Preview** |
| Language | Python 3.12 (`Python_3`) only |
| Inputs per node | Up to 5 |
| Nodes per flow | Up to 5 |
| Running nodes per account | Up to 25 |
| Code size | ≤ 5 MB |
| Output | Result of the **last executed line**; `print` output not captured |
| Environment | Isolated, AWS-managed, **no internet access** |
| Async flow executions | **Not supported** |

> [!WARNING]
> Wrong input/output types on an inline code node cause runtime errors. Match declared types to what the code returns.

---

## Lambda Node Event

```
{
  "messageVersion": "1.0",
  "flow": { "flowArn": "...", "flowAliasArn": "..." },
  "node": { "name": "...",
            "inputs": [ { "name": "...", "type": "...",
                          "expression": "$.data[1]", "value": 2 } ] }
}
```

- `value` is the whole input after the expression ran (whole input `[1,2,3]` + `$.data[1]` → `2`).
- Function return becomes the node output `functionResponse`.

---

## Need → Pick

| Need | Node |
|---|---|
| Route by a number/string result | Condition |
| Process each item of a list | Iterator → work nodes → Collector |
| Repeat until quality threshold | DoWhile (`maxIterations` to avoid infinite loops) |
| Call an enterprise API | Lambda |
| Small data transform, no external deps | Inline code |
| Save the result | S3 storage |
| Feed a stored document in | S3 retrieval |
| Ground an answer in documents | Knowledge Base (with `modelId`) |
| Clarify with the user | Agent (user input enabled) |
| Intent classification | Lex |

**Lambda node vs Inline code node:**

| | Lambda | Inline code |
|---|---|---|
| Language/deps | Any runtime, libraries | Python 3.12 only, no internet |
| Where it runs | Your Lambda function | AWS-managed isolated environment |
| Status | Not marked preview in docs | **Preview** |
| Async executions | Not listed as restricted | **Not supported** |
| Pick when | Real integrations, VPC, libraries | Light transform inside the flow |
