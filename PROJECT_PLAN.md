# Project 1 game plan: metric dimension of directed circulant graphs

This guide covers the general project instructions and **Project 1: exact values of the metric dimension of** $C_n(1,2,\ldots,n-d)$.

The public sources located for this project, their extracted results, and their notation differences are documented in [`REFERENCES.md`](REFERENCES.md). Legacy values extracted from reference [20] are documented under [`reference_data/`](reference_data/README.md).

## 1. What the assignment expects

The project is not only a literature review. It must contain:

- a clear explanation of the problem;
- a program that computes an **exact** metric dimension and an optimal resolving set;
- computational experiments on many pairs \((n,d)\);
- tests of Conjecture 1, including its claimed equality cases;
- attempts to identify exact formulas for fixed \(d\);
- a reproducible description of the program, data generation, and experiments;
- a report of roughly five pages, with tables, graphs, or other useful summaries;
- commented code and instructions for running it;
- a defense at which every group member can explain the mathematics, code, experimental choices, and conclusions.

### Official deadlines

| Deliverable | First term | 
|---|---:|
| Short problem description and work plan, at most 2 pages | 13 November 2026 | 
| Report and program | 11 December 2026 | 
| Defense | 14–18 December 2026 | 

Public copies of references [20]–[22] have now been located and summarized in `REFERENCES.md`. Ask the instructors for the following material immediately:

- the separate document **Computational tools for the projects**;
- paper [19], which is listed as submitted and was not found publicly;
- Popovič's original machine-readable `n<=40` table or source repository, if one exists beyond the public article.

The old ILP can save time, but the group must still understand, test, document, and be able to defend every part of the code it uses.

## 2. Essential notation and ideas

### 2.1 Directed graphs and distance

A directed graph is written as \(G=(V,A)\), where:

- \(V\) is the set of vertices;
- \(A\) is the set of directed edges, usually called **arcs**.

An arc \(uv\) allows movement from \(u\) to \(v\); it does not automatically allow movement from \(v\) to \(u\).

The directed distance

$d_G(u,v)$

is the length (number of arcs) of a shortest directed path **from \(u\) to \(v\)**. Direction matters, so in general \(d_G(u,v)\neq d_G(v,u)\). The subscript \(G\) is often omitted when the graph is clear.

A directed graph is **strongly connected** if every vertex can reach every other vertex by a directed path. All directed distances are then finite.

### 2.2 Resolving sets and metric dimension

Let

$
W=(w_1,w_2,\ldots,w_z)
$

be an ordered list of selected vertices, called landmarks. The **distance representation** or **signature** of a vertex \(v\) with respect to \(W\) is

$
r(W\mid v)=\bigl(d(w_1,v),d(w_2,v),\ldots,d(w_z,v)\bigr).
$

The distances go **from each landmark to \(v\)**. Reversing them is a common implementation error in directed graphs.

The set \(W\) is a **resolving set** if every two different vertices receive different signatures:

$
u\neq v \quad\Longrightarrow\quad r(W\mid u)\neq r(W\mid v).
$

Equivalently, for every pair \(u\neq v\), at least one selected landmark \(w\in W\) distinguishes them:

$
d(w,u)\neq d(w,v).
$

The **metric dimension** \(\dim(G)\) is the minimum possible size of a resolving set. Therefore an exact computational answer needs two things:

1. a resolving set of the reported size, which is an upper-bound certificate;
2. proof from the exact solver that no smaller resolving set exists.

### 2.3 Directed circulant graphs

For integers

$
1\leq a_1<a_2<\cdots<a_k\leq n-1,
$

the directed circulant graph \(C_n(a_1,\ldots,a_k)\) has vertices

$
v_0,v_1,\ldots,v_{n-1}.
$

For every vertex \(v_i\) and every generator \(a_j\), it has the arc

$
v_i\longrightarrow v_{(i+a_j)\bmod n}.
$

