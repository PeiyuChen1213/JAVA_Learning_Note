# CA assignment

### POWER AND ENERGY DISSIPATION 

多核处理器的发展和性能增长通常伴随着增加的功耗和能量消耗。随着核心数量的增加和频率的提高，处理器芯片会产生更多的热量，需要更多的能量供应和散热措施来保持温度在可接受的范围内。

1. Power and Energy Profiling of Scientific Applications on Distributed Systems" 这个标题的意思是“在分布式系统上对科学应用进行功耗和能量分析”。这一部分可能会介绍对科学应用在分布式系统上的功耗和能量特性进行分析和评估的研究。
2. A Multi-Core Approach to Addressing the Energy-Complexity Problem in Microprocessors" 这个标题的意思是“一种多核处理器方法来解决微处理器中的能量复杂性问题”。这一部分可能会介绍一种利用多核处理器来解决微处理器中能量复杂性问题的方法
3. Technology Trends in Computer Architecture and their Impact on Power Subsystems" 这个标题的意思是“计算机体系结构中的技术趋势及其对电源子系统的影响”。这一部分可能会讨论计算机体系结构中的技术趋势，并分析这些趋势对电源子系统的影响。





Due to the high-performance nature of multicore processors, they are widely demanded in real-time system applications, including automotive electronics, telecommunications, interactive television, digital healthcare, aerospace, and portable electronic devices[1]. These fields increasingly require more powerful, flexible, and energy-efficient microprocessor systems to meet the evolving demands of real-time applications and their increasing complexity and intelligence. Currently, multicore processors have become the mainstream in the market, and embedded real-time applications are continuously evolving towards multicore processor platforms[1].

However, the high performance of multicore processors also poses challenges in terms of high energy consumption. For instance, the Intel Core2 Duo processor consumes up to 130W at a working frequency of 3GHz, and the maximum power consumption of the Intel Core i7 processor exceeds 130W[3]. Energy consumption has become a significant factor to consider in real-time embedded systems, especially for battery-powered devices such as unmanned vehicles, wireless mobility, and portable computing devices[3]. However, batteries are limited by weight, volume, and size, and their energy storage capacity is constrained. Frequent battery replacement is unacceptable, particularly in special fields like military and aerospace.

High energy consumption not only shortens the usage time of battery-driven embedded devices, increases their size and weight but also leads to increased costs for heat dissipation, performance impact, reduced device lifespan, and decreased system reliability. Additionally, to meet real-time performance constraints, these embedded systems often adopt conservative design strategies, further increasing system energy consumption. Since processor power consumption typically accounts for 50% of the total computer system energy consumption, reducing processor power consumption has become a focal point in energy-efficient design for real-time multicore systems[4].







Translation:

多核处理器在实时系统应用中具有广泛的需求，如汽车电子、电信通讯、互动电视、数字医疗、航空航天和便携式电子设备等领域[1]。这些领域对性能更强大、操作更灵活、效能比更高的微处理器系统的需求日益迫切，以满足实时应用不断复杂化和智能化的要求。目前，多核处理器已成为市场的主流，嵌入式实时应用也不断向多核处理器平台发展[1]。

然而，多核处理器的高性能也带来了高能耗的挑战。例如，Intel Core2 Duo处理器在3GHz的工作频率下的功耗达到130W，而Intel Core i7处理器的最大功耗也超过130W[3]。能耗成为实时嵌入式系统考虑的重要因素，尤其是对于无人操控装备、无线移动和便携式计算设备等依赖电池供电的设备而言[3]。然而，电池受限于重量、体积和尺寸的限制，其储能能力有限，特别是在军事、航空等特殊领域，频繁更换电池是不可接受的。

高能耗不仅会缩短电池驱动的嵌入式设备的使用时间、增加体积和重量，还会导致散热成本的增加，影响性能，减少设备寿命，并降低系统的可靠性。此外，为满足实时性能约束，这些嵌入式系统往往采用保守的设计策略，进一步增加系统能耗。由于处理器能耗往往占整个计算机系统能耗的50%，降低处理器能耗已成为实时多核系统中节能设计的关注焦点[4]。



当前常见的能耗和功耗管理策略包括动态电压频率调节（Dynamic Voltage Frequency Scaling，DVFS）[5]和动态功耗管理（Dynamic Power Management，DPM）[6]等技术。这些技术已广泛应用于现代多核处理器系统中，以降低处理器的能耗并最大程度地提高能效。

