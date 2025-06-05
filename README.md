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
    * [SST/Vanadis][sst_vanadis]
* Memory System Simulators
    * [SST/memHierarchy][sst_memhierarchy]
* GPU Simulators
    * [Accel-Sim][sim_accelsim]
    * [MGPUSim][sim_mgpusim]
* Heterogeneous Simulators
    * gem5 APU simulator 
    * [gem5-gpu][power2014gem5gpu]
* Full-system Simulators
    * [gem5][sim_gem5] 
    * [Simics][magnusson2002simics]
* Distributed Systems/Network Systems:
    * [dist-gem5][alian2017distgem5]
    * [SimBricks][li2022simbricks]
    * [SST/merlin][sst_merlin]
    * [SST/mercury][sst_mercury]
* RTL Simulators
    * [Verilator][snyder2004verilator]
    * [Icarus Verilog][williams2002iverilog]
    * ModelSim
    * XSIM
    * QuestaSim
    * NCSim
    * VCS
    * Riviera-PRO
    * CVC
    * [RepCut][wang2023repcut]
    * [Parendi][emami2024parendi]
    * [SST/osseous][sst_osseous]
* FPGA-Accelerated Simulators
    * [FireSim][karandikar2018firesim]
    * [Simmani][kim2019simmani]
* Spatial Accelerator Simulators
    * [SST/llyr][sst_llyr]
* Analytical Models
    * [Roofline][williams2009roofline]
    * [GPUMech][huang2014gpumech]
    * [Aladdin][shao2014aladdin]
    * [Gables][hill2019gables]
    * [FFM][welton2019ffm]
    * [RPPM][pestel2019rppm]
    * [SparseLoop][wu2022sparseloop]
    * [CiMLoop][andrulis2024cimloop]
* Simulation Frameworks
    * [SST][sst_simulator]

## Computer Architecture Simulation Methodologies

Running modern workloads like the reference (large) inputs of the [SPEC CPU2017 benchmarks][speccpu2017] or the [Cloudsuite datacenter benchmark][cloudsuite] suites [can take years to simulate to completion][sabu2022lcddfma] due to simulator speeds being at least 10,000$`\times`$ slower than the execution time of the original application. This is due to the requirements to simulate a large number of microarchitectural components (caches, branch predictors, etc.) in detail.

### Synthetic Workload Generation

It is possible to generate small, synthetic workloads that take significantly less time to simulate, like has been done with [previous][kim2006wsgbwfsep] [works][liang2023deacfncs]. As with all methodologies, there are tradeoffs for using them. 
[MAMPO][ganesan2011mampo] is a multithreaded synthetic power virus generation framework targeting multicore systems. It uses a genetic algorithm to search for the best power virus for a given multicore system configuration. [SynchroTrace][nilakantan2015synchrotrace] is a trace-based multi-threaded simulation methodology that accurately replays synchronization- and dependency-aware traces for chip multiprocessor systems. SynchroTrace achieves this by recording synchronization events and dependencies in the traces, allowing for the replay on different hardware platforms. [GPGPU-Minibench][yu2015gpgpuminibench] captures the execution behavior of existing GPGPU workloads in a profile, which includes a divergence flow statistics graph (DFSG) to characterize the dynamic control flow behavior of a GPGPU kernel. GPGPU-MiniBench generates a synthetic miniature GPGPU kernel that exhibits similar execution characteristics as the original workload. [G-MAP][panda2017gmap] statistically models the GPU memory access stream locality by considering the regularity in code-localized memory access patterns and the parallelism in the execution model to create miniaturized memory proxies. [Mystique][liang2023mystique] is yet another technique that generates benchmarks from production AI models by leveraging PyTorch execution traces. 
For [Ditto][liang2023deacfncs], another recent work focusing on synthesizing workloads for data centers, the techniques can do well when mimicking traditional CPU performance behaviors, like branch mispredictions, cache miss rates, and IPC, these workloads generation techniques [might not be applicable to all workload studies][liang2023deacfncs], like cache compression or prefetching.

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
[LoopPoint][sabu2022lcddfma] proposed a generic profiling-based sampling methodology that eschewed assumptions about software style, employing loop boundaries as a heuristic for delineating representative regions. However, this heuristic failed to exploit the hierarchical nature of programs and lacked support for dynamic software and hardware changes. [Pac-Sim][liu2024pacsim] employs live profiling and region classification to enable the sampled simulation of multi-threaded applications in the presence of hardware and software dynamism.

