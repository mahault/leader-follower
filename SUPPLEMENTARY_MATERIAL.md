# Supplementary Material

## Scaffolding Dynamic Leader-Follower Coordination in Swarm Robotics through Constraint Closure and Multi-Timescale Social Interactions

Pranay Vaddepalli, Fethi Candan, Alejandro Jimenez Rodriguez

---

## S1. Component-Level Pressure Decomposition ($P_{net}$)

The net pressure $P_{net}$ quantifies constraint difficulty at the individual agent level through four mechanistic components:

$$P_{net}(t) = P_{energy}(t) + P_{init}(t) + P_{consensus}(t) + P_{groom}(t) \tag{S1}$$

**Energy deficit pressure** captures the gap between current energy and the minimum required for coordination:

$$P_{energy}(t) = \max\left(0, \; \frac{E_{threshold} - \bar{E}(t)}{E_{max}}\right) \tag{S2}$$

**Initiation barrier pressure** measures the difficulty of successful task initiation:

$$P_{init}(t) = 1 - \bar{P}_{init}(t) \tag{S3}$$

where $\bar{P}_{init}$ is the population mean initiation probability (Eq. 15 in main text).

**Consensus cost pressure** reflects the difficulty of forming functional groups:

$$P_{consensus}(t) = 1 - \frac{\bar{n}_{followers}(t)}{N - 1} \tag{S4}$$

where $\bar{n}_{followers}$ is the mean number of followers successfully recruited per initiation attempt.

**Grooming barrier pressure** captures the opportunity cost of social investment:

$$P_{groom}(t) = \frac{G_{cost}}{E(t)} \cdot \left(1 - \frac{\bar{A}(t)}{B_{cap}}\right) \tag{S5}$$

where $G_{cost}$ is the energy cost of grooming and $\bar{A}$ is the population mean bond strength.

$P_{net}$ provides a detailed mechanistic decomposition useful for understanding which specific constraints are active at each phase, but because it involves multiple derived quantities (initiation probability, follower count), it does not have the parameter-free character of the geometric decomposition (Eqs. 6-9).

**Figures S1.1-S1.2:** Component pressure trajectories across the three phases, showing the evolution of each $P_{net}$ component. Energy deficit pressure dominates in Phase I, initiation barrier in Phase II, and all components reach low levels in Phase III. Scatter plots of $P_{net}$ versus $P_{explicit}$ and $P_{implicit}$ demonstrate the complementary but non-redundant relationship between the frameworks.

---

## S2. Full Algorithm Specification

### Algorithm 1 (Full Version): Multi-Timescale Social Coordination

```
ALGORITHM 1: Multi-Timescale Social Coordination
Input: N agents, T trials, bond matrix A[N×N], energy vector E[N]
Output: Updated A, E, performance metrics

for t = 1 to T do:
    // PHASE 1: Initiator selection
    active_agents ← {i : E_i > 0}
    l ← uniform_random(active_agents)     // Random initiator (not leader)

    // PHASE 2: Initiation decision
    p_init ← σ(α₁·E_l/E_max + α₂·Ā_l/A_max + α₃·r_success_l)
    if rand() > p_init then:
        E_l ← E_l + E_solo                // Solo foraging (reduced reward)
        go to PHASE 5

    // PHASE 3: Follower recruitment
    followers ← {}
    for j in active_agents \ {l} do:
        p_follow ← σ(β₁·A_jl/A_max + β₂·E_j/E_max + β₃·(1 - r_recent_j))
        if rand() < p_follow then:
            followers ← followers ∪ {j}

    // Cancellation check
    if |followers| < min_group then:
        E_l ← E_l + E_solo
        go to PHASE 5

    // PHASE 4: Task execution
    group ← {l} ∪ followers
    success ← task_execute(group)          // Success depends on group size
    if success then:
        reward ← base_reward × (1 + coordination_bonus × |group|)
        for i in group do:
            E_i ← E_i + reward / |group|
            if i ≠ l then:
                E_i ← E_i + following_benefit  // Follower bonus
    else:
        for i in group do:
            E_i ← E_i - task_cost

    // PHASE 5: Grooming
    for each pair (i, j) where both participated in task do:
        if rand() < p_groom(E_i, A_ij) then:
            A_ij ← min(B_cap, A_ij + δ_A)
            A_ji ← min(B_cap, A_ji + δ_A)  // Symmetric bond update
            E_i ← E_i - groom_cost
            E_j ← E_j - groom_cost

    // PHASE 6: Energy dynamics
    for i = 1 to N do:
        E_i ← E_i - metabolism_cost         // Basal metabolism
        E_i ← max(0, E_i)                   // Non-negative constraint

    // Record metrics
    record(t, E, A, success, |followers|)
end for
```