DVFS技术通过在运行时调整处理器的供应电压和执行频率来降低处理器的动态功耗。通过降低电压和频率，处理器的功耗会相应减少。动态功耗通常是处理器速度的凸函数和递增函数，因此较低的执行速度可以显著降低动态能耗。DVFS技术的主要优势是能够根据当前负载和需求动态地调整处理器的性能和功耗，从而实现能耗和性能之间的平衡。

然而，DVFS技术也存在一些限制和挑战。首先，DVFS调整的频率和电压是在一定的离散级别上进行的，因此可能无法精确地满足所有负载和性能需求。其次，频繁的频率和电压调整可能会引起处理器性能的抖动，并且调整过程本身也会消耗一定的能量。此外，DVFS技术通常需要复杂的硬件和软件支持，以确保调整的正确性和稳定性。

另一方面，DPM技术主要通过关闭或休眠处理器来降低静态功耗。当处理器处于空闲或低负载状态时，可以将其关闭或切换到低功耗模式，从而减少静态功耗的消耗。DPM技术在处理器空闲时能够有效降低能耗，但在实时任务需要执行时，需要快速唤醒处理器以满足实时约束。因此，DPM技术需要智能的功耗管理策略来确保实时任务的及时执行和能耗的最小化。

节能实时调度研究致力于在满足实时约束的条件下最小化系统能耗，同时兼顾动态能耗和静态能耗。这方面的研究需要综合考虑任务的执行时间、处理器频率和电压的调整、功耗管理策略等因素。一种常见的方法是将实时任务调度和功耗管理相结合，通过动态地调整处理器的频率和电压，以在满足实时约束的前提下降低系统的能耗。

综上所述，动态电压频率调节（DVFS）和动态功耗管理（DPM）等能耗和功耗管理策略已经在现代多核处理器系统中得到应用。这些策略可以通过调整处理器的电压、频率和功耗状态来降低能耗，并在一定程度上提高系统的能效。然而，这些策略也面临着一些挑战，如精确性、性能抖动和复杂性等方面的限制。



在解决多核系统的功耗问题时，需要克服一些具体的限制和挑战

处理器的实际限制是在多核系统中解决节能实时调度问题时必须面对的挑战之一。多核处理器的型号和规格可能存在差异，如核数、时钟频率、缓存大小等方面的不同，这直接影响了任务的执行能力和性能。不同处理器的性能特性和资源分配策略会对节能实时调度产生重要影响。因此，在解决实际限制时，研究人员需要综合考虑处理器的特性以及任务的需求，并根据实际情况选择合适的任务分配策略和调度算法，以实现最佳的性能和节能效果。

抢占调度的实际限制是另一个关键因素。在多核系统中，任务可以被优先级更高的任务打断并立即执行。然而，频繁的抢占操作会引入额外的开销，如上下文切换和调度冲突。为了在节能实时调度中降低这些运行时开销，可以采用有限抢占调度模型，即限制抢占的次数和条件，以减少不必要的抢占操作。这样可以提高系统的能效，同时确保任务的实时性能。文章提出了一种峰值功耗感知的检查点技术（PPAC），用于在硬实时嵌入式系统中容忍故障并满足功耗约束。PPAC技术通过调整检查点时机和利用核心上的松弛时间，以平衡任务执行和避免超过热设计功耗的风险。实验结果表明，该技术能够在容忍一定数量的故障的同时，保持系统可靠性并满足功耗约束。[7]

为了应对并行任务模型的实际限制，研究人员致力于设计高效的调度算法和通信机制，以优化任务的执行顺序、数据传输和同步操作的开销。在多核系统中，任务可以以并行方式执行，但是任务之间可能存在数据依赖、同步操作和资源共享等约束。通过解决这些约束，可以实现更好的节能效果，并满足多核系统中复杂任务模型的实时调度需求。在这个背景下，研究者关注的是多重关键性环境下并行任务的调度问题。他们以"gang"模型为基础，该模型允许任务在多个处理器核心上同时执行。为了解决这一问题，他们提出了一种名为GEDF-VD的新调度技术，它结合了全局最早截止时间优先调度（GEDF）和带有虚拟截止时间的最早截止时间优先调度（EDF-VD）。通过证明GEDF-VD的正确性，并进行定量评估，研究者展示了该调度技术在多重关键性和非多重关键性情况下的性能。实验结果显示，对于非多重关键性的"gang"任务，GEDF提供了最多2倍的加速上界。而对于考虑多重关键性的"gang"任务，GEDF-VD提供了√5 + 1的加速上界。[8]此外，研究者通过在随机生成的"gang"任务集上进行实验，验证了理论结果，并展示了所提出方法的有效性。因此，这项研究通过解决多重关键性环境下并行任务调度的挑战，提出了GEDF-VD调度技术，并在实验中验证了其性能和有效性，为多核系统中的任务调度问题提供了重要的贡献。