Kambadur et al. introduced a [sampling solution for Intel GPU workloads][kambadur2015fcgdwg] building upon [GTPin][skaletsky2022gtpin], employing kernel names, arguments, and basic block entries to identify representative regions within GPU programs at a kernel-level granularity. 
[TBPoint][huang2014tbpoint] utilized BBVs and other kernel-specific features to pinpoint representative kernels, while [Principal Kernel Analysis (PKA)][baddouh2021pka] monitored IPC variations between sampling units to identify regions suitable for fast-forwarding. Both [TBPoint][huang2014tbpoint] and [PKA][baddouh2021pka] facilitated the sampled simulation of GPU workloads at both inter- and intra-kernel levels. [Sieve][tahan2023sieve] extended previous research, demonstrating that combining kernel names and instruction counts led to more effective sample selection. [Photon][liu2023photon] employed GPU BBVs for both inter- and intra-kernel workload sampling, resulting in a substantial enhancement in sampling accuracy compared to prior methods.

The table below outlines the sampled simulation methodologies and their applicability.
<div align="center">

| Methodology | Analysis Type | Parallel Simulation | Warmup | Applicability/Workloads |
| :-- | :--: | :--: | --- | --- |
| [SimPoint][sherwood2002aclspb] | ▣ | ✔️ | Prev Region | Single-threaded CPU |
| [SMARTS][wunderlich2003samsvrss] | □ | ❌️ | Functional | Single-threaded CPU |
| [LiveSim][hassani2016livesim] | ▣ | ✔️ | Checkpoint | Single-threaded CPU |
| [SimFlex][wenisch2006simflex] | □ | ❌️ | Checkpoint | Multi-program CPU |
| [Time-based][ardestani2013tbs] [sampling][carlson2013tbs] | □ | ❌️ | Functional | Multi-threaded CPU |
| [BarrierPoint][carlson2014barrierpoint] | ▣ | ✔️ | Prev Region | Multi-threaded CPU |
| [TaskPoint][grass2018taskpoint] | ▣ | ✔️ | Prev Region | Task-based CPU |
| [LoopPoint][sabu2022lcddfma] | ▣ | ✔️ | Prev Region | Multi-threaded CPU |
| [Pac-Sim][liu2024pacsim] | ● | ✔️ | Statistical | Multi-threaded CPU |

▣ Profile-driven analysis &emsp; □ Statistical analysis &emsp; ● Online profiling

</div>


### Checkpointing Techniques


Checkpoints allow for state exchange among multiple simulators, leveraging the strengths of each. For instance, in sampled simulation, a fast warming simulator that samples workloads can export checkpoints to a detailed simulator, which, though slower, provides precise performance data. This approach enhances simulation speed while maintaining accuracy.

Checkpoints can be categorized based on the type of state they save:

- Architectural checkpoint: Captures the architectural (software-visible) state, including the register files of cores, memory, and I/O device states. Popular emulators like QEMU, Simics, gem5, and VM hypervisors (e.g., KVM) maintain these states.
- Microarchitectural checkpoint: Captures the states of microarchitectural components such as pipelines, caches, branch predictors, TLBs, on-chip network virtual channels, and DRAM controllers.