### Algorithm 2: Grooming Decision and Bond Dynamics

```
ALGORITHM 2: Grooming Decision
Input: Agent i, partner j, bond matrix A, energy E_i
Output: Updated A_ij, E_i

// Grooming probability increases with energy surplus and bond potential
p_groom ← σ(γ₁·(E_i - E_threshold)/E_max + γ₂·(1 - A_ij/B_cap))

if rand() < p_groom then:
    A_ij ← min(B_cap, A_ij + δ_A)          // Bond increment
    A_ji ← min(B_cap, A_ji + δ_A)          // Symmetric
    E_i ← E_i - groom_cost                 // Energy cost of grooming

    // Note: Bond decay is NOT implemented — bonds are permanent
    // once formed. This design choice means isolated agents cannot
    // recover from the energy poverty trap (see Discussion 4.3).
```

**Key parameter values:**
- $\delta_A = 0.2$ (grooming increment per interaction)
- $B_{cap} \in \{50, 100, 200\}$ (default 200)
- $\text{metabolism\_cost}$: basal energy decay per trial
- $\text{groom\_cost}$: energy cost of grooming interaction
- $\alpha_1, \alpha_2, \alpha_3$: initiation probability weights
- $\beta_1, \beta_2, \beta_3$: following probability weights
- $\gamma_1, \gamma_2$: grooming probability weights

---

## S3. Detailed Experimental Protocols

### S3.1 Baseline emergence protocol
- **Agents:** $N = 10$
- **Trials:** $T = 5000$
- **Runs:** 50 independent runs with different random seeds
- **Metrics recorded per trial:** mean energy, mean bond strength, task success rate, grooming frequency, von Neumann entropy, initiator identity, follower count, individual agent energies
- **Block size:** 10 trials (for cross-correlation analysis)
- **Statistical tests:** Bonferroni-corrected significance at $\alpha = 0.001$

### S3.2 Perturbation protocol
- **Perturbation onset:** Trial 2000 (after Phase I-II transition)
- **Resource scarcity:** Energy income $\times 0.4$ for 300 trials (trials 2000-2300)
- **Leader removal:** Permanently remove agent with highest $\bar{A}_l$ at trial 2000
- **Control:** Identical random seed, no perturbation
- **Recovery window:** Trials 2300-5000 (scarcity) or 2000-5000 (removal)
- **Thermodynamic runs:** Separate 4000-trial protocol (2000 warmup + 500 intervention + 1500 recovery) for detailed thermodynamic analysis (Table 2 in main text)

### S3.3 Behavioural intervention protocol (ATE)
- **Runs:** 300 per intervention type
- **Intervention levels:** 10%, 30%, 50% modification of target variable
- **Forward interventions (individual → group):** Modify single agent's energy, bonds, grooming propensity
- **Reverse interventions (group → individual):** Modify environmental parameters (resource availability, grooming cost, task difficulty)
- **Measurement window:** 200 trials post-intervention
- **Effect size:** Cohen's $d = \text{ATE} / s_{\text{pooled}}$
- **Do-operator:** Pearl's do-calculus [15] applied to the structural causal model implied by the four-link chain

### S3.4 Scalability protocol
- **Group sizes tested:** $N \in \{3, 4, 5, 6, 7, 8, 9, 10, 12, 15, 20\}$
- **Runs per size:** 10
- **Trials:** 5000
- **Metrics:** Final energy, CCI, phase boundary timing, $S_{VN}$

