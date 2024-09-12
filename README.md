# Computer Architecture Simulation Best Practices

This document is an attempt to collect knowledge of computer architecture simulation practices that can be used for program analysis, hardware design, and next-generation development of computer systems.

One of the difficulties in computer architecture simulation is the need for speeding up the [very long simulation times][sabu2022lcddfma]. Over the past few decades, researchers and practitioners have used a myriad of techniques to accomplish this goal. But, rarely do we document both the successes and limitations of such techniques. In many cases, some techniques can be applicable to some workload types, and some might not be. In addition, there can be some methodologies that perform better for specific classes of applications. It is this set of trade-offs that we aim to present in this document, in addition to links to complete tutorials and use cases. We hope that as these methodologies improve over time, we can continue to augment this document so that it can become a way to bring the collective knowledge of years of computer architecture research to a single place.

<!-- TOC start -->

- [Computer Architecture Overview](#computer-architecture-overview)
   * [Computer Architecture Simulation](#computer-architecture-simulation)
   * [Computer Architecture Simulators](#computer-architecture-simulators)
- [Computer Architecture Simulation Methodologies](#computer-architecture-simulation-methodologies)
   * [Synthetic Workload Generation](#synthetic-workload-generation)
   * [Workload Sampling](#workload-sampling)

<!-- TOC end -->

## Computer Architecture Overview

Computer architecture at a high level can be described as "the internal organization of a computer in an abstract way; that is, it defines the capabilities of the computer and its programming model." [[1]][clements1992tpoch] It also includes the [Instruction Set Architecture (ISA)][wikipedia_isa] design, microarchitecture design, and logic design and implementation [[2]][hennessy2011caaqa], [[3]][wikipedia_comp_arch].

### Computer Architecture Simulation

The [timing simulation][wikipedia_timing_simulation] next-generation computer sytems allows us to understand the performance of a new architecture that exists, or has yet to be built. This includes the timing of critical components, and tends to be at the cycle-level, but does not need to simulate [down to the transistor or logic layers][wikipedia_circuit_design] (although, using [logic synthesis][wikipedia_logic_synthesis] and layout design flows can help to provide more accurate energy, power, and area analysis). Timing simulation is different from [architecture (or ISA) emulation][wikipedia_emulation]. Emulating a computer system typically refers to being able to functionally model an ISA of that system, which can be done with popular emulators like [QEMU][qemu].

### Computer Architecture Simulators

There are a large number of computer architecture simulators in use today. Below is a non-exhaustive list of some recent simulators.

* CPU Simulators
    * [gem5][sim_gem5]
    * [SimFlex][sim_simflex]
    * [Sniper][sim_sniper]
* GPU Simulators
    * [Accel-Sim][sim_accelsim]
    * [MGPUSim][sim_mgpusim]

## Computer Architecture Simulation Methodologies

Running modern workloads like the reference (large) inputs of the [SPEC CPU2017 benchmarks][speccpu2017] or the [Cloudsuite datacenter benchmark][cloudsuite] suites [can take years to simulate to completion][sabu2022lcddfma] due to simulator speeds being at least 10,000$`\times`$ slower than the execution time of the original application. This is due to the requirements to simulate a large number of microarchitectural components (caches, branch predictors, etc.) in detail.

### Synthetic Workload Generation

It is possible to generate small, synthetic workloads that take significantly less time to simulate, like has been done with [previous][kim2006wsgbwfsep] [works][liang2023deacfncs]. As with all methodologies, there are tradeoffs for using them. For [Ditto][liang2023deacfncs], a recent work focusing on synthesizing workloads for data centers, the techniques can do well when mimicking traditional CPU performance behaviors, like branch mispredictions, cache miss rates, and IPC, these workloads generation techniques [might not be applicable to all workload studies][liang2023deacfncs], like cache compression or prefetching.

### Workload Sampling

Instead of generating smaller, synthetic workloads, it can be possible to simulate a representative portion of the actual application's execution. In this way, it can now be possible to simulate the original workloads more quickly than before. Two of the most prevalent methodologies are the [SMARTS][wunderlich2003samsvrss] and [SimPoint][sherwood2002aclspb] methodologies, which are based on statistical sampling and region similarity detection, respectively.

[SimPoint][sherwood2002aclspb] employed Basic Block Vectors (BBVs) as distinctive signatures to represent instruction streams of fixed or variable length intervals, leveraging the principle that similar workloads should traverse comparable sequences of basic blocks. However, the [SimPoint][sherwood2002aclspb] approach failed to consider performance disparities between similar instruction streams arising from micro-architectural and hardware variations, such as cache states and frequency variations, and also neglected the effects of thread interactions in multi-threaded scenarios.
[SMARTS][wunderlich2003samsvrss] introduced a systematic sampling framework that simulated programs by alternating between fast-forward, warm-up, and detailed simulation phases, generating IPC samples for each detailed simulation. This approach enabled highly accurate estimation of program-wide IPC through statistical methods. Nonetheless, the scope of [SMARTS][wunderlich2003samsvrss] was limited to estimating overall IPC and could not provide granular IPC traces throughout program execution.
[LiveSim][hassani2016livesim] extended the statistical sampling framework by incorporating in-memory checkpoints at sample regions, facilitating interactive simulations. Similar to [SMARTS][wunderlich2003samsvrss], [LiveSim][hassani2016livesim] focused on IPC estimation with confidence levels but lacked the ability to generate detailed IPC traces.
[pFSA][sandberg2015pfsa] employed hardware virtualization to spawn processes that could fast-forward to regions of interest (ROIs) at near-native speeds and execute detailed simulations concurrently. Additionally, [pFSA][sandberg2015pfsa] introduced a novel cache warm-up technique based on estimating the error introduced by insufficient cache warming. 

[SimFlex][wenisch2006simflex] introduced a multi-threaded sampling technique, building upon the [SMARTS][wunderlich2003samsvrss] methodology, by strategically sampling processors executing the program's critical path.
[COTSon][argollo2009cotson] extended the simulation scope to encompass the entire software stack and hardware models, ensuring both high performance and accuracy.
[Time-based sampling][ardestani2013tbs] [methodologies][carlson2013tbs] pioneered a generic simulation framework for multi-threaded applications, employing time progression rather than instruction count. However, this approach was constrained by the execution time of the program, limiting its ability to identify program structures. Neither SimFlex nor Time-based Sampling leveraged software-specific knowledge, such as barriers, tasks, and loops, to inform the decomposition of programs into representative regions.
[BarrierPoint][carlson2014barrierpoint] and [TaskPoint][grass2018taskpoint] are based on the structural characteristics of multi-threaded programs, utilizing barrier synchronization primitives and tasks as units of work, respectively. This approach enabled the automatic identification of software regularities based on the inherent synchronization primitives. Nonetheless, these methods were restricted by their dependence on specific programming paradigms, limiting their general applicability.
[LoopPoint][sabu2022lcddfma] proposed a generic profiling-based sampling methodology that eschewed assumptions about software style, employing loop boundaries as a heuristic for delineating representative regions. However, this heuristic failed to exploit the hierarchical nature of programs and lacked support for dynamic software and hardware changes.

Kambadur et al. introduced a [sampling solution for Intel GPU workloads][kambadur2015fcgdwg] building upon [GTPin][skaletsky2022gtpin], employing kernel names, arguments, and basic block entries to identify representative regions within GPU programs at a kernel-level granularity. 
[TBPoint][huang2014tbpoint] utilized BBVs and other kernel-specific features to pinpoint representative kernels, while [Principal Kernel Analysis (PKA)][baddouh2021pka] monitored IPC variations between sampling units to identify regions suitable for fast-forwarding. Both [TBPoint][huang2014tbpoint] and [PKA][baddouh2021pka] facilitated the sampled simulation of GPU workloads at both inter- and intra-kernel levels. [Sieve][tahan2023sieve] extended previous research, demonstrating that combining kernel names and instruction counts led to more effective sample selection. [Photon][liu2023photon] employed GPU BBVs for both inter- and intra-kernel workload sampling, resulting in a substantial enhancement in sampling accuracy compared to prior methods.

[sabu2022lcddfma]: http://doi.org/10.1109/HPCA53966.2022.00051
[clements1992tpoch]: https://dl.acm.org/doi/abs/10.5555/531245
[cloudsuite]: https://github.com/parsa-epfl/cloudsuite
[hennessy2011caaqa]: https://dl.acm.org/doi/abs/10.5555/1999263
[kim2006wsgbwfsep]: https://doi.org/10.1109/IISWC.2014.6983051
[krishnamurthy2006aswtgtfstss]: https://doi.org/10.1109/TSE.2006.106
[liang2023deacfncs]: https://doi.org/10.1145/3575693.3575751
[qemu]: https://www.qemu.org
[sherwood2002aclspb]: https://doi.org/10.1145/605432.605403
[sim_gem5]: https://www.gem5.org
[sim_simflex]: https://parsa.epfl.ch/simflex/
[sim_sniper]: https://snipersim.org
[sim_accelsim]: https://accel-sim.github.io
[sim_mgpusim]: https://github.com/sarchlab/mgpusim
[speccpu2017]: https://www.spec.org/cpu2017/
[wikipedia_circuit_design]: https://en.wikipedia.org/wiki/Circuit_design
[wikipedia_comp_arch]: https://en.wikipedia.org/wiki/Computer_architecture
[wikipedia_emulation]: https://en.wikipedia.org/wiki/Emulator
[wikipedia_isa]: https://en.wikipedia.org/wiki/Instruction_set_architecture
[wikipedia_logic_synthesis]: https://en.wikipedia.org/wiki/Logic_synthesis
[wikipedia_timing_simulation]: https://en.wikipedia.org/wiki/Simulation
[wunderlich2003samsvrss]: https://doi.org/10.1145/859618.859629
[carlson2014barrierpoint]: https://doi.org/10.1109/ISPASS.2014.6844456
[ardestani2013tbs]: https://doi.org/10.1109/HPCA.2013.6522340
[carlson2013tbs]: https://doi.org/10.1109/ISPASS.2013.6557141
[wenisch2006simflex]: https://doi.org/10.1109/MM.2006.79
[hassani2016livesim]: https://doi.org/10.1109/HPCA.2016.7446098
[sandberg2015pfsa]: https://doi.org/10.1109/IISWC.2015.29
[argollo2009cotson]: https://doi.org/10.1145/1496909.1496921
[grass2018taskpoint]: https://doi.org/10.1109/TC.2018.2860012
[skaletsky2022gtpin]: https://doi.org/10.1109/ISPASS55109.2022.00011
[kambadur2015fcgdwg]: https://doi.org/10.1109/IISWC.2015.14
[huang2014tbpoint]: https://doi.org/10.1109/IPDPS.2014.53
[baddouh2021pka]: https://doi.org/10.1145/3466752.3480100
[tahan2023sieve]: https://doi.org/10.1109/ISPASS57527.2023.00030
[liu2023photon]: https://doi.org/10.1145/3613424.3623773