Research on checkpoints focuses on storage and adaptability. Storage solutions include compression (e.g., QEMU's incremental disk checkpoint and Simics) and pruning techniques like [Livepoints](wenisch2006livepoints), which stores only the state needed for a following short, detailed simulation window. Statistical profiles, such as [MRRL](haskins2003mrrl) and [BLRL](eeckhout2005blrl), store reuse distribution data to save space and are natural to extrapolate.

Adaptability is achieved by storing metadata as hints for post-processing. For example, [Memory Timestamp Record (MTR)](barr2005mtr) tracks each core's last read/write timestamp for every cache line, making it compatible with cache hierarchies that use any write-invalidate coherence state and LRU replacement policy. The aforementioned statistical profiles can also be naturally extrapolated to any cache capacity.

## Parallel Simulation
Modern compuers are increasingly parallel, meaning we need to parallelize our simulations in some way to fully utilize these resources. Some projects take the stance that a single-threaded simulation is accepatble, as you will need to run many simulations during your design-space exploration, so you can use many cores by running many single-threaded simulations. Others, however, have written parallel simulators. [SST][sst_simulator] is a conservative parallel discrete event simulator that can parallelize work across multiple processes or threads. It supports checkpointing -- not for the purpose of state transfer as described above, but to enable restarting simulations after nodes fail, as is common in large-scale, long-running simulations.



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
[liu2024pacsim]: https://doi.org/10.1145/3680548
[magnusson2002simics]: https://doi.org/10.1109/2.982916
[alian2017distgem5]: https://doi.org/10.1109/ISPASS.2017.7975287
[li2022simbricks]: https://doi.org/10.1145/3544216.3544253
[emami2024parendi]: https://doi.org/10.48550/arXiv.2403.04714
[karandikar2018firesim]: https://doi.org/10.1109/ISCA.2018.00014 
[kim2019simmani]: https://doi.org/10.1145/3352460.3358322 
[wu2022sparseloop]: https://doi.org/10.1109/MICRO56248.2022.00096
[power2014gem5gpu]: https://doi.org/10.1109/LCA.2014.2299539 
[williams2002iverilog]: https://dl.acm.org/doi/fullHtml/10.5555/513581.513584 
[snyder2004verilator]: https://veripool.org/verilator/
[barr2005mtr]: https://doi.org/10.1109/ISPASS.2005.1430560
[barr2006bpc]: https://doi.org/10.1109/ISPASS.2006.1620787
[wenisch2006livepoints]: https://ieeexplore.ieee.org/abstract/document/1620785
[haskins2003mrrl]: https://doi.org/10.1109/ISPASS.2003.1190246
[eeckhout2005blrl]: https://doi.org/10.1093/comjnl/bxh103
[wang2023repcut]: https://doi.org/10.1145/3582016.3582034
[williams2009roofline]: https://doi.org/10.1145/1498765.1498785
[huang2014gpumech]: https://doi.org/10.1109/MICRO.2014.59 
[shao2014aladdin]: https://doi.org/10.1145/2678373.2665689 
[hill2019gables]: https://doi.org/10.1109/HPCA.2019.00047 
[welton2019ffm]: https://doi.org/10.1145/3295500.3356213 
[pestel2019rppm]: https://doi.org/10.1109/ISPASS.2019.00038 
[andrulis2024cimloop]: https://doi.ieeecomputersociety.org/10.1109/ISPASS61541.2024.00012 
[ganesan2011mampo]: https://doi.org/10.1145/2063384.2063455 
[nilakantan2015synchrotrace]: https://doi.org/10.1109/ISPASS.2015.7095813 
[yu2015gpgpuminibench]: https://doi.org/10.1109/TC.2015.2395427
[panda2017gmap]: https://doi.org/10.1145/3061639.3062320
[liang2023mystique]: https://doi.org/10.1145/3579371.3589072 
[sst_vanadis]: http://sst-simulator.org/sst-docs/docs/elements/vanadis/intro
[sst_memhierarchy]: http://sst-simulator.org/sst-docs/docs/elements/memHierarchy/intro
[sst_merlin]: http://sst-simulator.org/sst-docs/docs/elements/merlin/intro
[sst_mercury]: http://sst-simulator.org/sst-docs/docs/elements/mercury/intro
[sst_osseous]: http://sst-simulator.org/sst-docs/docs/elements/osseous/intro
[sst_llyr]: http://sst-simulator.org/sst-docs/docs/elements/llyr/intro
[sst_simulator]: http://sst-simulator.org/sst-docs/ 