### S3.5 Ablation protocol
- **Parameters ablated:** Basal metabolism, following benefit, grooming increment (one at a time)
- **Runs:** 30 per ablation condition
- **Trials:** 3000
- **Essentiality criterion:** Parameter is essential if its removal causes $> 25\%$ reduction in CCI or eliminates the three-phase pattern

### S3.6 Multi-run validation (Eq. 18)
For each run $k \in \{1, ..., 50\}$ and each pathway $(X, Y)$, the detrended cross-correlation at lag $\tau$ is:

$$r^{(k)}_{XY}(\tau) = \text{corr}\left(\tilde{X}^{(k)}(t), \tilde{Y}^{(k)}(t + \tau)\right) \tag{S6}$$

Population statistics: $\bar{r}_{XY}(\tau) = \frac{1}{50}\sum_k r^{(k)}_{XY}(\tau)$, with standard error $\text{SE} = s / \sqrt{50}$.

Significance: A lag $\tau$ is significant if $\bar{r}_{XY}(\tau) > 0$ and the 95% confidence interval excludes zero, after Bonferroni correction for the number of lags tested.

---

## S4. Pressure Component Decomposition

**Figure S4.1** (corresponds to original Fig. 6): Net implicit pressure dynamics across the three phases. Phase I shows high, fluctuating implicit pressure as the system explores coordination strategies. Phase II shows a systematic decline as bonds develop. Phase III stabilises at minimal implicit pressure.

**Figure S4.2** (corresponds to original Fig. 7): Stacked area plot of the four $P_{net}$ components (energy deficit, initiation barrier, consensus cost, grooming barrier) across 5000 trials, with phase boundaries overlaid. The energy deficit component dominates Phase I, transitions to consensus cost dominance in Phase II, and all components are small in Phase III.

---

## S5. Thermodynamic Details

**Figure S5.1** (corresponds to original Fig. 9): $P_{net}$ vs $P_{Landau}$ comparison across 5000 trials. Both decrease monotonically through Phase II but $P_{net}$ peaks during mid-Phase II (consensus cost spike) while $P_{Landau}$ decreases smoothly. This difference reflects $P_{net}$'s sensitivity to short-timescale fluctuations versus the Landau model's thermodynamic averaging.

**Figure S5.2** (corresponds to original Fig. 10): Shannon entropy and von Neumann entropy evolution. Shannon entropy (computed over the degree distribution) tracks $S_{VN}$ qualitatively but misses the fine structure of bond weight distribution that $S_{VN}$ captures. Both show Phase I high-entropy exploration, Phase II entropy collapse, and Phase III stabilisation.

**Figure S5.3** (corresponds to original Fig. 11): Normalised Shannon and von Neumann entropy comparison. Demonstrates that $S_{VN}$ provides greater dynamic range and is more sensitive to bond network topology changes, justifying its use as the primary entropy measure.

**Figure S5.4** (corresponds to original Fig. 12): Pressure-temperature phase space ($P_{Landau}$ vs $T = 1/E$). The system traces a characteristic trajectory from high-$T$, high-$P$ (Phase I) through a critical region (Phase II crossover) to low-$T$, low-$P$ (Phase III). Phase boundaries from the geometric analysis are overlaid, showing consistent identification.

**Figure S5.5** (corresponds to original Fig. 13): Phase diagram showing phase boundaries identified independently by the geometric crossover ($t_{cross} = 1312$), the Landau critical energy ($E^* = 158$), and the $S_{VN}$ transition. The convergence of three independent criteria on consistent boundaries strengthens confidence in the phase identification.

---

## S6. Bond Cap Robustness

**Table S6.1** (corresponds to original Table 3): Key metrics across bond cap values.

| $B_{cap}$ | Phase I→II boundary | Phase II→III boundary | Final $\bar{E}$ | Final $S_{VN}$ | CCI |
|-----------|--------------------|-----------------------|------------------|----------------|-----|
| 50 | ~1100 | ~2800 | ~145 | ~0.12 | Positive |
| 100 | ~1250 | ~3100 | ~155 | ~0.10 | Positive |
| 200 | ~1312 | ~3333 | ~162 | ~0.10 | Positive |