Thus all vertex indices are calculated modulo \(n\). The graph is strongly connected exactly when

$
\gcd(n,a_1,\ldots,a_k)=1.
$

In this project the generators are \(1,2,\ldots,n-d\). Since generator \(1\) is present, the graph is automatically strongly connected. The assignment calls \(d\) the missing-generator parameter. More literally, among the nonzero jumps \(1,\ldots,n-1\), the excluded jumps are

$
n-d+1,n-d+2,\ldots,n-1.
$

Be careful when reading reports [20] and [21]: in those reports, \(C(n,d)\) means \(C_n(1,\ldots,d)\), so their \(d\) is the number of included generators. That is not the convention used in this project statement.

### 2.4 Why the relevant distances are only 0, 1, and 2

The project studies \(n>2d\), equivalently \(d<n/2\). Put

$
\delta=(j-i)\bmod n.
$

Then in \(C_n(1,\ldots,n-d)\),

$
d(v_i,v_j)=
\begin{cases}
0, & i=j,\\
1, & \delta\in\{1,2,\ldots,n-d\},\\
2, & \delta\in\{n-d+1,\ldots,n-1\}.
\end{cases}
$

This is one of the most important simplifications in the project. There is no need to run a shortest-path algorithm for every instance in the main parameter range. A distance can be computed directly from a modular difference.

It also turns the problem into a combinatorial separation problem: choose as few landmarks as possible so that every pair of vertices differs in at least one coordinate of its \(0/1/2\) signature.

### 2.5 A small example

Consider \(C_9(1,2,3,4,5)\), so \(n=9\) and \(d=4\). From \(v_0\), vertices \(v_1,\ldots,v_5\) are at distance 1 and \(v_6,v_7,v_8\) are at distance 2.

One optimal resolving set is

$
W=(v_0,v_1,v_2,v_4).
$

The signatures of \(v_0,\ldots,v_8\) are, respectively,

```text
(0,2,2,1)  (1,0,2,2)  (1,1,0,2)
(1,1,1,2)  (1,1,1,0)  (1,1,1,1)
(2,1,1,1)  (2,2,1,1)  (2,2,2,1)
```

They are all different, so \(W\) resolves the graph. An exhaustive check shows that no set of size 3 resolves it, hence the metric dimension is 4. This is a useful first unit test because the example is small enough to inspect by hand.

### 2.6 The exact ILP as a hitting-set problem

Introduce one binary variable for each possible landmark:

$
x_w=
\begin{cases}
1,&w\in W,\\
0,&w\notin W.
\end{cases}
$

For each unordered pair of distinct vertices \(\{u,v\}\), precompute the set of landmarks that distinguish the pair:

$
D_{u,v}=\{w\in V:d(w,u)\neq d(w,v)\}.
$

At least one of those landmarks must be selected. The ILP is therefore

$
\begin{aligned}
\text{minimize}\quad & \sum_{w\in V}x_w,\\
\text{subject to}\quad & \sum_{w\in D_{u,v}}x_w\geq 1
&&\text{for every }u<v,\\
&x_w\in\{0,1\}&&\text{for every }w\in V.
\end{aligned}
$

This has \(n\) binary variables and \(\binom n2\) pair-separation constraints. After solving, the program must independently recompute all signatures and verify that the returned \(W\) really resolves the graph.

The graph is invariant under rotation of all indices. Any nonempty resolving set can be rotated so that it contains \(v_0\). Adding

$
x_0=1
$

therefore breaks rotational symmetry without changing the optimum. The assignment specifically recommends this when the solver becomes slow. Keep a switch that enables or disables the constraint so its correctness and performance can be tested.

For very small instances, also implement an independent exhaustive solver that tries subsets in increasing order of size. It will be slower, but it is invaluable for checking the ILP rather than merely trusting it.

### 2.7 Known values to use as tests

The assignment gives the following exact results:

$
\dim\bigl(C_n(1,\ldots,n-4)\bigr)=\left\lceil\frac{2n}{5}\right\rceil
\qquad(n\geq 10),
$

