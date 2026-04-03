# Analysis: Scaffolding Dynamic Leader-Follower Configurations in Swarm Robotics

**Paper**: Pranay Vaddepalli, Fethi Candan, Alejandro Jimenez Rodriguez
**Target**: Bioinspiration & Biomimetics (IOP)
**Reviewer**: Alejandro / Mahault
**Date**: 2026-04-03
**Version**: Working document (49pp, Overleaf export)

---

## 1. Overall Assessment

**Verdict: Strong science, needs major surgery on length and one headline claim.**

The theoretical contribution (constraint closure as continual learning mechanism), dual pressure formalization, and three-method causal triangulation are genuine contributions. The expanded working document has significantly improved the methodology exposition and added valuable content (ablation studies, bond cap robustness, behavioral intervention analysis). However:

1. **The paper is now 49 pages with 29 figures.** Target journal accepts 12-15 pages. This must be cut by 60%+.
2. **The post-crisis "enhancement" claim (27.8-42.0%) is now provably misleading.** Table 6 in the paper itself contains the control data that undermines the claim (see Section 3.1 below).
3. **Three overlapping pressure frameworks** (P_net, geometric, Landau) create confusion rather than convergence.

The core findings — circular causality, phase transitions, channel-specific perturbation response, bidirectional causal symmetry — are all solid and publishable. The paper needs to be honest about what it shows and ruthless about what it cuts.

---

## 2. What Works Well

### 2.1 Narrative structure
The rhetorical architecture remains the paper's greatest strength. The progression from emergence validation (3.1) to mechanistic explanation (3.2) to robustness testing (3.3) to causal analysis (3.4) is logical and well-paced.

### 2.2 Dual formalisation (geometric + Landau)
The geometric state-space decomposition (Eqs. 6-9) remains excellent: no free parameters, clear physical interpretation, data-driven phase boundaries. The convergence of geometric crossover (t=1,312) and Landau critical energy (E*=158) on consistent phase boundaries is genuinely convincing.

### 2.3 Causal analysis methodology
The three-method triangulation (Granger, ATE, do-operator) is now fully developed and impressive. The Cohen's d normalization correction — collapsing an apparent 870x asymmetry to ~1x — is a clean, quotable methodological finding. The efficiency vs. total force distinction is the paper's best one-liner:

> "Individual behaviour is more *efficient* per unit of intervention (d ~ 1.7-2.2), while the group exerts more *total force* through collective action (d ~ 1.2-1.6)."

### 2.4 Channel-specific perturbation response
The scarcity/leader-removal contrast remains the most novel contribution:
- Scarcity hits explicit pressure channel: bonds buffered (S_VN unchanged ~0.101-0.105, P_Landau stays +16.3)
- Leader removal hits implicit pressure channel: P_implicit spike to -6/-7, bond collapse -31%, S_VN overshoot 0.101 -> 0.085 -> 0.151

The geometric implicit pressure spike (Figure 22, Panel 3) as a diagnostic signature is an excellent addition.

### 2.5 New additions that work well
- **Ablation studies (Table 4, Figure 15):** Clean essentiality criteria, three parameters confirmed essential with distinct mechanistic roles. The following-benefit ablation producing paradoxical staircase increase (+182%) but CCI collapse (-56.2%) is a nice diagnostic distinction.
- **Bond cap robustness (Table 3, Figure 14):** Three cap values all producing three-phase dynamics addresses a natural reviewer concern.
- **Energy poverty trap (Figure 25):** Agents R2 and R6 becoming permanently isolated through bidirectional feedback failure is an interesting emergent phenomenon. The framing in Section 4.8 as illustrating bidirectional causality is appropriate.
- **Multi-run validation (Figure 3):** N=50 runs confirming the lag structure is a significant improvement over single-run results.

---

## 3. Substantive Issues (Must Fix)

### 3.1 [CRITICAL] Post-crisis enhancement claim is contradicted by the paper's own control data

**The problem:** The paper headlines "27.8-42.0% post-crisis performance enhancement" in the abstract, conclusions, and throughout. This comparison is between Final energy (trials 4800-5000) and pre-perturbation baseline (trials 1900-2000, ~139 units). But the paper now includes Table 6, which contains a control row:

| Scenario | Phase | E_bar |
|----------|-------|-------|
| Scarcity | After (trials 3000-4000) | 160.8 |
| Leader Removal | After (trials 3000-4000) | 138.5 |
| **Control** | **After (trials 3000-4000)** | **158.1** |