综上所述，在解决多核系统的节能实时调度问题时，处理器的实际限制、抢占调度的实际限制和并行任务模型的实际限制是需要克服的重要挑战。研究人员需要综合考虑处理器的特性、任务的需求和系统的实际情况，采用合适的任务分解、实时调度和节能策略等方法来实现多个维度的合理折中。未来的研究将继续探索创新的方法和技术，以进一步优化多核系统的节能实时调度效果，并满足不断发展的多核处理器和复杂任务模型的需求。





The commonly used energy and power management strategies in current systems include Dynamic Voltage Frequency Scaling (DVFS) and Dynamic Power Management (DPM) techniques. These techniques have been extensively applied in modern multi-core processor systems to reduce the energy consumption and maximize efficiency.

DVFS technology reduces the processor's dynamic power consumption by lowering the supply voltage and operating frequency during runtime. By decreasing the voltage and frequency, the power consumption of the processor is also reduced. Since dynamic power is typically a convex and increasing function of processor speed, lower execution speeds can significantly minimize dynamic energy consumption. The main advantage of DVFS technology is its ability to dynamically adjust the performance and power consumption of the processor based on the current workload and requirements, thus achieving a balance between energy consumption and performance.

However, DVFS technology also has certain limitations and challenges. Firstly, the frequency and voltage adjustments in DVFS are done at discrete levels, which may not precisely meet all the load and performance demands. Secondly, frequent adjustments of frequency and voltage can introduce performance fluctuations, and the adjustment process itself incurs additional energy consumption. Moreover, DVFS technology typically requires complex hardware and software support to ensure the correctness and stability of the adjustments.

On the other hand, DPM technology primarily reduces static power consumption by shutting down or putting the processor to sleep. When the processor is idle or under low load, it can be powered off or switched to a low-power mode to minimize static power consumption. DPM technology effectively reduces energy consumption during processor idle times, but when real-time tasks need to be executed, the processor needs to be quickly awakened to meet real-time constraints. Therefore, DPM technology requires intelligent power management strategies to ensure timely execution of real-time tasks and minimize energy consumption.

Energy-efficient real-time scheduling research aims to minimize system energy consumption while satisfying real-time constraints, considering both dynamic and static power consumption. This research area involves considering factors such as task execution time, processor frequency and voltage adjustments, power management strategies, etc. One common approach is to integrate real-time task scheduling with power management by dynamically adjusting the processor's frequency and voltage to reduce system energy consumption while meeting real-time constraints.

In conclusion, DVFS and DPM are common energy and power management strategies employed in modern multi-core processor systems. These strategies aim to reduce energy consumption by adjusting voltage, frequency, and power state of the processor, thereby improving system efficiency. However, they also face challenges such as precision, performance fluctuations, and complexity.







The actual limitations of processors are among the key challenges in addressing energy-aware real-time scheduling in multi-core systems. Different models and specifications of multi-core processors, such as the number of cores, clock frequency, and cache size, may vary and directly impact the task execution capability and performance. The performance characteristics and resource allocation strategies of different processors significantly influence energy-aware real-time scheduling. Therefore, researchers need to consider the processor's characteristics and task requirements holistically, and choose appropriate task allocation strategies and scheduling algorithms based on the actual situation to achieve optimal performance and energy efficiency.

Another crucial factor is the practical limitations of preemption scheduling. In multi-core systems, a task can be preempted and immediately executed by a higher-priority task. However, frequent preemption operations introduce additional overhead, such as context switching and scheduling conflicts. To reduce these runtime overheads in energy-aware real-time scheduling, a limited preemption scheduling model can be adopted, which restricts the number and conditions of preemption to minimize unnecessary preemptions. This approach improves system efficiency while ensuring real-time performance of tasks. A article proposes a peak-power-aware checkpointing (PPAC) technique for tolerating faults and meeting power constraints in hard real-time embedded systems. PPAC adjusts checkpoint timing and utilizes slack time on cores to balance task execution and mitigate the risk of exceeding thermal design power. Experimental results demonstrate that the proposed technique maintains system reliability and meets power constraints while tolerating a given number of faults. [7]