and

$
\dim\bigl(C_n(1,\ldots,n-5)\bigr)=
\begin{cases}
\lfloor n/3\rfloor,&3\mid n,\\
\lfloor n/3\rfloor+2,&3\nmid n,
\end{cases}
\qquad(n\geq 11).
$

Use these formulas as automated regression tests over many values of \(n\), not just one or two examples.

Reference [20]'s public workbook supplies 462 additional comparison rows for `4<=d<=10`, `n>2d`, and `n<=80`. They have been extracted to `reference_data/reference20_metric_dimensions.csv`. They agree with both known formulas and show no counterexample to Conjecture 1, but they lack saved witnesses and solver metadata. Treat them as regression inputs to reproduce, not as a substitute for your own exact runs.

### 2.8 Conjecture 1

For \(d\geq4\) and \(n>2d\), define the displayed lower bound

$
B(n,d)=
\begin{cases}
\displaystyle
\left\lfloor\frac{n}{d+1}\right\rfloor
\left\lceil\frac{d+1}{3}\right\rceil,
&d\not\equiv0\pmod 3,\$1.2ex]
\displaystyle
\frac{n}{2d-1}\cdot\frac{2d}{3},
&d\equiv0\pmod 3.
\end{cases}
$

The conjecture states that

$
\dim\bigl(C_n(1,\ldots,n-d)\bigr)\geq B(n,d).
$

Because the metric dimension is an integer, the operational integer lower bound is \(\lceil B(n,d)\rceil\). The conjecture further claims equality exactly in these cases:

- if \(d\not\equiv0\pmod3\), equality holds if and only if \((d+1)\mid n\);
- if \(d\equiv0\pmod3\), equality holds if and only if \((2d-1)\mid n\).

For every computed pair \((n,d)\), the analysis should record:

- the exact metric dimension;
- \(B(n,d)\) and \(\lceil B(n,d)\rceil\);
- whether the lower bound holds;
- whether equality was observed;
- whether the relevant divisibility condition predicts equality;
- the gap \(\dim-B\), kept exactly as a rational number where necessary.

Record equality both against the displayed rational bound and against its rounded-up integer version. This avoids silently choosing an interpretation when reporting the experiment.

### 2.9 What “guess exact formulas” means

For each fixed \(d=6,7,8,\ldots\), inspect the sequence as \(n\) grows. The assignment suggests organizing values by residue classes:

- use \(n\bmod(d+1)\) when \(d\not\equiv0\pmod3\);
- use \(n\bmod(2d-1)\) when \(d\equiv0\pmod3\).

Write \(n=qM+r\), where \(M\) is the relevant modulus. For each residue \(r\), look for a stable expression such as

$
\dim = aq+c_r
$

or an equivalent floor/ceiling formula. Report:

- the proposed formula;
- the smallest observed threshold \(N_d\) after which it always works in the computed range;
- every exception below that threshold;
- the largest tested \(n\);
- a clear label saying **conjectured from computation**, unless a proof is supplied.

## 3. Shared technical decisions

The instructors recommend SageMath because it provides graph and integer-programming support. A practical setup is SageMath/Python plus an ILP backend available to the group. If Sage's default backend is too slow, benchmark another exact solver. Solver changes must be recorded in the results.

Agree on one stable interface before working separately, for example:

```text
solve(n, d, symmetry_break=True)
    -> dimension, resolving_set, solver_status, runtime_seconds
```

Every result row should contain at least:

```text
n, d, dimension, resolving_set, runtime_seconds,
solver_name, solver_status, symmetry_break, code_version
```

Useful repository layout:

```text
src/          exact solver and verification code
tests/        small examples and known-formula tests
experiments/  scripts for parameter sweeps
results/      machine-readable CSV data
analysis/     tables, plots, and formula-search notebooks/scripts
report/       short description, final report, and presentation
```