**What this reveals:**
- **Scarcity "enhancement":** 160.8 vs 158.1 control = **+1.7%** (not 27.8%)
- **Leader removal "enhancement":** 138.5 vs 158.1 control = **-12.4%** (actually *worse* than control)

Even acknowledging that the thermodynamic runs (4000 trials) differ from the main perturbation runs (5000 trials), this is devastating for the enhancement narrative. At the 4000-trial timepoint, the system has NOT exceeded the control — it has barely caught up (scarcity) or fallen behind (leader removal).

**The 5000-trial discrepancy:** The main perturbation runs (Table 5) report scarcity reaching 177.92 and leader removal reaching 197.33 by trial 5000. These are compared against baseline at trial 2000 (~139), NOT against the control at trial 5000. Looking at Figure 17, the control (dashed) appears to converge with the perturbed simulation around ~170-180 by trial 5000. Figure 18 is ambiguous but the control appears lower.

**What actually happened:** At trial 2000, the system is still in the transition phase (energy ~139 vs stable phase ~158). The "enhancement" is mostly the system continuing its normal emergence trajectory. The perturbation may have slightly delayed or (for leader removal) slightly accelerated convergence, but the effect is small, not 27.8-42.0%.

**Required fix:**
1. **Report perturbed vs. control at matched timepoints** as the primary comparison in Table 5.
2. **If the enhancement is genuine against the control** (possible for leader removal at t=5000 based on Figure 18), report THAT number, not the comparison against t=2000 baseline.
3. **If the enhancement vanishes** (likely for scarcity), reframe the headline claim as:
   - **Resilience**: "minimal degradation (0.9-2.3%) under extreme perturbation"
   - **Channel-specific recovery**: "distinct thermodynamic signatures depending on perturbation type"
   - These are still strong, publishable findings — just not "paradoxical enhancement."
4. **Remove "27.8-42.0% enhancement" from the abstract** until the comparison against control is done properly.

**Note:** The Limitations section (4.7) already acknowledges "multi-run confirmation" is needed, which is good. But the Abstract and Conclusions still headline the unsupported numbers.

### 3.2 [CRITICAL] CCI inconsistency with Bonds->Success null result

**The problem:** The CCI metric is defined (Section 2.3) as "Minimum peak correlation across the four constraint closure pathways." Bonds->Success shows no significant correlation at any lag across all 50 runs (Section 3.1, point 4; Figure 3). If CCI = min of four pathways and one pathway ≈ 0, then CCI ≈ 0 for all runs.

But the ablation study (Table 4) reports baseline CCI values that are clearly > 0 (e.g., the ablation of basal metabolism causes -78.8% CCI reduction, implying baseline CCI was meaningfully positive).

**This is an internal contradiction.** Either:
1. CCI is computed differently than defined (e.g., excluding Bonds->Success, or using Granger F-statistics)
2. The ablation runs (3000 trials, N=30) show different Bonds->Success dynamics than the main runs (5000 trials, N=50)
3. There's a bug in the CCI computation

**Required fix:** Clarify the CCI computation. If it excludes or weights down the Bonds->Success pathway, say so explicitly and justify. The most defensible position: "CCI uses three of four pathways that operate on the 10-trial block timescale; the Bonds->Success pathway is validated separately through ATE evidence (Section 3.4) because it operates on a slower timescale not captured by block cross-correlation."

### 3.3 [CRITICAL] Three pressure frameworks create confusion

**The problem:** The paper now has three distinct pressure quantities:
1. **P_net** (Eq. 1): Four-component mechanistic pressure (energy deficit, initiation barrier, consensus cost, grooming barrier)
2. **P_explicit / P_implicit** (Eqs. 7-9): Geometric state-space decomposition
3. **P_Landau** (Eq. 13): Thermodynamic pressure from free energy model

Their relationships are never established. Section 3.2 plots P_Landau vs P_net (Figure 9) showing they're both monotonically decreasing, but one peaks during Phase II while the other doesn't. The geometric pressures are plotted separately (Figure 8). The reader is left confused about which pressure "matters" and what each one actually measures.

**Why this matters:** Reviewers will ask: "You have three pressure models. Do they agree? Which one is primary? Why do you need all three?"