To address the practical limitations of parallel task models, researchers have devoted their efforts to designing efficient scheduling algorithms and communication mechanisms that optimize the execution order, data transfer, and synchronization overhead of tasks. In multi-core systems, tasks can be executed in parallel, but they may face constraints such as data dependencies, synchronization operations, and resource sharing. By resolving these constraints, better energy efficiency can be achieved, meeting the real-time scheduling requirements of complex task models in multi-core systems.Within this context, researchers have focused on the scheduling problem of parallel tasks in environments with multiple criticalities. They build upon the "gang" model, which allows tasks to execute simultaneously on multiple processor cores. To address this problem, they propose a novel scheduling technique called GEDF-VD, which combines the Global Earliest Deadline First scheduling (GEDF) and the Earliest Deadline First scheduling with Virtual Deadlines (EDF-VD). By proving the correctness of GEDF-VD and conducting quantitative evaluations, the researchers demonstrate the performance of this scheduling technique in scenarios with and without multiple criticalities.Experimental results indicate that for non-multiple criticality "gang" tasks, GEDF provides a maximum speedup bound of up to 2 times. On the other hand, for "gang" tasks considering multiple criticalities, GEDF-VD offers a speedup bound of √5 + 1. Furthermore, the researchers validate the theoretical results and showcase the effectiveness of the proposed method through experiments conducted on randomly generated "gang" task sets.Therefore, this study tackles the challenges of scheduling parallel tasks in environments with multiple criticalities. It introduces the GEDF-VD scheduling technique and validates its performance and effectiveness through experiments, making a significant contribution to the task scheduling problem in multi-core systems.

In summary, when addressing energy-aware real-time scheduling in multi-core systems, it is essential to overcome the actual limitations of processors, preemption scheduling, and parallel task models. Researchers need to consider the processor's characteristics, task requirements, and system constraints comprehensively, employing suitable approaches such as task decomposition, real-time scheduling, and energy-saving strategies to achieve a balanced solution across multiple dimensions. Future research will continue exploring innovative methods and technologies to further optimize the energy-aware real-time scheduling in multi-core systems, meeting the evolving demands of multi-core processors and complex task models.

1. Saidani, T., Piskorski, S., Lacassagne, L., & Bouaziz, S. (2007). Parallelization schemes for memory optimization on the cell processor: a case study of image processing algorithm. In F. Pierfrancesco (Ed.), The Workshop on Memory Performance: Dealing with Applications (pp. 9-16). New York: ACM.
2. Hirata, K., & Goodacre, J. (2007). Arm mpcore: the streamlined and scalable arm11 processor core. In H. Onodera (Ed.), Asia and South Pacific Design Automation Conference 2007 (pp. 747-748). Yokohama: IEEE.
3. Lei, T. (2005). Research on real-time energy-saving scheduling method based on dynamic voltage adjustment [Doctoral dissertation]. Hefei: University of Science and Technology of China.
4. Yi, H. Z. (2006). Research on low-power technology - architecture and compilation optimization [Doctoral dissertation]. Changsha: National University of Defense Technology.
5. Chandrakasan, A., Sheng, S., & Brodersen, R. W. (1992). Low-power CMOS digital design. IEEE Journal of Solid-State Circuit, 27(4), 473-484.
6. Rele, S., Pande, S., Onder, S., & Gupta, R. (2002). Optimizing static power dissipation by functional units in superscalar processors. In R. N. Horspool (Ed.), Lecture Notes in Computer Science 2304 (pp. 85-100). Berlin: Springer Heidelberg.

7. Ansari, M., Safari, S., Khdr, H., Gohari-Nazari, P., Henkel, J., Ejlali, A., & Hessabi, S. (2022). Power-Aware Checkpointing for Multicore Embedded Systems. *IEEE Transactions on Parallel and Distributed Systems*, *33*(12), 4410-4424.
7. ahmed Bhuiyan, A., Yang, K., Arefin, S., Saifullah, A., Guan, N., & Guo, Z. (2019, December). Mixed-criticality multicore scheduling of real-time gang task systems. In *2019 IEEE Real-Time Systems Symposium (RTSS)* (pp. 469-480). IEEE.





