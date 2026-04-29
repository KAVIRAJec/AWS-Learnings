## Amazon CodeGuru

Amazon CodeGuru is a **machine learning-powered developer tool** that automates code reviews and identifies application performance bottlenecks — trained on millions of code reviews from Amazon's own engineering teams and open-source projects.

**CodeGuru Reviewer:**
- Analyzes source code in PRs and repositories — finds issues before code ships.
- **Detects**: Security vulnerabilities (OWASP Top 10, hardcoded secrets, crypto misuse), resource leaks, concurrency issues, input validation problems, AWS SDK best practice violations.
- **Supported languages**: Java and Python.
- Integrates with **GitHub, GitHub Enterprise, Bitbucket, GitLab, AWS CodeCommit** — automatically reviews every PR, posts comments on the exact line with the issue and recommendation.
- Powered by ML + automated reasoning (formal verification) — context-aware, not just pattern matching.

**CodeGuru Profiler:**
- Profiles your **live application's CPU and heap usage in production** — pinpoints the exact methods consuming the most resources.
- **Flame Graphs**: Visualize the full call stack and time distribution per request.
- **Anomaly Detection**: Flags new performance regressions by comparing against a historical baseline automatically.
- **Low overhead**: ~5% CPU — safe to run continuously in production.
- Supported runtimes: **Java (JVM) and Python** — works on EC2, Lambda, ECS, EKS, Fargate, and on-premises.
- How to enable: add the Profiler agent as a dependency + a few lines of init code → view flame graphs and recommendations in the console.

**Use cases:** Automated security review in CI/CD pipelines, finding vulnerabilities before deployment, identifying production performance bottlenecks, reducing cloud cost by fixing inefficient code.
