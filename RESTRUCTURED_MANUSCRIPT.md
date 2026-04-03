# Scaffolding Dynamic Leader-Follower Coordination in Swarm Robotics through Constraint Closure and Multi-Timescale Social Interactions

**Pranay Vaddepalli**$^{1}$, **Fethi Candan**$^{1}$, **Alejandro Jimenez Rodriguez**$^{1,*}$

$^{1}$ Department of Computer Science, University of South Florida, Tampa, FL, USA

$^{*}$ Corresponding author. E-mail: ajimenez3@usf.edu

ORCID: P. Vaddepalli — 0009-0002-5812-8245

---

## Abstract

Multi-agent systems face a fundamental stability-plasticity dilemma: maintaining coherent coordination while adapting to environmental disruptions. Biological social groups solve this through *constraint closure* — circular causal loops where individual actions generate group-level constraints that in turn shape future individual behaviour. Whether such mechanisms can scaffold emergent leader-follower coordination in artificial swarms remains an open question.

We present a three-timescale architecture — fast task execution, medium-rate energy dynamics, and slow bond accumulation — validated across 50 independent simulation runs of 10-agent swarms performing a collective foraging task. A geometric state-space decomposition formalises *explicit* and *implicit* pressure channels with no free parameters, directly operationalising Wilson and Prescott's constraint closure framework. A complementary Landau free energy model connects the observed dynamics to established phase transition theory.

Three principal findings emerge. First, circular causality is confirmed through detrended cross-correlation across a four-link causal chain (Success $\to$ Energy $\to$ Grooming $\to$ Bonds), with multi-run validation (N=50, lag-10 peak $r = 0.153 \pm 0.016$). Second, channel-specific perturbation response constitutes the paper's most novel contribution: resource scarcity targets the explicit pressure channel while bonds remain buffered ($S_{VN}$ stable at $\sim$0.101-0.105), whereas leader removal targets the implicit channel, triggering structural reorganisation ($S_{VN}$: 0.101 $\to$ 0.085 $\to$ 0.151). The system shows minimal degradation under extreme perturbation (0.9-2.3% immediate performance loss), with distinct thermodynamic recovery signatures depending on perturbation type. Third, bidirectional causal symmetry is established through controlled average treatment effect analysis with Cohen's $d$ normalisation, collapsing an apparent 870$\times$ asymmetry in raw Granger statistics to approximate parity: individual behaviour is more *efficient* per unit intervention ($d \approx 1.7$-$2.2$), while the group exerts more *total force* ($d \approx 1.2$-$1.6$). Scalability analysis identifies a coordination limit at 10-12 agents, paralleling grooming bandwidth constraints observed in primate social groups.

---

## 1. Introduction

Catastrophic forgetting — the tendency for learned capabilities to degrade when agents encounter novel situations — remains a central challenge in multi-agent reinforcement learning [1, 2]. When coordination strategies acquired under stable conditions encounter environmental disruptions, performance often collapses irreversibly. This stability-plasticity dilemma demands architectures that preserve functional competence while permitting adaptive reorganisation [3].

Biological social groups offer a compelling solution. Baboon troops, for instance, maintain coherent group coordination despite continuous membership changes, resource fluctuations, and predation events [4, 5]. Their resilience arises not from centralised control but from *constraint closure*: circular causal loops where individual behaviours generate group-level social structures that subsequently constrain individual action [6]. Wilson and Prescott [6] formalised this as a dynamic interplay between *explicit* pressures (environmental demands on individuals) and *implicit* pressures (social structural constraints), arguing that the closure of these causal loops scaffolds emergent group-level competence.

Existing multi-agent coordination approaches address aspects of this challenge. Consensus-based methods [7, 8] provide mathematical convergence guarantees but assume static topologies. Market mechanisms [9] enable flexible task allocation but lack the persistent social memory that supports recovery from disruption. Stigmergic approaches [10, 11] produce emergent coordination through environmental modification but offer limited social complexity. Hierarchical architectures [12, 13] impose leader-follower structures by design but cannot model leadership emergence. None of these approaches implements the circular causal architecture that characterises biological social resilience.

This gap motivates our central research question: *Can constraint closure, implemented through multi-timescale social dynamics, scaffold emergent leader-follower coordination that exhibits biologically realistic resilience to environmental perturbation?*

We make five contributions:

1. A three-timescale simulation architecture with 50-run circular causality validation demonstrating constraint closure through a four-link causal chain.
2. A geometric pressure decomposition with no free parameters that operationalises Wilson and Prescott's explicit/implicit pressure framework, complemented by a Landau free energy model connecting to phase transition theory.
3. Channel-specific perturbation response: perturbations targeting different pressure channels produce distinct thermodynamic recovery signatures, providing a diagnostic framework for predicting system resilience.
4. Bidirectional causal symmetry between individual and group levels, demonstrated through Cohen's $d$-normalised average treatment effects that collapse a raw 870$\times$ Granger asymmetry.
5. Scalability limits at 10-12 agents confirmed through systematic group size variation, with ablation studies identifying three essential parameters with distinct mechanistic roles.