Prefer scripts that regenerate tables and figures from raw CSV files. Do not manually copy numbers into the report without an automated or clearly documented check.

### Definition of a trustworthy result

A row may be called exact only if:

- the solver reports an optimal solution, not merely a feasible solution or a timeout;
- the saved resolving set has the reported size;
- the independent verifier confirms that all \(n\) signatures are distinct;
- \(n>2d\) and the instance parameters are saved correctly;
- enough metadata is stored to reproduce the run.

Timeouts and feasible-but-not-proven solutions should be kept in a separate table. They are useful performance data, but they are not exact metric dimensions.

## 4. Collaboration rules

- Use short-lived Git branches and pull requests. At least one other person reviews each substantial change.
- No one reviews only prose: everyone must review some code, and no one reviews only code.
- At each meeting, one person gives a 10-minute explanation of work they did, followed by questions from the other two.
- Merge only after the automated tests pass.
- Tag or record the exact commit used for every large experiment.
- Store raw output in a common format. Never maintain three incompatible spreadsheets.
- If one person's computer is much faster, redistribute runs by estimated runtime, but keep ownership of the analysis by congruence class as described below.
- Budget about 10–12 hours per person for meetings and integration, about 40–42 hours for the individual blocks explicitly listed below, and a 6–8 hour reserve for reading the requested references, failed or repeated runs, instructor feedback, and defense preparation. This brings the plan close to the assignment's expected 60 hours per person without pretending that experimental runtime is fully predictable.

## 5. Step-by-step work plan

### Group meeting 1 — kickoff and shared foundation (22–24 October, 90–120 minutes)

Everyone should read pages 1–3 of the assignment beforehand.

During the meeting:

1. Work through the \(C_9(1,\ldots,5)\) example together.
2. Check that everyone understands arc direction, modular indices, signatures, resolving sets, and metric dimension.
3. Derive the pair-separation ILP together on a whiteboard.
4. Agree on SageMath/Python versions, solver choices, function interface, CSV schema, repository layout, and Git workflow.
5. Review `REFERENCES.md`, then contact the instructors for the computational-tools document, paper [19], and any unpublished source data accompanying [21].
6. Create issues for all tasks below and assign owners.

The interface and data format agreed here let all three people work independently during the next block.

### Person 1 — theory notes and an independent exhaustive checker (about 6 hours)

- Turn Sections 2.1–2.5 above into concise notes suitable for the short description.
- Implement direct distance and signature functions.
- Implement a simple exhaustive solver for small \(n\) that tries subsets in increasing size.
- Independently verify the \((n,d)=(9,4)\) example.
- Cross-check the direct modular-distance formula against BFS on several small graphs.
- Write tests for the most likely mistakes: reversed distance direction, missing modulo reduction, duplicate signatures, and the \(i=j\) distance-zero case.

This work does not depend on the production ILP and gives the group an independent correctness oracle.

### Person 2 — production ILP solver (about 7 hours)

- Implement the pair-separation ILP.
- Return both the optimum value and the actual resolving set.
- Add optional symmetry breaking \(x_0=1\).
- Record solver status and runtime.
- Add a post-solve certificate check that recomputes all signatures.
- Provide a small command-line entry point or callable API matching the agreed interface.
- Document the mathematical meaning of every variable and constraint in comments.

This can be developed entirely from the agreed interface and small hand-worked examples.

### Person 3 — experiment and analysis framework (about 6 hours)

- Implement the standard CSV/result schema and safe incremental saving, so a long run can resume after interruption.
- Write a parameter-sweep driver that accepts ranges of \(d\) and \(n\).
- Implement the formulas for the known \(d=4,5\) values and for \(B(n,d)\).
- Create automatic columns for divisibility, equality prediction, bound gap, and runtime.
- Prepare scripts that turn sample or placeholder CSV rows into a table and a runtime plot.
- Implement a second lightweight resolving-set verifier if feasible.

