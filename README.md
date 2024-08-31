# Computer Architecture Simulation Best Practices

This document is an attempt to collect knowledge of computer architecture simulation practices that can be used for program analysis, hardware design and next-generation development of computer systems.

## Computer Architecture Overview

Computer architecture at a high level can be described as "the internal organization of a computer in an abstract way; that is, it defines the capabilities of the computer and its programming model." [[1]][clements1992tpoch] It also includes the [Instruction Set Architecture (ISA)][wikipedia_isa] design, microarchitecture design and logic design and implementation [[2]][hennessy2011caaqa], [[3]][wikipedia_comp_arch].

### Computer Architecture Simulation

The [timing simulation][wikipedia_timing_simulation] next-generation computer sytems allows us to understand the performance of a new architecture that exists, or has yet to be built. This includes the timing of critical components, and tends to be at the cycle-level, but does not need to simulate [down to the transistor or logic layers][wikipedia_circuit_design] (although, using [logic synthesis][wikipedia_logic_synthesis] and layout design flows can help to provide more accurate enery, power and area analysis). Timing simulation is different from [architecture (or ISA) emulation][wikipedia_emulation]. Emulating a computer system typically refers to being able to functionally model an ISA of that system, which can be done with popular emulators like [QEMU][qemu].

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

It is possible to generate small, synthetic workloads that take significantly less time to simulate, like has been done with [previous][kim2006wsgbwfsep] [works][liang2023deacfncs]. Unfortunately, these workloads generation techniques [might not be applicable to all workload studies][liang2023deacfncs], like cache compression or prefetching.

### Workload Sampling

Instead of generating smaller, synthetic workloads, it can be possible to simulate a representative portion of the actual application's execution. In this way, it can now be possible to simulate the original workloads more quickly than before.

[sabu2022lcddfma]: http://doi.org/10.1109/HPCA53966.2022.00051
[clements1992tpoch]: https://dl.acm.org/doi/abs/10.5555/531245
[cloudsuite]: https://github.com/parsa-epfl/cloudsuite
[hennessy2011caaqa]: https://dl.acm.org/doi/abs/10.5555/1999263
[kim2006wsgbwfsep]: https://doi.org/10.1109/IISWC.2014.6983051
[krishnamurthy2006aswtgtfstss]: https://doi.org/10.1109/TSE.2006.106
[liang2023deacfncs]: https://doi.org/10.1145/3575693.3575751
[qemu]: https://www.qemu.org
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