The remainder of this paper is organised as follows. Section 2 presents the theoretical framework, simulation architecture, and experimental design. Section 3 reports results across circular causality validation, phase transitions, perturbation response, and causal analysis. Section 4 discusses implications and limitations. Section 5 concludes.

---

## 2. Methods

### 2.1 Theoretical Framework

#### Geometric state-space decomposition

We formalise Wilson and Prescott's [6] pressure concept through a geometric decomposition of the system's state space. The collective state vector $\mathbf{x}(t) = [E(t), B(t), S(t), G(t)]$ captures mean energy $E$, mean bond strength $B$, task success rate $S$, and grooming frequency $G$ at trial $t$.

The total system pressure $P_{total}$ is decomposed into explicit and implicit components:

$$P_{total}(t) = P_{explicit}(t) + P_{implicit}(t) \tag{6}$$

**Explicit pressure** captures environmental demands that individuals experience directly — resource scarcity, task difficulty, and energy costs:

$$P_{explicit}(t) = \left\| \frac{d\mathbf{x}_{env}}{dt} \right\| \cdot \frac{1}{E(t)} \tag{7}$$

where $\mathbf{x}_{env} = [E(t), S(t)]$ is the environmentally determined subspace and the energy denominator captures the system's diminishing capacity to absorb further environmental demands as resources deplete.

**Implicit pressure** captures constraints arising from social structure — bond asymmetries, coordination overhead, and emergent role differentiation:

$$P_{implicit}(t) = \left\| \frac{d\mathbf{x}_{soc}}{dt} \right\| \cdot \left(1 - \frac{B(t)}{B_{cap}}\right) \tag{8}$$

where $\mathbf{x}_{soc} = [B(t), G(t)]$ is the socially determined subspace and the bond capacity term captures how developed social structures buffer against implicit pressure.

The critical insight is the **pressure crossover**: in early dynamics (Phase I), $P_{explicit} > P_{implicit}$ as environmental demands dominate; as bonds develop, implicit pressure rises while explicit pressure decreases, crossing at time $t_{cross}$ which we identify as the phase boundary:

$$t_{cross}: \quad P_{explicit}(t_{cross}) = P_{implicit}(t_{cross}) \tag{9}$$

This decomposition introduces no free parameters — every term is computed directly from simulation observables and the bond capacity parameter $B_{cap}$.

#### Landau free energy model

To connect the observed phase transitions to established thermodynamic theory, we construct a Landau free energy model. We map the bond order parameter $\phi \equiv B/B_{cap}$ (normalised mean bond strength) and the system temperature $T \equiv 1/E$ (inverse mean energy, capturing fluctuation intensity) to a free energy functional:

$$F(\phi, T) = a\phi^2 + bT\phi^4 \tag{12}$$

with $a = -0.5$ (favouring the ordered state) and $b = 10^{-6}$ (providing higher-order stabilisation). These coefficients are chosen to capture qualitative phase transition behaviour; they are not fitted to data but selected to place the model in the correct symmetry regime — negative $a$ ensures the system favours bond formation, while the $T$-dependent quartic term provides a restoring force whose strength scales with thermal fluctuations.

The Landau pressure is derived as:

$$P_{Landau} = -\frac{\partial F}{\partial \phi} = -2a\phi - 4bT\phi^3 \tag{13}$$

When $P_{Landau} > 0$, the system is driven toward higher bond order (coordination); when $P_{Landau} < 0$, the system experiences pressure toward disorder.

The three pressure frameworks relate as follows. $P_{net}$ (see Supplementary Material S1) quantifies constraint difficulty at the individual agent level through four mechanistic components. The geometric decomposition (Eqs. 6-9) formalises Wilson and Prescott's pressure concept at the system level with no free parameters, serving as the primary analytical tool. The Landau model provides thermodynamic predictions connecting to established phase transition theory [16], particularly valuable as a diagnostic during perturbation analysis where the sign of $P_{Landau}$ distinguishes qualitatively different recovery regimes.

#### Bond network entropy

To characterise the structural complexity of the bond network, we employ the von Neumann entropy of the normalised bond matrix:

$$S_{VN} = -\sum_i \mu_i \ln \mu_i \tag{10}$$

where $\mu_i$ are the eigenvalues of the symmetric bond matrix $\mathbf{A}(t)$ normalised by $\text{tr}(\mathbf{A})$, using only positive eigenvalues for the entropy calculation. Higher $S_{VN}$ indicates a more distributed, egalitarian bond topology; lower $S_{VN}$ indicates concentrated, hierarchical structure.

### 2.2 Simulation Architecture

The simulation models $N$ agents (default $N = 10$) performing a collective foraging task over $T = 5000$ trials. Each trial consists of five phases: initiator selection, follower recruitment, task execution, grooming, and energy dynamics.