This framework can be built against a temporary mock `solve` function, so Person 3 does not need to wait for the ILP.

### Group meeting 2 — cross-validation and first integration (29–30 October, about 2 hours)

1. Each person demonstrates their component.
2. Compare exhaustive and ILP answers on every small admissible instance in an agreed range, for example all \(4\leq d\leq7\) and \(2d<n\leq20\).
3. Run the known \(d=4,5\) regression tests.
4. Deliberately disable or alter one constraint to confirm the tests detect a bad solver.
5. Compare runs with and without \(x_0=1\); the optimum must be unchanged.
6. Freeze version 1 of the solver interface and result schema.
7. Review unresolved questions from the requested papers with the instructors if needed.

Do not begin the large sweep until the exhaustive solver, ILP, and known formulas agree.

### Person 1 — mathematical validation and short-description text (about 5 hours)

- Produce a clean explanation of the problem, the distance simplification, and the resolving condition.
- Summarize the known \(d=4,5\) results and Conjecture 1.
- Build a table comparing the exhaustive solver, ILP, and known formulas on small cases.
- List which statements are known theorems, which are conjectures from the assignment, and which are new experimental observations.

### Person 2 — solver validation and performance study (about 5 hours)

- Compare the ILP against the exhaustive checker on all manageable small cases.
- Benchmark symmetry breaking on representative cases.
- Test at least one alternative exact backend if available.
- Write concise pseudocode and a complexity/size description: \(n\) binary variables and \(\binom n2\) pair constraints.
- Make solver failures explicit: infeasible, timeout, feasible-only, and optimal must not be conflated.

### Person 3 — reproducible preliminary experiment (about 5 hours)

- Run a preliminary sweep for \(d=4,5\).
- Generate a known-values table and a runtime plot directly from CSV.
- Test interrupted-run recovery and duplicate-row handling.
- Draft the experimental-scope paragraph: parameter ranges, exact solver, machine information, timeout rule, and saved outputs.
- Assemble a skeleton of the two-page description from all three contributions.

### Group meeting 3 — preliminary results and proposal structure (5–6 November, about 90 minutes)

1. Review the validation table and benchmark results.
2. Decide which exact solver/backend will be used for the main sweep.
3. Agree on the largest intended \(n\) for each \(d\), while retaining the mandatory target of at least \(n=100\) for \(5\leq d\leq10\).
4. Decide timeout and retry policies before seeing difficult results.
5. Review the two-page description outline.
6. Assign one cross-review: Person 1 reviews solver pseudocode, Person 2 reviews the experiment protocol, and Person 3 reviews mathematical notation.

### Person 1 — contribution to the two-page description (about 3 hours)

- Finalize the mathematical problem statement and notation.
- State the known values and Conjecture 1 exactly.
- Keep the discussion short enough to leave room for methods and planned experiments.

### Person 2 — contribution to the two-page description (about 3 hours)

- Finalize the ILP formulation and short pseudocode.
- Add one small validation result and the planned performance strategy.
- Explain why fixing \(v_0\in W\) is a valid symmetry break.

### Person 3 — edit and reproducibility pass for the two-page description (about 3 hours)

- Combine the sections into a consistent document of at most two pages.
- Add the exact planned parameter range and division of work.
- Check every table value against the generated CSV.
- Return the draft to both teammates for comments; do not silently rewrite mathematical claims.

### Group meeting 4 — approve the short description (11–12 November, about 90 minutes)

1. Read the whole description aloud once.
2. Verify the notation against the assignment, especially the meaning of \(d\).
3. Confirm that the method, experiments, and division of work are all present.
4. Confirm the document is at most two pages.
5. Submit by **13 November 2026** for the first term.
6. Tag the validated solver version that will be used for the main experiments.

### Person 1 — full sweep and analysis for \(d\equiv0\pmod3\) (about 10 hours)

