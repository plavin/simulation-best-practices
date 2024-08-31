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