**Figure 1** illustrates the task schematic. An *initiator* $l$ is selected uniformly at random from active agents (those with energy $E_i > 0$). We distinguish *initiation* (random focal agent selection) from *leadership* (emergent influence from accumulated bonds): any agent may be selected as initiator, but only those with strong bonds successfully recruit followers.

The initiator decides whether to attempt group coordination with probability:

$$P_{init}(l) = \sigma\left(\alpha_1 \cdot \frac{E_l}{E_{max}} + \alpha_2 \cdot \frac{\bar{A}_l}{A_{max}} + \alpha_3 \cdot r_{success,l}\right) \tag{15}$$

where $\sigma$ is the sigmoid function, $E_l$ is the initiator's energy, $\bar{A}_l$ is their mean bond strength, $r_{success,l}$ is their recent success rate, and $\alpha_1, \alpha_2, \alpha_3$ are weighting coefficients.

Each potential follower $j$ decides whether to follow initiator $l$ based on:

$$P_{follow}(j, l) = \sigma\left(\beta_1 \cdot \frac{A_{jl}}{A_{max}} + \beta_2 \cdot \frac{E_j}{E_{max}} + \beta_3 \cdot (1 - r_{recent,j})\right) \tag{16}$$

where $A_{jl}$ is the bond strength between $j$ and $l$, $E_j$ is the follower's energy, $r_{recent,j}$ is their recent success rate, and $\beta_1, \beta_2, \beta_3$ are weighting coefficients.

Task success depends on group size, with a minimum threshold for coordination benefit. Successful group coordination yields energy rewards shared among participants. After task completion, agents engage in grooming — a bond-strengthening interaction:

$$A_{ij}(t+1) = \begin{cases} \min(B_{cap}, \; A_{ij}(t) + \delta_A) & \text{if grooming}(i,j) \text{ at trial } t \\ A_{ij}(t) & \text{otherwise} \end{cases} \tag{17}$$

where $B_{cap}$ is the bond capacity (default 200) and $\delta_A = 0.2$ is the grooming increment. Energy decays by a basal metabolism cost each trial. Full algorithmic details, including the grooming decision process and energy dynamics, are provided in Supplementary Material S2.

### 2.3 Experimental Design

Five experimental protocols probe different aspects of the system:

**Baseline emergence** ($N = 50$ runs): 10 agents, 5000 trials, random seed variation. Establishes the robustness of the three-phase emergence pattern and provides the multi-run validation of circular causality.

**Perturbation experiments**: Two perturbation types applied at trial 2000 (after Phase I emergence). (i) *Resource scarcity*: energy income reduced by 60% for 300 trials (trials 2000-2300), targeting the explicit pressure channel. (ii) *Leader removal*: the agent with the highest mean bond strength is permanently removed at trial 2000, targeting the implicit pressure channel. Each perturbation type runs with a matched control (identical seed, no perturbation).

**Behavioural intervention** ($N = 300$ runs): Controlled ATE experiments with graded perturbation magnitudes (10%, 30%, 50% interventions on individual-level and group-level variables) to establish causal direction and effect sizes. Analysis uses both forward (individual $\to$ group) and reverse (group $\to$ individual) interventions.

Scalability, ablation, and multi-run validation protocols are detailed in Supplementary Material S3.

### 2.4 Analysis Methods

**Circular causality measurement.** We assess the four-link causal chain (Success $\to$ Energy $\to$ Grooming $\to$ Bonds) using detrended block cross-correlation on 10-trial blocks. For each pair of variables $(X, Y)$, we compute:

$$r_{XY}(\tau) = \text{corr}\left(\tilde{X}(t), \tilde{Y}(t + \tau)\right) \tag{18}$$

where $\tilde{X}$ denotes the detrended series and $\tau$ is the lag in blocks. Significance is assessed against a null distribution from 1000 phase-shuffled surrogates.

**Circular Causality Index (CCI).** CCI quantifies the strength of the complete causal loop. We define CCI using the three pathways that operate on the block timescale (Success $\to$ Energy, Energy $\to$ Grooming, Grooming $\to$ Bonds); the fourth pathway (Bonds $\to$ Success) is validated separately through ATE analysis (Section 3.5) because it operates on a slower timescale not captured by 10-trial block cross-correlation. CCI is defined as:

$$\text{CCI} = \min\left(r^*_{S \to E}, \; r^*_{E \to G}, \; r^*_{G \to B}\right)$$

where $r^*_{X \to Y}$ denotes the peak significant cross-correlation for each pathway.

**Average treatment effect (ATE).** We employ controlled intervention experiments with graded perturbation magnitudes. For each intervention level $\delta$ on variable $X$, we measure the treatment effect on outcome $Y$:

$$\text{ATE}(X \to Y, \delta) = \mathbb{E}[Y | do(X + \delta)] - \mathbb{E}[Y | do(X)]$$

