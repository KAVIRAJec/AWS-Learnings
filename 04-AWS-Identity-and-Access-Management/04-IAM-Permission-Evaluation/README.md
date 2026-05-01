#### IAM Permission Evaluation Logic

Every AWS request passes through these checks **in order**. The first DENY wins. An ALLOW is only granted if all applicable layers permit it.

```
Incoming Request
│
├─► 1. Explicit Deny in ANY policy?
│         │
│         ├─ YES ──────────────────────────────────────► ❌ DENY (final)
│         └─ NO  → continue
│
├─► 2. AWS Organizations SCP  (only if account is in an org)
│         │
│         ├─ Action NOT allowed by SCP ───────────────► ❌ DENY (final)
│         └─ Allowed by SCP → continue
│
├─► 3. Resource-Based Policy on the target resource?
│         │
│         ├─ Explicit ALLOW + same account ──────────► ✅ ALLOW (final)
│         ├─ Explicit ALLOW + cross-account → continue  (identity check still needed)
│         └─ No policy / no allow → continue
│
├─► 4. IAM Permission Boundary  (only if attached to the user/role)
│         │
│         ├─ Action NOT allowed by boundary ─────────► ❌ DENY (final)
│         └─ Allowed by boundary → continue
│
├─► 5. Session Policy  (only if request is via an assumed-role session)
│         │
│         ├─ Action NOT allowed by session policy ───► ❌ DENY (final)
│         └─ Allowed by session policy → continue
│
└─► 6. Identity-Based Policy  (IAM policies on the user/role)
          │
          ├─ Explicit ALLOW ───────────────────────────► ✅ ALLOW (final)
          └─ No ALLOW found ───────────────────────────► ❌ DENY (implicit deny)
```

**Rules to remember:**
- **Explicit Deny always wins** — checked first across every policy type, cannot be overridden.
- **Implicit Deny is the default** — no allow found at the end means denied.
- **SCPs & Permission Boundaries never grant** — they only restrict what identity policies can allow.
- **Resource-based policy alone is enough (same account)** — no identity policy needed if the resource policy explicitly allows it.
- **Cross-account needs both sides** — resource-based policy (or role trust policy) must allow the foreign principal AND the caller's identity policy must allow the action.
- **Effective permissions = intersection of all active layers** — every applicable layer must allow the action.