**Figure S6.1** (corresponds to original Fig. 14): Energy staircase and bond dynamics for $B_{cap} \in \{50, 100, 200\}$. All three produce the characteristic three-phase pattern with staircase energy dynamics. Lower $B_{cap}$ accelerates convergence (earlier phase boundaries) but produces lower final performance. The qualitative dynamics are preserved regardless of cap value.

---

## S7. Ablation Study Details

**Table S7.1** (corresponds to original Table 4): Ablation results.

| Parameter removed | $\Delta\bar{E}$ (%) | $\Delta$CCI (%) | Three-phase preserved? | Mechanistic role |
|-------------------|---------------------|-----------------|----------------------|-----------------|
| None (baseline) | — | — | Yes | — |
| Basal metabolism | +45.2 | -78.8 | No (Phase I only) | Drives grooming motivation through energy scarcity |
| Following benefit | +182.0 | -56.2 | No (energy staircase but no hierarchy) | Creates coordination incentive; removal makes agents self-sufficient |
| Grooming increment ($\delta_A$) | -23.1 | -100.0 | No (Phase I only) | Enables bond accumulation (slow timescale) |

The following-benefit ablation produces a paradoxical result: energy performance *increases* dramatically (+182%) because agents become individually self-sufficient, but coordination collapses (CCI -56.2%). This demonstrates that high energy does not imply constraint closure — the causal loop structure is what distinguishes coordinated performance from independent success.

**Figure S7.1** (corresponds to original Fig. 15): Energy staircase and CCI evolution for each ablation condition. The baseline shows the characteristic three-phase staircase with rising CCI. The metabolism ablation eliminates energy pressure, producing flat high energy with no grooming investment. The following-benefit ablation produces rising energy without coordination. The grooming ablation prevents bond formation, trapping the system in Phase I.

---

## S8. Scalability

**Figure S8.1** (corresponds to original Fig. 16): Performance metrics versus group size $N$.

- **$N = 3$-$4$:** Viable but limited — small groups cannot form robust hierarchies. CCI is low.
- **$N = 5$-$10$:** Optimal range. Three-phase pattern emerges reliably. CCI peaks at $N = 7$-$8$.
- **$N = 12$-$15$:** Progressive degradation. Phase II lengthens, final energy decreases, $S_{VN}$ increases (more egalitarian but less coordinated topology). CCI declines below significance threshold.
- **$N = 20$:** Effective breakdown. No clear phase structure. Grooming is too diluted ($< 0.003$ interactions per pair per trial) to build meaningful bonds.

The breakdown at $N \approx 12$ arises from grooming bandwidth saturation: $N(N-1)/2 = 66$ pairwise relationships compete for a fixed number of grooming opportunities per trial. The per-pair grooming rate drops below the threshold needed to offset implicit bond decay effects, preventing the slow timescale from operating effectively.

---

## S9. Thermodynamic Perturbation Analysis

**Figure S9.1** (corresponds to original Fig. 19): Four-panel thermodynamic response to resource scarcity. Panel (a): Energy trajectory with perturbation window shaded. Panel (b): Bond network entropy $S_{VN}$ — remains flat, confirming bond buffering. Panel (c): Geometric pressure components — $P_{explicit}$ spikes, $P_{implicit}$ unaffected. Panel (d): $P_{Landau}$ — remains positive throughout, indicating sustained coordination drive.

**Figure S9.2** (corresponds to original Fig. 20): Extended thermodynamic analysis of resource scarcity, including energy and bond trajectories for individual agents, revealing that all agents are affected approximately equally (no differential impact on leaders vs. followers).

**Figure S9.3** (corresponds to original Fig. 21): Bond matrix snapshots under resource scarcity at three timepoints (pre-perturbation, during perturbation, post-recovery). The hierarchical structure is preserved throughout — same leaders, same followers, same block structure.