Effect sizes are reported as Cohen's $d = \text{ATE}/s_{\text{pooled}}$, which normalises by the variability of the outcome variable. This normalisation is critical: raw ATE values conflate the intervention's causal strength with the outcome's natural scale, producing misleading asymmetries between individual-level (small scale) and group-level (large scale) variables.

**Granger causality** provides a complementary time-series test for predictive information flow between variable pairs [14]. We apply pairwise Granger $F$-tests with lag orders 1-30 and Bonferroni correction.

---

## 3. Results

### 3.1 Circular Causality Validates Constraint Closure

**Figure 2** presents evidence for the four-link causal chain underlying constraint closure. Single-run cross-correlations (Fig. 2, top) reveal a temporal cascade:

$$\text{Success} \xrightarrow{\tau \approx 3} \text{Energy} \xrightarrow{\tau \approx 5} \text{Grooming} \xrightarrow{\tau \approx 10} \text{Bonds} \tag{19}$$

where $\tau$ denotes lag in 10-trial blocks. The ordering of lag peaks is consistent with the causal model: task success drives energy accumulation (fast), energy surplus enables grooming investment (medium), and grooming gradually strengthens bonds (slow). This three-timescale cascade reflects the architecture's design: fast task dynamics, medium-rate energy dynamics, and slow bond accumulation.

Multi-run validation across $N = 50$ independent runs (Fig. 2, bottom) confirms the robustness of this pattern. The Grooming $\to$ Bonds pathway shows the strongest and most consistent signal (lag-10 peak $r = 0.153 \pm 0.016$, $p < 0.001$ in 48/50 runs). The Success $\to$ Energy and Energy $\to$ Grooming pathways show peaks at lags 3 and 5 respectively, both significant in $> 45/50$ runs.

The Bonds $\to$ Success pathway — closing the causal loop — shows no significant cross-correlation at any lag in the 10-trial block analysis. This null result is theoretically informative rather than problematic: the bond-to-success mechanism operates through leadership crystallisation, a slow structural effect where accumulated bonds make certain initiators more effective at recruiting followers. This process unfolds over hundreds of trials, well beyond the 30-block (300-trial) cross-correlation window. The ATE evidence in Section 3.5 directly confirms the Bonds $\to$ Success link through controlled intervention.

### 3.2 Three-Phase Emergence and Pressure Formalisation

The system's dynamics unfold across three distinct phases, identified through the geometric pressure decomposition (Fig. 3).

**Phase I (Exploration, trials 0-$t_{cross}$):** Explicit pressure dominates ($P_{explicit} > P_{implicit}$). Agents explore the task space with low energy and weak bonds. The energy staircase shows flat or slowly increasing performance ($\bar{E} \approx 80$-$120$).

**Phase II (Transition, trials $t_{cross}$-$t_{stable}$):** The pressure crossover occurs at $t_{cross} \approx 1312$ trials, where $P_{explicit} = P_{implicit}$ (Fig. 3, top panel). Beyond this point, implicit pressure dominates: the developing social structure creates coordination constraints (role differentiation, grooming obligations) that outweigh direct environmental demands. Energy rises steeply through a characteristic staircase pattern ($\bar{E} \approx 120$-$155$).

**Phase III (Stable coordination, trials $t_{stable}$-5000):** Both pressure components stabilise at low levels ($t_{stable} \approx 3333$). Energy plateaus ($\bar{E} \approx 155$-$165$) with low variance. The system has achieved constraint closure: the social structure is self-maintaining.

**Figure 3** (bottom panel) shows the energy staircase with phase boundaries overlaid. The staircase structure — discrete jumps in mean energy separated by plateaus — is characteristic of self-organising systems approaching criticality. Each step corresponds to the successful integration of an additional agent into the coordination structure.

**Figure 4** shows the bond matrix evolution across the three phases. In Phase I (trials 0-1000), bonds are sparse and approximately uniform. By Phase II (trials 1500-3000), a hierarchical structure emerges: 2-3 agents develop strong bonds with multiple partners, forming proto-leaders. In Phase III (trials 3500-5000), the bond matrix shows clear block structure with stable leader-follower relationships.

Thermodynamic quantities characterise each phase. Phase I exhibits high entropy ($S_{VN} \approx 0.15$-$0.18$), indicating a distributed, uncommitted social structure. Phase II shows entropy collapse ($S_{VN} \approx 0.10$-$0.12$) as the hierarchical structure crystallises. Phase III stabilises at $S_{VN} \approx 0.10$, reflecting a mature, asymmetric bond topology.

The Landau model provides complementary validation. The Landau pressure $P_{Landau}$ is positive throughout Phase II (driving bond formation) and approaches zero in Phase III (equilibrium). The Pearson correlation between $P_{Landau}$ predictions and observed dynamics is $r = 0.54$, reflecting the model's qualitative rather than quantitative character. The value of $P_{Landau}$ as a diagnostic tool becomes evident in the perturbation analysis (Section 3.4), where its sign distinguishes qualitatively different recovery mechanisms.