- Own \(d=6,9,12,15\).
- Reach at least \(n=100\) for \(d=6,9\); go as far beyond \(40\) as practical for \(d=12,15\).
- Organize formula searches by \(n\bmod(2d-1)\).
- Test both the conjectured lower bound and equality condition \((2d-1)\mid n\).
- Record all timeouts and reruns, and run a few overlap instances owned by Person 2.

### Person 2 — full sweep and analysis for \(d\equiv1\pmod3\) (about 10 hours)

- Own \(d=4,7,10,13\).
- Use \(d=4\) primarily as a known-formula test and compute it through at least \(n=60\). Do not waste the entire budget forcing slow runs at \(n\geq90\) if the solver behavior matches the warning in the assignment and the validated exact formula already supplies the needed comparison.
- Reach at least \(n=100\) for \(d=7,10\); go as far beyond \(40\) as practical for \(d=13\).
- Organize formula searches by \(n\bmod(d+1)\).
- Test the equality condition \((d+1)\mid n\).
- Record all timeouts and reruns, and run a few overlap instances owned by Person 3.

### Person 3 — full sweep and analysis for \(d\equiv2\pmod3\) (about 10 hours)

- Own \(d=5,8,11,14\).
- Use \(d=5\) as a strong known-formula regression test.
- Reach at least \(n=100\) for \(d=5,8\); go as far beyond \(40\) as practical for \(d=11,14\).
- Organize formula searches by \(n\bmod(d+1)\).
- Test the equality condition \((d+1)\mid n\).
- Record all timeouts and reruns, and run a few overlap instances owned by Person 1.

This congruence-class division gives each person a self-contained set of computations and formula questions. The small overlapping samples detect machine, merge, or configuration errors without duplicating the entire workload.

### Group meeting 5 — merge and audit the main data (20–22 November, about 2 hours)

1. Merge the three result sets with an automated script.
2. Check that there is exactly one authoritative exact row for every intended pair \((n,d)\).
3. Compare all overlap instances.
4. Separate optimal results, timeouts, and feasible-only results.
5. Search automatically for any violation of the conjectured bound or equality characterization.
6. If a surprising case appears, rerun it independently with the exhaustive solver when small or a second exact backend when larger.
7. Redistribute remaining computations according to measured runtime.

### Person 1 — formula hypotheses for the \(d\equiv0\) family (about 6 hours)

- Produce one residue-class table per owned \(d\), using modulus \(2d-1\).
- Express candidate formulas in terms of \(n=q(2d-1)+r\).
- Identify the first observed \(N_d\) after which each formula holds.
- Attempt a mathematical explanation or partial lower/upper-bound argument.
- Clearly list exceptions and the end of the tested range.

### Person 2 — formula hypotheses for the \(d\equiv1\) family (about 6 hours)

- Produce one residue-class table per owned \(d\), using modulus \(d+1\).
- Express candidate formulas in terms of \(n=q(d+1)+r\).
- Identify observed stabilization thresholds and exceptions.
- Compare the new patterns with the known \(d=4\) formula.
- Examine whether rotationally repeated resolving-set patterns explain any upper bounds.

### Person 3 — formula hypotheses for the \(d\equiv2\) family (about 6 hours)

- Produce one residue-class table per owned \(d\), using modulus \(d+1\).
- Express candidate formulas in terms of \(n=q(d+1)+r\).
- Identify observed stabilization thresholds and exceptions.
- Compare the new patterns with the known \(d=5\) formula.
- Create combined plots of metric dimension, lower-bound gap, and runtime for all three families.

### Group meeting 6 — choose the report's actual claims (28–30 November, about 2 hours)

For each proposed formula, classify it as one of:

- proved;
- verified exactly on a finite stated range;
- conjectured from a stable observed pattern;
- too uncertain to claim, but worth mentioning as a question.

Then:

1. Choose the clearest tables and plots rather than including every raw value.
2. Agree on any new conjectures and their exact wording.
3. Decide how to report timeouts and computational limits.
4. Make a report outline with named section owners.
5. Assign cross-reviews outside each person's specialty.