**Figure S9.4** (corresponds to original Fig. 22): Four-panel thermodynamic response to leader removal. Panel (a): Energy trajectory — sharper initial decline, slower recovery. Panel (b): $S_{VN}$ — characteristic collapse (0.101→0.085) then overshoot (→0.151). Panel (c): Geometric pressures — $P_{implicit}$ spikes to -6/-7 (structural void detection). Panel (d): $P_{Landau}$ — briefly negative (disorder pressure), then recovers positive.

**Figure S9.5** (corresponds to original Fig. 23): Extended thermodynamic analysis of leader removal, including individual agent energy trajectories. Shows differential impact: the former followers of the removed leader experience the largest energy dips, while agents with pre-existing alternative bonds recover fastest.

**Figure S9.6** (corresponds to original Fig. 24): Bond matrix snapshots under leader removal at three timepoints. Pre-perturbation: clear hierarchical structure with one dominant hub. During recovery: sparse, fragmented bonds. Post-recovery: new, more distributed topology — multiple moderate-strength hubs rather than one dominant hub.

---

## S10. Network Structure and Granger Heatmaps

**Figure S10.1** (corresponds to original Fig. 25): Network structure visualisation at trial 5000 showing the energy poverty trap. Agents are positioned by energy (y-axis) and mean bond strength (x-axis). The main cluster (8 agents) shows high energy and strong bonds. Agents R2 and R6 are isolated in the low-energy, low-bond corner with no connections to the main cluster. Edge thickness indicates bond strength; node size indicates energy.

**Figure S10.2** (corresponds to original Fig. 26): Granger causality heatmap (pairwise $F$-statistics, Bonferroni corrected). Rows and columns are the eight analysis variables (4 individual-level: agent energy, agent bonds, agent grooming, agent success; 4 group-level: mean energy, bond entropy, grooming rate, success rate). Strong Granger-causal links (high $F$) appear along the expected four-link chain. The group→individual $F$-statistics are approximately 870× larger than individual→group, reflecting the scale difference discussed in Section 3.5.

---

## S11. Reverse ATE Curves

**Figure S11.1** (corresponds to original Fig. 28): Reverse ATE dose-response curves for group→individual interventions. Three intervention levels (10%, 30%, 50%) applied to group-level variables (resource availability, grooming opportunity, task difficulty). Raw ATE shows large effects on individual-level outcomes (energy, bonds), but Cohen's $d$ normalisation reveals moderate effect sizes ($d \approx 1.2$-$1.6$), comparable in magnitude to the forward direction.

**Figure S11.2** (corresponds to condensed from Fig. 29): Do-operator analysis for the five significant causal pathways identified in Section 3.5. Bar chart with 95% confidence intervals showing the estimated causal effect under Pearl's do-calculus. Forward pathways (energy→success, bonds→following, grooming→bonds) show positive effects. Reverse pathways (group success→individual energy, bond entropy→grooming) show positive effects of comparable magnitude.

---

## Figure and Table Index

| Supplementary | Corresponds to original | Content |
|--------------|------------------------|---------|
| S1, Figs S1.1-S1.2 | Eqs 1-5, Figs 6-7 | $P_{net}$ component decomposition |
| S2 | Algorithms 1-2 (full) | Complete pseudocode |
| S3 | Section 2.3 (expanded) | Experimental protocols |
| S4, Figs S4.1-S4.2 | Figs 6-7 | Pressure component trajectories |
| S5, Figs S5.1-S5.5 | Figs 9-13 | Thermodynamic details |
| S6, Table S6.1, Fig S6.1 | Table 3, Fig 14 | Bond cap robustness |
| S7, Table S7.1, Fig S7.1 | Table 4, Fig 15 | Ablation study |
| S8, Fig S8.1 | Fig 16 | Scalability analysis |
| S9, Figs S9.1-S9.6 | Figs 19-24 | Perturbation thermodynamics |
| S10, Figs S10.1-S10.2 | Figs 25-26 | Network + Granger |
| S11, Figs S11.1-S11.2 | Figs 28-29 | Reverse ATE + do-operator |