### 3.3 System Robustness

**Scalability.** Systematic variation of group size from 3 to 20 agents reveals viable coordination for 5-10 agents, with progressive degradation above 10 and effective breakdown at 12-15 agents. The breakdown arises from grooming bandwidth saturation: the number of pairwise relationships grows as $N(N-1)/2$ while grooming opportunities per trial remain approximately constant, producing a per-pair grooming rate that scales as $\sim 1/N^2$. This bandwidth constraint is suggestive of — though not equivalent to — the subgroup size limitations observed in primate social systems [17]. Full scalability results are in Supplementary Material S8.

**Ablation.** Systematic removal of individual parameters identifies three as essential for constraint closure (Table 4, Supplementary Material S7): (i) *basal metabolism* (energy decay per trial): removal eliminates the energy cost that drives grooming motivation, collapsing CCI by 78.8%; (ii) *following benefit* (energy bonus for successful following): removal paradoxically increases the energy staircase by 182% (individual agents become self-sufficient) but collapses CCI by 56.2%, demonstrating that high performance without coordination is possible but does not constitute constraint closure; (iii) *grooming increment* ($\delta_A$): removal prevents bond accumulation, eliminating the slow timescale entirely. Each parameter plays a mechanistically distinct role, and no subset of two suffices to maintain the three-phase pattern.

**Bond cap robustness.** The three-phase pattern and phase boundaries are preserved across $B_{cap} \in \{50, 100, 200\}$, confirming that the dynamics arise from the architecture rather than parameter tuning (Supplementary Material S6).

### 3.4 Channel-Specific Perturbation Response

This section presents the paper's most novel finding: perturbations targeting different pressure channels produce qualitatively distinct thermodynamic recovery signatures.

#### Resource scarcity (explicit channel)

**Figure 5** shows the system response to resource scarcity (60% energy reduction, trials 2000-2300). The intervention targets the explicit pressure channel directly — environmental resource availability.

**Immediate impact:** Performance drops by 0.9% relative to the pre-perturbation baseline. The small magnitude reflects bonds buffering the system: even under reduced resources, the established coordination structure enables continued (though less rewarding) group foraging.

**Recovery trajectory:** Energy recovers to pre-perturbation levels within $\sim$400 trials of perturbation offset. Comparing perturbed versus matched control at trials 3000-4000 (Table 2): perturbed $\bar{E} = 160.8$ versus control $\bar{E} = 158.1$, a difference of +1.7%. This modest advantage, if real, likely reflects the system having been pushed through a slightly different dynamical trajectory rather than genuine performance enhancement.

**Thermodynamic signature:** The bond network is *buffered*. Von Neumann entropy remains stable ($S_{VN} \approx 0.101$-$0.105$) throughout the perturbation, indicating that the social structure absorbs the shock without reorganisation. The Landau pressure remains positive ($P_{Landau} = +16.3$), confirming sustained drive toward coordination. Explicit pressure spikes transiently but implicit pressure is unaffected.

#### Leader removal (implicit channel)

**Figure 6** shows the response to permanent removal of the highest-bonded agent at trial 2000. This targets the implicit pressure channel — the social structure itself.

**Immediate impact:** Performance drops by 2.3%, more than twice the scarcity effect, reflecting the loss of a coordination hub.

**Recovery trajectory:** Recovery is slower and qualitatively different from scarcity. At trials 3000-4000 (Table 2): perturbed $\bar{E} = 138.5$ versus control $\bar{E} = 158.1$, a difference of -12.4%. The system has not yet recovered to control levels at this timepoint, though Figure 6 suggests convergence by trial 4500-5000.

**Thermodynamic signature:** The bond network *reorganises*. $S_{VN}$ drops initially (0.101 $\to$ 0.085) as surviving bonds contract around the remaining agents, then overshoots to 0.151 as new, more distributed bonds form — a more egalitarian topology than the original hierarchical structure. The geometric implicit pressure spikes dramatically ($P_{implicit}$ reaching $-6$ to $-7$) as the system detects the structural void. $P_{Landau}$ briefly becomes negative, indicating thermodynamic pressure toward disorder before the system reorganises.

#### Channel specificity as diagnostic framework

| Metric | Scarcity (explicit) | Leader removal (implicit) |
|--------|-------------------|--------------------------|
| Immediate degradation | 0.9% | 2.3% |
| $\bar{E}$ vs control (t=3000-4000) | +1.7% | -12.4% |
| $S_{VN}$ trajectory | Stable (0.101-0.105) | Collapse→overshoot (0.101→0.085→0.151) |
| $P_{Landau}$ | Positive (+16.3) | Transient negative |
| $P_{implicit}$ spike | None | -6 to -7 |
| Recovery mechanism | Bond buffering | Structural reorganisation |

**Table 2.** Thermodynamic quantities across perturbation types, with matched control comparison.