**Required fix:**
- **Demote P_net to a diagnostic tool**, not a formalization. It measures constraint difficulty, not pressure in the Wilson & Prescott sense.
- **Keep the geometric decomposition as the primary formalization** (it's the one that operationalizes Wilson & Prescott with no free parameters).
- **Keep Landau as a complementary lens** (it connects to established phase transition theory and provides the P_Landau sign diagnostic for perturbation analysis).
- **Add a paragraph** explicitly stating how the three relate: "P_net quantifies constraint difficulty; the geometric decomposition formalizes Wilson & Prescott's pressure concept; the Landau model provides thermodynamic predictions."

### 3.4 [IMPORTANT] Leader selection vs. leadership emergence — still unresolved

Algorithm 1 still reads "Choose leader l uniformly from active agents." The paper text (Section 3.1, bond matrix evolution) describes emergent leaders. The mechanism is sound (random initiation, but bond-dependent following makes only some initiators effective), but the terminology remains confusing.

**Fix:** Rename to "initiator" or "focal agent" in Algorithm 1. Add one sentence distinguishing initiation (random) from effective leadership (bond-dependent).

### 3.5 [IMPORTANT] Landau model issues — still unresolved

Eq. 12 still claims a = -0.5, b = 10^-6 "following standard Landau theory conventions." The non-standard form F = aB^2 + bEB^4 (E in quartic term) is still unjustified. The Pearson correlation with F_data is only r = 0.54 (Section 3.2), which is moderate at best.

**Fix:** Use option 3 from previous analysis — acknowledge the parameters as qualitative. For the non-standard form, justify why E multiplies the quartic term (intuitively: higher energy accelerates the restoring force? But this should be stated).

---

## 4. Presentation Issues (Must Fix for Journal)

### 4.1 [CRITICAL] Paper length: 49 pages must become ~20

The paper is **3x the maximum length** for Bioinspiration & Biomimetics. This is the single biggest practical obstacle to submission.

**Recommended structure (target ~18-20 pages including references):**

**Keep in main text (12-14 pages):**
- Introduction (shortened to 1.5 pages)
- Methods: Keep Eqs. 6-9 (geometric), Eq. 12 (Landau), Algorithm 1 (condensed), experimental design (condensed)
- Results 3.1: Circular causality with multi-run validation — Figures 2, 3 (merge into one composite)
- Results 3.2: Three-phase emergence — Figures 4, 5 (bond matrix + energy staircase only)
- Results 3.3: Perturbation response — Figures 17, 18 (two 2-panel figures)
- Results 3.4: Behavioral intervention — Figures 27-29 (condensed to key panels)
- Discussion (shortened to 2-3 pages)
- Conclusions (1 page max)

**Move to supplementary material:**
- Algorithm 2 (grooming/decay details)
- P_net formalization (Eqs. 1-5) and Figures 6, 7
- Thermodynamic detail figures (Figs 9-13, 19-24)
- Ablation studies (Table 4, Figure 15)
- Bond cap robustness (Table 3, Figure 14)
- Scalability (Figure 16)
- Network structure (Figure 25)
- Granger heatmaps (Figure 26)

**Cut entirely:**
- Figure 11 (Shannon vs VN entropy normalized comparison — adds nothing beyond Figure 10)
- Phase diagram Figure 13 (already shown in the geometric pressure figure)
- Redundant bond matrix snapshots in perturbation sections (Figs 21, 24 — keep one set)

### 4.2 Figure count: 29 figures must become ~8-10

For a 15-page journal paper, 8-10 figures is the maximum. Current 29 is absurd. Suggested final figure set:

| Fig | Content | Panels |
|-----|---------|--------|
| 1 | Task schematic | 4 panels (existing Figure 1) |
| 2 | Circular causality evidence | 4 cross-correlation + 4 Granger (merge Figs 2-3) |
| 3 | Bond matrix evolution | 3 heatmaps (existing Figure 4) |
| 4 | Energy staircase + pressure crossover | 2 panels (merge Figs 5, 8) |
| 5 | Thermodynamic diagnostics | Table 2 + P_Landau vs P_net (condensed Fig 9) |
| 6 | Resource scarcity response | 2 panels: energy + bonds (existing Fig 17) |
| 7 | Leader removal response | 2 panels: energy + bonds (existing Fig 18) |
| 8 | ATE dose-response | Key forward+reverse panels (condensed Figs 27-28) |
| 9 | Do-operator + synthesis | Panel from Fig 29 + Table 7 |
| 10 | Scalability | Existing Fig 16 (optional, could be supplementary) |

Everything else goes to supplementary.

### 4.3 Conclusions section is too long and repetitive

The current conclusions (Section 5) runs 3+ pages and essentially re-reports the results with full statistics. Conclusions should be 1 page:
- 1 paragraph: what we showed (constraint closure enables emergent coordination)
- 1 paragraph: what's new (channel-specific perturbation, bidirectional causality, normalization correction)
- 1 paragraph: limitations + next steps
- 1 paragraph: practical implications

### 4.4 Discussion subsections overlap

Sections 4.1 (multi-timescale scaffolding) and 4.3 (constraint closure as continual learning) cover similar ground. Sections 4.4 (stress-enhanced performance) repeats the perturbation results. Merge 4.1+4.3 and compress 4.4 to the mechanistic insight only.

### 4.5 Figure quality

The figures are matplotlib defaults throughout. All need:
- Consistent font sizes (minimum 8pt at print size)
- Vector output (PDF/EPS)
- Colourblind-safe palette
- Consistent phase colours across all figures
- Remove redundant titles (captions handle this)
- IOP single-column (86mm) or double-column (170mm) widths

### 4.6 Missing metadata

- Alejandro's ORCID still placeholder (0000-0000-0000-0000). Pranay's is now real (0009-0002-5812-8245). Fethi's still placeholder.
- Received/Revised dates still "dd Month yyyy"
- Volume/issue still "vv/aaaaaa"

---

## 5. Minor Issues

### 5.1 Dunbar's number analogy
Section 3.3 and 4.5 claim the 10-12 agent breakdown "parallels Dunbar's subgroup constraints." This is still a stretch. The breakdown is a grooming bandwidth saturation effect (N(N-1)/2 quadratic cost vs linear benefit), acknowledged in Section 4.5. Call it what it is and note the Dunbar parallel as suggestive, not definitive.

### 5.2 Algorithm presentation
Algorithm 1 is now 1.5 pages long. The if/else/go-to structure is still confusing. For the journal version, condense to pseudocode covering the key decisions (initiation, following, grooming) without implementation-level detail.

### 5.3 Von Neumann entropy definition
Eq. 10 still uses "positive eigenvalues." Bond matrix A_ij is non-negative symmetric but could have negative eigenvalues. Clarify handling.

### 5.4 Energy poverty trap implications
20% of agents (2/10) become permanently isolated (Figure 25). This is a significant limitation — the system fails for 1 in 5 agents. The paper discusses this as illustrating bidirectional causality (4.8), which is appropriate, but should also discuss whether this is a design flaw to be addressed (e.g., by adding bond decay so trapped agents can eventually re-enter) or an expected feature of the model.

### 5.5 Eq. 17 notation
Still informal: "A_ij(t+1) = min(200, A_ij(t) + 0.2 if grooming(i,j))". Use piecewise notation.

### 5.6 Wilson & Prescott citation density
[29] is cited extensively throughout Sections 2.1 and 4.1. This is appropriate but add a clear statement of how this work extends Wilson & Prescott.

### 5.7 Recent references
References [32-35] cite volume/page numbers. Verify publication status at submission time.

### 5.8 Follower recruitment before initiation
Algorithm 1 comments note "Follower recruitment (done before initiation in code)." This is unusual — followers are recruited before the leader decides whether to initiate. If the leader doesn't initiate (goes to Phase 5), the recruitment was wasted computation. This doesn't affect results but is conceptually odd and a reviewer might flag it.

---

## 6. Figure Specifications (for regeneration)

### General requirements
- **Format:** Vector (PDF or EPS) for submission
- **Font:** Sans-serif (Helvetica/Arial), minimum 8pt at print size
- **Colour:** Colourblind-safe palette (seaborn "colorblind" or matplotlib "tab10")
- **Resolution:** 300 DPI minimum for raster elements
- **Width:** Single column (86mm) or double column (170mm) per IOP guidelines
- **Phase colours:** Use consistent palette throughout — e.g., Phase I = blue, Phase II = orange, Phase III = green

### Key figure specs

**Circular causality composite (merge current Figs 2+3):**
- Double-column figure
- Top: 4 single-run cross-correlation panels (compact, shared y-axis)
- Bottom: 4 multi-run validation panels (shared y-axis)
- Significance markers clear; remove redundant legends

**Energy staircase + pressure crossover (merge current Figs 5+8):**
- Two-panel vertical stack, single-column
- Top: pressure crossover with subtle phase shading
- Bottom: energy staircase with phase boundaries

**Perturbation figures (Figs 17, 18 as-is but cleaned):**
- Two-panel each: energy + bonds over time
- Control as dashed line (essential for the comparison)
- Intervention period as subtle shading (not heavy red)
- Same y-axis scale for both scenarios

**ATE composite (condense Figs 27-29):**
- Double-column
- Left: 3x2 grid of forward dose-response (key intervention x outcome pairs only)
- Right: 3x2 reverse dose-response
- Keep Cohen's d axis; include significance bands

---

## 7. Recommended Action Plan (Priority Order)

| # | Priority | Issue | Section | Effort |
|---|----------|-------|---------|--------|
| 1 | CRITICAL | **Fix post-crisis enhancement claim**: Compare perturbed vs control at t=5000, reframe as resilience if enhancement vanishes | Abstract, 3.3, 3.5, Table 5, Conclusions | Medium (re-analysis + rewrite) |
| 2 | CRITICAL | **Cut paper to 18-20 pages**: Move supplementary material, reduce figures to 8-10 | All | High (structural rewrite) |
| 3 | CRITICAL | **Resolve CCI inconsistency**: Clarify how CCI is computed given Bonds->Success null result | 2.3, 3.1, Table 4 | Low (clarification) |
| 4 | CRITICAL | **Clarify three pressure frameworks**: Establish which is primary, how they relate | 2.1, 3.2 | Medium (restructure Section 2.1) |
| 5 | IMPORTANT | Clarify leader selection (random) vs. leadership emergence (bond-dependent) | 2.2, Algorithm 1 | Low (terminology) |
| 6 | IMPORTANT | Acknowledge Landau parameters as qualitative, justify Eq. 12 form | 2.1 | Low (rewrite) |
| 7 | PRESENTATION | Regenerate all figures to publication quality | All | High (Pranay) |
| 8 | PRESENTATION | Compress Conclusions to 1 page | Section 5 | Low |
| 9 | MINOR | Fix Dunbar analogy, Eq. 17, ORCIDs, poverty trap discussion, Algorithm flow | Various | Low |

---

## 8. What Changed Between Versions

For reference, comparing the earlier PDF (27pp) to the working document (49pp):

**Expanded/Added:**
- P_net formalization (Eqs 1-5) — new
- Algorithm 2 (grooming/decay) — new
- Five experimental protocols described in detail — expanded
- Multi-run validation (N=50) with Figure 3 — new
- Net implicit pressure dynamics (Figures 6-7) — new
- Bond cap robustness (Table 3, Figure 14) — new
- Ablation studies (Table 4, Figure 15) — expanded
- Scalability (Figure 16) — expanded
- Thermodynamic perturbation analysis (Figures 19-24) — significantly expanded
- Behavioral intervention (Figures 25-29, Table 7) — significantly expanded
- Discussion subsections 4.1-4.9 — expanded
- Conclusions with five numbered findings — expanded
- Pranay's ORCID now real

**Unchanged issues:**
- Post-crisis enhancement comparison methodology (still vs. baseline, not vs. control)
- Leader/initiator terminology in Algorithm 1
- Landau parameters "standard conventions" claim
- Eq. 12 non-standard form
- Eq. 17 informal notation
- Alejandro/Fethi ORCID placeholders

**Partially addressed:**
- Bonds->Success gap now explicitly discussed in results (Section 3.1, point 4), but CCI inconsistency introduced
- Thermodynamic perturbation analysis now includes Control row in Table 6 — which actually undermines the enhancement claim

---

## 9. Summary for Pranay

**The science is strong. The paper needs two things before submission:**

1. **Honesty about the enhancement claim.** Table 6 shows the control reaches comparable energy levels. The "27.8-42.0% enhancement" is mostly the system continuing its normal emergence trajectory. The real finding — channel-specific resilience with distinct thermodynamic signatures — is publishable and novel. Reframe accordingly.

2. **Ruthless cutting.** 49 pages must become ~20. The supplementary material will be substantial, but the main text must focus on: (a) circular causality evidence, (b) geometric pressure formalization, (c) channel-specific perturbation response, (d) bidirectional causal symmetry. Everything else is supporting evidence that belongs in supplementary.

Address these two issues and the paper is ready for submission. The underlying contributions — constraint closure as continual learning mechanism, no-free-parameter geometric formalization, thermodynamic perturbation diagnostics, Cohen's d normalization correction — are all genuine and worth publishing.