### Person 1 — report theory and interpretation (about 7 hours)

- Draft the introduction, notation, known results, and conjecture sections.
- Explain the distance-\(0/1/2\) simplification.
- Draft the interpretation of observed formulas and any proof ideas.
- Review Person 2's code/method explanation against the actual implementation.

### Person 2 — report method and final software package (about 7 hours)

- Draft the ILP, symmetry-breaking argument, pseudocode, and performance discussion.
- Clean the command-line interface and write exact usage instructions.
- Ensure comments explain mathematical intent rather than only programming syntax.
- Run the project from a clean environment or fresh checkout if possible.
- Review Person 3's tables against selected raw result rows.

### Person 3 — report experiments and visualizations (about 7 hours)

- Draft the data-generation protocol, tested ranges, result tables, and plots.
- Include machine/solver information and timeout policy.
- Generate every final table and plot from scripts.
- Check Conjecture 1 programmatically one final time over all exact rows.
- Review Person 1's formulas and notation against the assignment page.

### Group meeting 7 — full report review and mock defense (6–7 December, 2–3 hours)

1. Read the report as one document and remove contradictions or repeated explanations.
2. Reproduce at least one small and one large result from the written instructions.
3. Verify that every headline claim points to exact data or a proof.
4. Check that the repository contains code, tests, raw results, generated summaries, and usage instructions.
5. Give a timed presentation of at most 20 minutes.
6. Hold a mock defense. Ask each person questions about sections they did **not** write.

Suggested cross-topic defense preparation:

- Person 1 prepares to explain the ILP and symmetry breaking.
- Person 2 prepares to explain Conjecture 1 and the residue-class patterns.
- Person 3 prepares to explain all definitions and the known \(d=4,5\) formulas.

### Person 1 — final mathematical audit (about 3 hours)

- Check every floor, ceiling, modulus, range restriction, and divisibility condition.
- Make sure empirical formulas are labelled as conjectures.
- Proofread the report and slides for notation consistency.

### Person 2 — final reproducibility audit (about 3 hours)

- Run tests and a small end-to-end experiment from the documented commands.
- Confirm exact-solver status is checked everywhere.
- Tag the final code and record the version in the report or release notes.

### Person 3 — final data audit (about 3 hours)

- Regenerate final tables and figures.
- Confirm that reported maxima of \(n\), timeouts, and exception lists match the CSV.
- Check that no plot silently includes non-optimal rows as exact results.

### Group meeting 8 — submission and defense readiness (10 December, about 90 minutes)

1. Perform the submission checklist below.
2. Submit the program and report by **11 December 2026** for the first term.
3. Freeze the submitted commit; make later defense-only changes on a separate branch.
4. Agree on presentation transitions and who answers which first question, while ensuring anyone can answer follow-ups.

## 6. Final submission checklist

- [ ] The report clearly defines directed distance, signatures, resolving sets, metric dimension, and the circulant graph.
- [ ] The convention for \(d\) matches the current assignment rather than the older reports.
- [ ] The ILP is stated mathematically and matches the code.
- [ ] The solver returns both the optimum and a resolving set.
- [ ] Every saved set is independently verified.
- [ ] The \(d=4,5\) known formulas pass automated tests.
- [ ] Every exact result has an optimal solver status.
- [ ] All \(4\leq d\leq15\) are covered for \(n>2d\) as far as computationally practical.
- [ ] The range reaches at least \(n=100\) for every \(5\leq d\leq10\).
- [ ] Both parts of Conjecture 1—the bound and equality characterization—are tested.
- [ ] Formula guesses specify moduli, observed starting points, exceptions, and maximum tested \(n\).
- [ ] The report distinguishes proofs, exact finite verification, and empirical conjectures.
- [ ] Code has comments and usable run instructions.
- [ ] Tables and plots can be regenerated from saved data.
- [ ] All three people can explain the entire workflow at the defense.