The channel-specificity of the perturbation response provides a principled diagnostic framework: monitoring explicit and implicit pressure channels separately predicts not only *whether* the system can recover but *how* — through buffering (explicit perturbation) or reorganisation (implicit perturbation). Detailed thermodynamic time series are in Supplementary Material S9.

### 3.5 Bidirectional Causal Symmetry

The three-method causal triangulation (Granger, ATE, do-operator) reveals a striking methodological finding: the apparent direction of causal dominance depends on the measurement framework.

**Raw Granger statistics** suggest massive asymmetry: group $\to$ individual $F$-statistics are approximately 870$\times$ larger than individual $\to$ group. This would imply near-complete top-down determination.

**Cohen's $d$ normalisation** collapses this asymmetry to approximate parity. The raw asymmetry arises because individual-level variables (single agent energy, bonds) have much smaller natural scales than group-level variables (mean energy, bond entropy). When the treatment effect is normalised by the outcome's standard deviation, the causal influence is approximately symmetric.

**Figure 7** presents the forward ATE dose-response curves. Key findings across intervention levels (10%, 30%, 50%):

- *Forward (individual $\to$ group)*: Cohen's $d \approx 1.7$-$2.2$. Individual-level interventions produce outsized per-unit effects on group-level outcomes. Small changes in one agent's energy or bonding behaviour propagate efficiently through the coordination structure.
- *Reverse (group $\to$ individual)*: Cohen's $d \approx 1.2$-$1.6$. Group-level interventions (modifying shared environment, grooming opportunities) produce moderate per-unit effects on individuals but operate on all agents simultaneously, generating more total causal force.

The synthesis across methods (Table 3) resolves the apparent contradiction:

| Method | Forward ($I \to G$) | Reverse ($G \to I$) | Apparent direction |
|--------|-------------------|--------------------|--------------------|
| Granger $F$-test | 1$\times$ | 870$\times$ | Group dominates |
| ATE (raw) | Small | Large | Group dominates |
| ATE (Cohen's $d$) | 1.7-2.2 | 1.2-1.6 | Symmetric |
| Do-operator | Moderate | Moderate | Symmetric |

**Table 3.** Causal direction synthesis across three methods. The Granger and raw ATE asymmetry reflects scale differences between individual and group variables, not genuine causal asymmetry.

**Figure 8** presents the causal synthesis, combining Table 3 with key do-operator results. The do-calculus analysis confirms five pathways with significant causal effects: three forward (energy $\to$ task success, bonds $\to$ following rate, grooming $\to$ bond strength) and two reverse (group success rate $\to$ individual energy, bond entropy $\to$ individual grooming propensity).

The practical implication is that individual behaviour is more *efficient* per unit of intervention while the group exerts more *total force* through simultaneous action on all members. This bidirectional symmetry is consistent with biological social systems, where neither top-down nor bottom-up causation dominates [6, 18].

An illustrative failure mode confirms the bidirectional structure: two agents (R2 and R6) become trapped in an energy poverty cycle, where low energy prevents grooming, weak bonds prevent effective following, and failed coordination further depletes energy. This bidirectional feedback failure — in which neither the individual-level nor group-level escape route is accessible — demonstrates that constraint closure, while generally beneficial, can lock agents into pathological attractors (Supplementary Material S10).

---

## 4. Discussion

### 4.1 Emergent Coordination Through Multi-Timescale Scaffolding

The three-timescale architecture produces coordination through a mechanism distinct from existing approaches. Unlike consensus methods that converge to equilibrium [7, 8], the system exhibits *staircase dynamics* — discrete qualitative transitions between coordination regimes. Unlike market mechanisms [9], coordination persists through disruption because bond-based social memory provides structural inertia. Unlike stigmergic approaches [10, 11], the coordination substrate is internal (bonds between agents) rather than external (environmental modification).

The constraint closure architecture offers a natural solution to the stability-plasticity dilemma in continual learning [3]. The differential timescales of the three dynamics create a separation of concerns: fast task execution explores the performance landscape, medium-rate energy dynamics provide the motivational gradient for social investment, and slow bond accumulation creates structural memory that persists through perturbation. Stability emerges from the slow timescale (bonds change slowly, preserving coordination structure), while plasticity is maintained at the fast timescale (task strategies adapt immediately to changed conditions). The bond cap $B_{cap}$ provides a natural ceiling that prevents over-commitment while preserving the three-phase structure, as confirmed by robustness across $B_{cap} \in \{50, 100, 200\}$.

### 4.2 Channel-Specific Resilience as Design Principle

The most significant finding is the qualitative difference in recovery mechanisms depending on which pressure channel is perturbed. This channel specificity has not, to our knowledge, been reported in the multi-agent coordination literature.

When perturbation targets the explicit channel (resource scarcity), the bond network buffers the impact: social structure absorbs environmental fluctuation without reorganisation ($S_{VN}$ unchanged). The system degrades minimally (0.9%) and recovers quickly, with performance within 1.7% of matched controls after recovery.

When perturbation targets the implicit channel (leader removal), the bond network itself must reorganise. The characteristic $S_{VN}$ overshoot (0.101 $\to$ 0.085 $\to$ 0.151) indicates that the system does not simply rebuild the original hierarchy — it transitions to a more distributed topology. This reorganisation is slower (performance -12.4% versus control at trials 3000-4000) but may ultimately produce a more resilient structure.

Previous work reported this recovery pattern as "paradoxical enhancement" (27.8-42.0% performance increase post-crisis). However, this comparison used the pre-perturbation baseline at trial 2000 as the reference — a timepoint at which the system is still in Phase II transition and has not yet reached its natural plateau. Proper comparison against matched controls reveals that the enhancement is mostly the system continuing its normal emergence trajectory. The genuine finding — channel-specific resilience with distinct thermodynamic signatures — is both more honest and more scientifically useful than the enhancement claim, as it provides a predictive diagnostic framework.

The $S_{VN}$ overshoot after leader removal deserves particular attention. The post-recovery entropy (0.151) exceeds the pre-perturbation value (0.101), indicating structural reorganisation to a more distributed topology. Whether this represents genuine adaptive reorganisation or merely transient fluctuation requires longer simulation runs and multi-run confirmation.

### 4.3 Limitations and Future Work

Several limitations constrain the current findings. The architecture is validated on a single task type (collective foraging); transfer to more complex task domains remains untested. The perturbation results come from single-seed comparisons for the 5000-trial runs, though the 50-run baseline analysis confirms the robustness of the underlying emergence pattern. The enhancement claim, while reframed, requires multi-run perturbation experiments with proper control matching at each timepoint.

The energy poverty trap affecting 2 of 10 agents (20%) represents a significant design limitation. Once trapped in the low-energy, low-bond attractor, these agents cannot re-enter the coordination structure. Future work could explore bond decay mechanisms that allow trapped agents to eventually re-engage, or minimum-energy thresholds that prevent complete exclusion.

Scalability beyond 10-12 agents requires hierarchical extensions — nested groups with inter-group bonds — which would introduce a fourth timescale. The current architecture's quadratic grooming cost ($N(N-1)/2$ pairwise relationships) is the fundamental bottleneck.

The simulation-to-reality gap remains unaddressed. Physical robot swarms face communication constraints, sensing noise, and actuator limitations not present in our idealised simulation. Validating these findings on physical platforms is a critical next step.

### 4.4 Biological Parallels and Practical Implications

The 10-12 agent scalability limit parallels subgroup constraints observed in primate social groups [17], though we emphasise that the mechanism (grooming bandwidth saturation) rather than the specific number is the meaningful parallel. The bidirectional causal symmetry is consistent with Seyfarth and Cheney's [5] observation that primate social structure both shapes and is shaped by individual behaviour.

For multi-agent system designers, three actionable principles emerge: (i) *multi-timescale architecture*: separating fast task execution from slow social memory creation provides inherent stability-plasticity balance; (ii) *bond-based memory*: persistent pairwise interaction records outperform stateless coordination for disruption resilience; (iii) *pressure channel monitoring*: tracking explicit and implicit pressure separately enables prediction of system-specific vulnerabilities and appropriate intervention strategies.

---

## 5. Conclusions

This work demonstrates that constraint closure — circular causal loops between individual behaviour and group social structure — can scaffold emergent leader-follower coordination in simulated swarms, producing biologically realistic resilience to environmental perturbation.

Five principal findings emerge:

1. **Circular causality** is confirmed through a four-link causal chain with consistent temporal ordering across 50 independent runs, with the slow Bonds $\to$ Success link validated through controlled intervention experiments.

2. **Geometric pressure decomposition** with no free parameters operationalises Wilson and Prescott's explicit/implicit pressure framework, identifying phase boundaries ($t_{cross} \approx 1312$, $t_{stable} \approx 3333$) consistent with a complementary Landau free energy model.

3. **Channel-specific perturbation response** constitutes the paper's most novel contribution: explicit-channel perturbation (resource scarcity) is buffered by bonds, while implicit-channel perturbation (leader removal) triggers structural reorganisation, with distinct thermodynamic signatures ($S_{VN}$ stability versus collapse-overshoot) providing a diagnostic framework.

4. **Bidirectional causal symmetry** is established through Cohen's $d$-normalised ATE, collapsing a 870$\times$ raw Granger asymmetry to approximate parity: individual behaviour is more efficient per unit ($d \approx 1.7$-$2.2$) while the group exerts more total force ($d \approx 1.2$-$1.6$).

5. **Scalability** is viable for 5-10 agents, with breakdown at 12-15 driven by grooming bandwidth saturation. Ablation confirms three essential parameters with distinct mechanistic roles.

The critical next steps are multi-run perturbation experiments with proper control comparisons, hierarchical extension beyond the 10-12 agent limit, and physical robot validation. The channel-specific resilience framework may find application beyond swarm robotics, wherever multi-agent coordination must persist through environmental disruption.

---

## References

[1] McCloskey M and Cohen NJ 1989 Catastrophic interference in connectionist networks: The sequential learning problem *Psychology of Learning and Motivation* **24** 109-165

[2] Kirkpatrick J et al 2017 Overcoming catastrophic forgetting in neural networks *PNAS* **114** 3521-3526

[3] Parisi GI et al 2019 Continual lifelong learning with neural networks: A review *Neural Networks* **113** 54-71

[4] King AJ et al 2009 The origins and evolution of leadership *Current Biology* **19** R911-R916

[5] Seyfarth RM and Cheney DL 2012 The evolutionary origins of friendship *Annual Review of Psychology* **63** 153-177

[6] Wilson SP and Prescott TJ 2022 Scaffolding layered control architectures through constraint closure *Philosophical Transactions of the Royal Society B* **377** 20210361

[7] Olfati-Saber R and Murray RM 2004 Consensus problems in networks of agents *IEEE Trans. Automatic Control* **49** 1520-1533

[8] Ren W and Beard RW 2005 Consensus seeking in multiagent systems under dynamically changing interaction topologies *IEEE Trans. Automatic Control* **50** 655-661

[9] Dias MB et al 2006 Market-based multirobot coordination: A survey and analysis *Proceedings of the IEEE* **94** 1257-1270

[10] Dorigo M et al 2004 Ant algorithms and stigmergy *Future Generation Computer Systems* **16** 851-871

[11] Theraulaz G and Bonabeau E 1999 A brief history of stigmergy *Artificial Life* **5** 97-116

[12] Werger BB and Matarić MJ 2000 Broadcast of local eligibility for multi-target observation *Distributed Autonomous Robotic Systems* **4** 347-356

[13] Chen J et al 2020 A review of multi-robot coordination in swarm robotics *IEEE Access* **8** 188610-188630

[14] Granger CWJ 1969 Investigating causal relations by econometric models and cross-spectral methods *Econometrica* **37** 424-438

[15] Pearl J 2009 *Causality: Models, Reasoning, and Inference* 2nd edn (Cambridge University Press)

[16] Landau LD and Lifshitz EM 1980 *Statistical Physics* 3rd edn (Pergamon Press)

[17] Dunbar RIM 1992 Neocortex size as a constraint on group size in primates *Journal of Human Evolution* **22** 469-493

[18] Flack JC 2012 Multiple time-scales and the developmental dynamics of social systems *Philosophical Transactions of the Royal Society B* **367** 1802-1810

[19] Trianni V et al 2011 Fundamental collective behaviors in robotic swarms *Swarm Intelligence* **5** 255-289

[20] Brambilla M et al 2013 Swarm robotics: A review from the swarm engineering perspective *Swarm Intelligence* **7** 1-41

[21] Camazine S et al 2001 *Self-Organization in Biological Systems* (Princeton University Press)

[22] Couzin ID et al 2005 Effective leadership and decision-making in animal groups on the move *Nature* **433** 513-516

[23] Strandburg-Peshkin A et al 2015 Shared decision-making drives collective movement in wild baboons *Science* **348** 1358-1361

[24] Cavagna A et al 2010 Scale-free correlations in starling flocks *PNAS* **107** 11865-11870

[25] Nagy M et al 2010 Hierarchical group dynamics in pigeon flocks *Nature* **464** 890-893

[26] Jolles JW et al 2020 The role of social attraction and its link with boldness in the collective movements of three-spined sticklebacks *Animal Behaviour* **167** 155-167

[27] Harcourt JL et al 2009 Social feedback and the emergence of leaders and followers *Current Biology* **19** 248-252

[28] Herbert-Read JE et al 2011 Inferring the rules of interaction of shoaling fish *PNAS* **108** 18726-18731

[29] Hand S and Watson RA 2025 Emergence and persistence of diversity in complex networks *Evolutionary Computation* **33** 1-28

[30] Chan KY and Kanso E 2026 Collective coordination through passive hydrodynamic interactions *Physical Review Letters* **136** 028101

[31] Brenes-Torres JC et al 2024 Biologically inspired leader-follower coordination in multi-robot systems *Robotics and Autonomous Systems* **172** 104589

[32] Sivaraman A et al 2025 Adaptive social learning in multi-agent systems: A meta-reinforcement learning approach *Autonomous Agents and Multi-Agent Systems* **39** 15

[33] Wilson EO 2000 *Sociobiology: The New Synthesis* 25th Anniversary edn (Harvard University Press)

[34] de Waal FBM 2007 *Chimpanzee Politics: Power and Sex Among Apes* (Johns Hopkins University Press)

[35] Bonabeau E et al 1999 *Swarm Intelligence: From Natural to Artificial Systems* (Oxford University Press)
