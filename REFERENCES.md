# Reference review for Project 1

Research status: **21 October 2026**.

This note covers the sources mentioned in the introduction and Project 1 of `navodila.pdf`. It distinguishes material that is publicly available from material that still needs to be requested from the instructors.

## Availability summary

| Item | Publicly located? | Useful material |
|---|---|---|
| [19] M. Knor, J. Sedlar, R. Škrekovski, T. Vetrík, *On the metric dimension of directed circulant graphs* | No public copy found | The current assignment quotes its consecutive-generator results; request the submitted manuscript from the instructors. |
| [20] J. Mazej, L. Stojčić, *Metrične dimenzije usmerjenih grafov* | Yes | Public GitHub repository with Sage notebooks, LaTeX report, and Excel results through `n=80` for several families. |
| [21] M. Popovič, *Metrične dimenzije usmerjenih cirkulantnih grafov* | Yes | A public 2026 journal article that appears to be a polished version of the earlier student work: ILP, distance and diameter formulas, bounds, proofs, and experiments for `n <= 40`. |
| [22] T. Vetrík, *On the metric dimension of directed and undirected circulant graphs* | Yes | Open-access 2020 paper; exact result for directed circulants with two generators. |
| *Computational tools for the projects* | No public copy found | Request it from the instructors; the assignment says it covers Sage, `geng`, ILP, SAT, heuristics, and multigraphs. |

Exact-title searches, author searches, arXiv searches, and searches of relevant public author/university pages did not reveal [19] or the computational-tools handout. This is not proof that no public copy exists; it only means that the group should not wait for one to be indexed.

## Reference [20]: Mazej and Stojčić repository

Public repository: <https://github.com/JasminaMazej/projektna-naloga-FP->

Inspected revision:

```text
commit: c7e4391e95132212add8eb3bd9ddb4d8f2e17f43
commit date: 2025-12-20
license: MIT
```

Relevant files in that repository:

- `nal2.ipynb`: calculations for graphs of the form `C_n(1,...,n-m)`;
- `projekt.ipynb`: experiments for `C_n(1,...,k)` and threshold questions;
- `porocilo.tex` / `porocilo.pdf`: final report;
- `rezultati_naloga_2.xlsx`: metric-dimension values for `m=4,...,10` up to `n=80`;
- `rezultati_dimenzija_cirkulantnih_grafov.xlsx`: results for the threshold study.

### Notation translation

Their notebook function constructs

```text
clockwise_circulant_graph(n, n-m)
```

when studying `C_n(1,...,n-m)`. Therefore their parameter `m` is the same as the current assignment's parameter `d`. Elsewhere in their report, `k` denotes the number of included generators, so always inspect the surrounding section before reusing a formula.

### Reusable ILP idea

The notebook code:

1. computes all directed distances;
2. creates one binary variable `x[w]` for every possible landmark;
3. for every ordered pair `u != v`, finds all `w` for which `d(w,u) != d(w,v)`;
4. adds the constraint that at least one such `w` is selected;
5. minimizes the number of selected vertices.

This is the same hitting-set model stated in `PROJECT_PLAN.md`, and it is a useful implementation reference. It should not be copied unchanged:

- it adds both `(u,v)` and `(v,u)`, although one constraint per unordered pair is enough;
- it computes all-pairs graph distances even though the present project has a direct modular formula;
- it has no `v_0 in W` symmetry-breaking constraint;
- it does not save solver name, solver status, runtime, or optimality metadata;
- it does not run a separate post-solve certificate checker;
- it extracts variables using exact comparison with `1`, which is less robust than using a numerical tolerance when a backend returns floating-point values.

Use the old code as a mathematical cross-check, not as the final production program.

### Reported formulas and observations

The report records the following computational observations:

- for `d=4`, the values agree with `ceil(2n/5)` through `n=80` (the report starts below the range of the current assignment as well);
- for `d=5`, it gives

  $
  \dim(C_n(1,\ldots,n-5))=
  \begin{cases}
  n/3,&3\mid n,\\
  \lceil n/3\rceil+1,&3\nmid n,
  \end{cases}
  $

  which is equivalent to the formula in the current assignment, `floor(n/3)+2` in the nondivisible case;
- for `d=6`, it gives only an ad hoc finite-range fit involving `round(9n/25+1)`, a correction when `11 | n`, and a short exception list;
- it did not find convincing simple formulas for `d=7,8,9,10`.

The `d=6` expression should not be promoted to a conjectured exact formula. Its main value is historical: it shows why the new assignment asks for residue-class analysis modulo `2d-1=11` instead of fitting one rounded real-valued line.

### Extracted legacy dataset

The workbook has been converted to a tidy CSV:

- [`reference_data/reference20_metric_dimensions.csv`](reference_data/reference20_metric_dimensions.csv)
- [`reference_data/README.md`](reference_data/README.md)

It contains only the current project range `n>2d`:

| `d` | Rows | `n` range |
|---:|---:|---:|
| 4 | 72 | 9–80 |
| 5 | 70 | 11–80 |
| 6 | 68 | 13–80 |
| 7 | 66 | 15–80 |
| 8 | 64 | 17–80 |
| 9 | 62 | 19–80 |
| 10 | 60 | 21–80 |
| **Total** | **462** | |

Checks performed during extraction:

- zero disagreements with the known `d=4` formula;
- zero disagreements with the known `d=5` formula;
- zero violations of the lower bound in Conjecture 1;
- zero disagreements with Conjecture 1's claimed equality characterization.

Equality cases found in the 462 rows:

| `d` | Equality cases | Predicted divisor |
|---:|---:|---:|
| 4 | 15 | `d+1=5` |
| 5 | 12 | `d+1=6` |
| 6 | 6 | `2d-1=11` |
| 7 | 9 | `d+1=8` |
| 8 | 7 | `d+1=9` |
| 9 | 3 | `2d-1=17` |
| 10 | 6 | `d+1=11` |

These checks are encouraging but are **secondary analysis of legacy output**, not new independently verified results. The group should rerun the instances with its own solver and certificate checker before citing them as project results.

## Reference [21]: Popovič public article

Journal page: <https://matrika.fmf.uni-lj.si/letnik-13/stevilka-2/Popovic.html>

PDF: <https://matrika.fmf.uni-lj.si/letnik-13/stevilka-2/Popovic.pdf>

The assignment cites a 2024 student project. Based on the matching author, title, topic, and ILP lineage, the publicly available article appears to be a polished version of that work. It was published in *Matrika*, volume 13, issue 2 (2026), under the journal's CC BY 4.0 policy for articles published since September 2025.

### Important convention

This source writes

$
C(n,k)=C_n(1,2,\ldots,k),
$

where `k` is the largest included generator. For the graph in the current assignment, substitute

$
k=n-d.
$

### Distance and diameter formulas

For vertices indexed by `0,...,n-1`, put

$
\Delta(u,v)=(v-u)\bmod n.
$

The article proves

$
d(u,v)=\left\lceil\frac{\Delta(u,v)}{k}\right\rceil
$

for `C(n,k)`, and consequently

$
\operatorname{diam}(C(n,k))=
\left\lceil\frac{n-1}{k}\right\rceil.
$

For the present project, `k=n-d`. If `n>2d`, then `n-d>n/2`, so the diameter is 2 and the formula becomes exactly the direct `0/1/2` distance rule used in `PROJECT_PLAN.md`.

This gives a clean derivation to cite in the report and a general formula with which to test the specialized implementation.

### General bounds from the article

The source gives the resolving set

$
W=\{0,1,\ldots,k-1\}
$

and obtains

$
\dim(C(n,k))\leq k.
$

It also uses signature counting to obtain

$
\dim(C(n,k))\geq
\left\lceil\log_{D+1}(n)\right\rceil,
\qquad
D=\left\lceil\frac{n-1}{k}\right\rceil.
$

After substituting `k=n-d` in the range `n>2d`, these become

$
\dim(C_n(1,\ldots,n-d))\leq n-d
$

and

$
\dim(C_n(1,\ldots,n-d))\geq\lceil\log_3 n\rceil.
$

Both are valid but much weaker than the specialized linear lower bound in Conjecture 1. They are still useful as independent sanity checks for the solver.

### Exact special cases

The article proves or records:

$
\dim(C(n,1))=1,\qquad
\dim(C(n,2))=2,
$

$
\dim(C(n,n-1))=n-1,
\qquad
\dim(C(n,n-2))=\left\lfloor\frac n2\right\rfloor.
$

It reports computations for all `C(n,k)` with `n<=40`, displayed as a heatmap. No machine-readable table or separate public source-code repository was located on the article page. The [20] repository says its ILP was adapted from Popovič's code and exposes a closely matching implementation.

### ILP note

The article's Sage code uses the same direct pair-separation constraints as [20]. This is the formulation to follow. Its surrounding mathematical description introduces auxiliary variables, but the simpler precomputed-set formulation in `PROJECT_PLAN.md` needs only the `x_w` variables and is less error-prone.

## Reference [22]: Vetrík 2020

DOI and article page: <https://doi.org/10.7151/dmgt.2110>

Open PDF: <https://www.dmgt.uz.zgora.pl/publish/pdf.php?doi=2110>

Full citation:

> T. Vetrík, “On the metric dimension of directed and undirected circulant graphs,” *Discussiones Mathematicae Graph Theory* 40 (2020), 67–76. DOI: 10.7151/dmgt.2110.

The directed part studies the two-generator graph `C_n(1,t)`, not the dense consecutive-generator graph in the current project. Its main relevant result is

$
\dim(C_n(1,t))=t
\qquad\text{for }t\geq2\text{ and }n\geq2t^2.
$

The paper proves a lower bound of `t` in that range and an upper bound of `t` for every `2<=t<n`; the upper bound uses `t` consecutive landmarks. It works with the isomorphic reverse orientation `C_n(-1,-t)` in the proof.

This paper provides background for the assignment's statement that directed circulants with two generators have been studied. It does not provide code or a result that directly determines `dim(C_n(1,...,n-d))`.

## Recommended use in this project

1. Cite [21] for the general distance formula, diameter formula, general bounds, and the earlier ILP approach.
2. Use [20]'s code and CSV only as independent comparison material. Reimplement the optimized solver yourselves.
3. Use [22] in the related-work paragraph, not as experimental ground truth for the present graph family.
4. Ask the instructors for [19], since it is the source most closely connected to the current conjecture and known consecutive-generator results.
5. Ask for the computational-tools handout and, if available, Popovič's original machine-readable `n<=40` data.
6. Record the exact source, commit, and notation conversion whenever an old value is imported.

