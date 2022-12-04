# Computer architecture

TODO merge in my notes from corey, CS152

TODO fused multiply add (FMA)

misc notes

- harvard architecture: separate instr & data mem
- x87: the FPU

SSE

- has ssqrt and a much faster rsqrt

ARM

- invents designs; licenses to actual mfr's
- reasonable perf, good price/perf, amazing power/perf
    - no multicore, no >2gb ram
- pushed by dga (FAWN), james hamilton

intel atom

- good low-power chip but currently no 64-bit mem or ECC

RAM

- frequency, bandwidth: each xfer ships 8 bytes
    - eg ddr3-1333 (pc3-10600): 1333 MHz * 8 bytes/xfer = 10.6 GB/s
- ddr: data per xfer; ddr = 2x, ddr2 = 4x, ddr3 = 8x
    - eg ddr3-1333 (pc3-10600): already accounts ddr3
- channels: width btwn cpu and ram
    - eg dual channel ddr3-1333 (pc3-10600): 2*10600=21200 GB/s
- registered ram: buffered; higher capacity but lower latency
- rank: independently accessible 64-bit area of the memory (don't understand)
- misc terms: ecc, capacity
- [http://blog.serverfault.com/post/know-your-ram/](http://blog.serverfault.com/post/know-your-ram/)

power mgmt

- cpu idle
    - 3 modes ("C states")
        - C1: 1us exit latency, 1000mW; turns off CPU clock
        - C2: 1us exit latency, 500mW; turns off other clocks
        - C3: 57us exit latency, 100mW; power down parts of CPU; most time spent here
    - [http://lwn.net/Articles/384146/](http://lwn.net/Articles/384146/)

nvidia GPUs

- *Parallel Thread eXecution (PTX)* layer: bytecode; helps portability; no legacy machine code
- nvidia has very good appreciation of software importance
- OpenCL, DirectCompute are comparable; more standard than single-vendor CUDA

nvidia fermi/gt300, due 2010

- biggest advance since gt80
    - gt200 made big changes, but was fundamentally same design as G80
    - demo'd real-time raytracer
- major features geared toward HPC, don't help graphics
    - real FP in quality and perf; IEEE 754-2008 with fused multiply add
        - double precision now half speed of single precision, vs. 10th in G80
    - ECC main mem and caches
    - 64-bit virtual address space; PTX helped migration
    - caches
        - graphics apps had enough parallelism to hide DRAM latency
        - L1: 64 KB SRAM; configurable semi-coherence; 16/48 KB shared/cache or vice-versa (cache = coherent within SM)
        - regs: 128 KB > L1; total L1 = 768 KB = L2
    - fast context switching: 20-25 us; enables indep kernels overlapping execution
    - unified addr space: used to have separate instructions for accessing scratchpad, graphics, and system mems
    - debugging support with traps/breakpoints to freeze GPU
    - faster atomic instrs for task-based parallelism
        - graphics apps needed less synchronization
        - G80 had atomics; now atomics can be in L2; 5-20X faster
    - brand new instr set: more RISC-like, with sep load/store instrs; should last
    - faster than G80
- more
    - indirect branches
- architecture TODO
    - GDDR5
- remaining challenges
    - small GPU memory; no direct IO; no glueless multisocket HW, nor SW support with cache coherence
- contemporary landscape
    - intel's larrabee: too out there; will probably be much slower
    - AMD/ATI's evergreen: focusing on graphics/gaming; market is not as strong during econ downturn
    - nvidia also pressing mobile; they may be working on x86 due to looming IGP (GPU merging with CPU)
- ref: patterson paper, RWT article, ars technica, press release

intel arch

- iret: restores context; for returning from interrupt/exception handler
    - complex; pseudo-code spans pages in manual
    - 2 stages: pre-commit, post-commit (commit = privilege level change)
    - from [http://www.cr0.org/misc/CVE-2009-2793.txt](http://www.cr0.org/misc/CVE-2009-2793.txt)
        
        ```bash
        IF new mode != 64-Bit Mode
        	THEN
        		IF tempEIP is not within code segment limits
        		THEN #GP(0); FI;
        	EIP <- tempEIP;
        ELSE (* new mode = 64-bit mode *)
        	IF tempRIP is non-canonical
        		THEN #GP(0); FI;
        	RIP <- tempRIP;
        FI;
        CS <- tempCS;  // This is the commit point (privilege switch)
        EFLAGS (CF, PF, AF, ZF, SF, TF, DF, OF, NT) <- tempEFLAGS;
        ```
        

disks

- native command queueing (NCQ): a SATA tech that allows HD to internally
reorder operations
    - also used in SSDs but for a reverse purpose: because the SSDs experience
    latency in the CPU; this allows for a batch of operations to be issued
    - obsoletes PATA tagged command queueing (TCQ), which was CPU intensive
- extensible firmware interface (EFI)
    - intel's proposal to replace PC BIOS, one of few remaining parts of orig IBM PC
- hard disk layout
    - MBR: 512 B
        - 0-445: boot loader (eg GRUB)
        - 446-511: partition table
- logical block addressing (LBA): controller manages mapping to virtualize
physical locations of logical blocks; keep bad block table; requires spare
blocks
    - ECC: controller calculates ECC and stores together w data
- BIOS: de facto standard in defining a firmware interface
    - performs bootstrapping: identify, test, initialize system devices like display card, HDD
    - stored on chip
- unified extensible firmware interface (UEFI): predominate new PCs by 2011
    - based on intel-only EFI
    - alts: open firmware, coreboot
- programmable interrupt controller (PIC): device that allows pri level to be
assigned to its interrupt outputs
    - when device has multiple interrupt outputs to assert, it will assert them
    in order of relative pri
    - common modes: hard pris, rotating pris, cascading pris
    - PICs typically have common set of registers
        - interrupt request register (IRR): which ints are pending acks; typically
        can't directly access this
        - in-service register (ISR): which ints acked but waiting for end of
        interrupt (EOI)
        - interrupt mask register (IMR): which ints to be ignored/unacked
    - interrupts may be edge triggered or level triggered
- intel 8259
    - 8259, 8259A, 8259B
    - acts as multiplexer: combines multiple interrupt input sources into a
    single interrupt output to interrupt a single device
- advanced programmable interrupt controller (APIC)
    - replaces intel 8259A in newer x86s
    - local APIC (LAPIC): one per cpu
        - in orig systems, LAPICs and IO APICs connected by dedicated APIC bus
        - newer systems use system bus for comm btwn all APIC components
        - manages all external interrupts for cpu
        - accepts & generates inter-processor interrupts (IPIs) btwn LAPICs
        - support up to 224 usable IRQ vectors from IO APIC; 0-31 reserved for x86
        exception handling
    - IO APIC; one per peripheral bus
        - contain redirection table, used to route interrupts from peripheral buses
        to 1+ LAPICs
- *instruction-level parallelism (ILP)*
- hardware multithreading: applies to superscalar processors
    - *simultaneous multithreading (SMT)*: multiple threads can execute in parallel in each pipeline stage
        - eg *HyperThreading (HT)*, Intel's successor to super-threading
    - *temporal multithreading* aka *super-threading*: one thread at a time per pipeline stage
        - eg *super-threading*, but only with granularity of cycles
- pins
- standard
    - CPU
    - *northbridge* aka *memory controller hub (MCH)*: Intel moving on-chip (TODO only heard this at mtg)
    - *front-side bus (FSB)*: connects CPU and northbridge
    - back-side bus: TODO
    - southbridge aka *IO controller hub (ICH)*: TODO
    - [WP](http://en.wikipedia.org/wiki/Northbridge_(computing))
- *memory management unit (MMU)*: handles CPU mem accesses
    - translates virt addrs to phys addrs
    - mem protection
    - cache control
    - bus arbitration
    - divide virt addr space into pages
    - *translation lookaside buffer (TLB)*: cache of *page table entries (PTE)*
    for translating *virtual page numbers (VPN)* to *phys page numbers (PPN)*
        - PTE also has: dirty bit, accessed bit (LRU), process protection
        (user/supervisor), cached
        - on miss, a slower mechanism, eg software, is used
        - if VPN has no PPN, signal *page fault* to OS
            - if legal VPN, OS can find spare RAM or page in from disk
            - if no RAM left, evict mem page to disk
            - if no TLB left, evict PTE
    - x86 MMU is extremely complex (for compatibility)
- caches
    - inclusive: lower-level cache included in higher-level cache; unique data
    limited to higher-level cache
    - exclusive: lower-level cache and higher-level cache include unique data;
    capacity is sum of caches
        - makes more sense when there are multiple lower-level caches, since
        associativity spells potential conflicts in the higher-level cache
    - [http://www.amd.com/us-en/Processors/SellAMDProducts/0,,30_177_4458_4513^1413^2128,00.html](http://www.amd.com/us-en/Processors/SellAMDProducts/0,,30_177_4458_4513%5E1413%5E2128,00.html)
- *hardware scout* aka *run-ahead scout*: on cache miss, continue with loads in
the same instr stream
    - performs prefetches during cache misses
    - exploits *memory-level parallelism (MLP)*
- fat camp
- lean camp
- speed demons: try to get as many raw cycles out
- brainiacs: try to get as much work done per cycle
- data structures
    - linked lists have poor cache properties, eg traversal is non-sequential
- *cache-oblivious* algo: exploit cache without knowing cache size as explicit
param
    - eg cache-oblivious linked lists
    - coined by CEL
- IO
    - *programmed input/output (PIO)*
        - method of transferring data between CPU and peripheral (network adapter,
        storage device, etc.)
    - *direct memory access (DMA)*
- virtualization
    - Intel Virtualization Technology (IVT)
        - TODO
    - AMD Virtualization (AMD-V)
        - TODO
- GPU
    - NVIDIA CUDA
    - Apple OpenCL: now supported by NVIDIA
    - MS DirectCompute: in DX11
- *standard performance evaluation corporation (SPEC)*
    - TODO
- Cray
- AMD
    - Opteron (Barcelona)
        - exclusive caches (L1/L2, L2/L3)
            - [http://209.85.165.104/search?q=cache:1EE4_L4_B0EJ:www.amd.com/us-en/assets/content_type/white_papers_and_tech_docs/23932.pdf+inclusive+|+exclusive+cache+opteron&hl=en&ct=clnk&cd=1&gl=us&client=opera](http://209.85.165.104/search?q=cache:1EE4_L4_B0EJ:www.amd.com/us-en/assets/content_type/white_papers_and_tech_docs/23932.pdf+inclusive+%7C+exclusive+cache+opteron&hl=en&ct=clnk&cd=1&gl=us&client=opera)
            - [http://www.anandtech.com/IT/showdoc.aspx?i=3162&p=2](http://www.anandtech.com/IT/showdoc.aspx?i=3162&p=2)
- Intel
    - Clovertown
- Sun
    - Niagara
        - 8 core, 32 thread
        - simple 5-stage cores with very few stalls
        - coarse-grained hyperthreading: all functional units must be operating on
        same thread
        - switch after every instr, rather than run till stall
    - UltraSPARC T2 Plus: multi-socket UltraSPARC T2
        - full cross bar between cores' L2 and memory
        - 8 mem banks
        - coherence units
        - fully buffered DIMMs
        - 65nm process
- IBM
    - POWER5
        - OOO execution
    - POWER6
        - 5 GHz
        - 2 to 4 thread
        - released 4/10/08
        - in-order execution
        - fine-grained hypethreading: all functional units can operate on different threads
- DDR3: fully buffered DIMM

internet-scale service infrastructure efficiency
talk at ISCA (international symposium on system architecture) 2009
by james hamilton

- enterprise vs. internet-scale datacenters
    - enterprise: largest cost = ppl; ~100:1 machine:ppl
        - consolidate onto fewer, larger systems; large sans for storage, large
        routers for networking
    - internet-scale: largest cost = server & storage hw
        - then cooling, power dist, power
        - networking varies from very low to dominant
        - ppl < 10%, often < 5%; >1000:1 machine:ppl
    - optimize for work-per-$ or work-per-joule
- costs
    - assumptions:
        - facility: ~$200M for 15MW facility (15-yr amort)
        - servers: ~$2K * 50,000 (3-yr amort)
        - server draws 80% max power when running at 30% utilization
        - commercial power: ~$0.07/kWhr
    - monthly costs: $3M servers, $1.3M power/cooling, $1M power, $.2M other
    - power related costs trending flat or up; server costs trending down
- PUE (power usage effectiveness) = total facility power / it equip power
- DCiE (data center infrastructure efficiency) = it equip power / total
facility power * 100%
- power: 59% equip, 8% power xmission/switching, 33% cooling (high)
- cooling: all air cooling using ACs, outside air
    - IO: 5W-25W, 50C-60C
    - CPU: 40W-200W, 60C-70C
    - mem: 3W-20W, 85C-105C
    - HD: 7W-25W, 50C-60C
    - cloudrack C2: 40C
- servers
    - 1983-2007: seq BW 110X, rand 2X; rand growth 10% of seq; disk seq BW
    lagging DRAM/CPU; 15-150 days to rand read 1TB
    - techniques
        - sequentialize workloads
        - redundant/denormalized data
        - distributed memory (network) faster
        - better distribution of hot data among cold data
        - partition hot/cold data onto SSDs/HDDs

a scalable data center network architecture
amin vahdat, ucsd

- typical topology: 2- or 3-tier tree (here is 3); 3 supports ~25K hosts
    - 1G end-of-rack (edge) switches, 48-288 ports, 10G uplinks
    - 10G end-of-row (aggregation) switches, 32-128 ports
    - 10G core switches connected via mesh
    - oversubscribed by 2.5 (400Mbps) or 8:1 (125Mbps)
    - ms is oversubscribed at core by 240x
- want to achieve "full bisection bandwidth"
- use a special instance of a clos topology (telecom technology) called a
*fat-tree*
    - 16 hosts across 4 pods, 4 per pod, pods are connected at the top
    - can scale down; to oversubscribe by 2x, just use half as many switches per
    pod
    - simple regular structure
    - using 48-port switches, support 27K hosts
    - key challenge: scheduling (NP hard)
        - rough idea: have a centralized scheduler that inspects "elephant" flows
        and places them using heuristics
- portland: nice features like vm migration without ip renewal, etc
- question: relation to monsoon networks?

hardware can make data center software simpler and more robust
james larus
icsa 09

- heterogeneity
    - helios: heterogeneous multiprocessing with satellite kernels (sosp09)
    - resource allocation and scheduling are huge problems
- monsoon network: provably hotspot free for any admissible traffic matrix
    - software can now ignore network topology (for bandwidth)
- use pcm for reliable message transport (messages eventually processed)

TODO

- specint

IA32

- access MSRs (eg apic regs) via rdmsr and wrmsr

x86

- [http://www.intel.com/products/processor/manuals/](http://www.intel.com/products/processor/manuals/)
- PAE: physical address extension
    - with OS support, allows system to use >4GB (up to 64GB) physical mem
    - a process can still only map 4GB in address space at a time though
- IRQs
    - older: intel 8259; newer: intel apic
    - view on windows using device manager or `msinfo32`
    - view on linux using `/proc/interrupts` or `procinfo`
    - IA64/AMD64: 16 IRQLs
    - x86: 32 IRQLs
    - diff primarily due to type of interrupt controller of each CPU
    - each cpu can be running at a different IRQL
    - distinct from windows IRQLs
- rings 0-3; usu. only 0 and 3 used

x86_64 abi

- function args go into: rdi rsi rdx rcx r8 r9 stack (must end on 16-byte boundary)

PC architecture

- [http://arstechnica.com/hardware/news/2009/09/intel-launches-all-new-pc-architecture-with-core-i5i7-cpus.ars](http://arstechnica.com/hardware/news/2009/09/intel-launches-all-new-pc-architecture-with-core-i5i7-cpus.ars)

latencies everyone should know (ns)

- L1 cache reference: 0.5
- Branch mispredict: 5
- L2 cache reference: 7
- Mutex lock/unlock: 25
- Main memory reference: 100
- Compress 1KB bytes with Zippy: 3,000
- Send 2K bytes over 1 Gbps network: 20,000
- Read Flash: 100,000
- Read 1MB sequentially from memory: 250,000
- Roundtrip within same datacenter: 500,000
- Disk seek: 10,000,000
- Read 1MB sequentially from disk: 20,000,000
- Send packet CA -> Netherlands -> CA: 150,000,000

DRAM Errors in the Wild: A Large Scale Field Study

- 1/3 machines, 8% DIMMs saw correctable errors in 1 year
- per-DIMM: avg 25K-75K FIT (failures in time per billion hrs of operation) per Mbit, median 778-25K
    - prev studies: 200-5K
    - high variance across DIMMs
- memory errors strongly correlated (in space and time)
    - a DIMM that sees error is 13-228X likely to see another in same month
    - autocorrelation shows significant corr levels up to 7 months
    - 70-80% uncorrectable errors preceded by correctable
    - presence of correctable error increases prob of uncorrectable error by 9-400X
    - abs. prob of correctable-then-uncorrectable is small, .1-2.3%/month
        - so, replacing DIMMs on correctable error is only attractive if downtime outweighs costs
- CEs increase after 10-18 months; UEs decrease continuously early on (due to replacements; survival of fittest)
- newer, denser DIMMs don't have increasing CE rates; in fact, latest have lower rates than 2 older platforms
- temp has no effect
- strong corr with utilization
- measurements don't distinguish, but unlikely to be dominated by soft errors; rather, hard errors or errors on data path
    - used *memory scrubbers* to periodically scan otherwise idle ECC RAM for errors
    - *soft error*: random bit corruption; most prior work focuses on this
    - *hard error*: repeatable corruption due to physical defect (eg "stuck bits")
- [kathy yelick's ICSA09 keynote: ECC recovery is common and slows compute]
- [http://perspectives.mvdirona.com/2009/10/07/YouReallyDONeedECCMemory.aspx](http://perspectives.mvdirona.com/2009/10/07/YouReallyDONeedECCMemory.aspx)

## Storage

Petabytes on a budget: How to build cheap cloud storage (Backblaze blog post 2009)

- 67 TB 4U servers for $7,867
- major components
    - 45 HDs $5400
    - 4 SATA cards $175
    - 2 PSUs $540
    - custom case with 6 fans, 1 boot drive, 9 multiplier backplanes $758
    - mobo/cpu $365
    - 4 GB RAM $50
- software: https, JFS, 3 RAID6 volumes
    - IBM JFS is journaling file system...curious why they chose this

Nimbus Data Systems

- 100TB NAND, iSCSI, NFS, CIFS (SAN/NAS)
- [http://www.theregister.co.uk/2010/04/26/nimbus_s_class/](http://www.theregister.co.uk/2010/04/26/nimbus_s_class/)

## Misc

from facebook engineering blog post on web app benchmarking system "dyno"

> After testing several generations of processors and micro-architectures, we
determined that the most significant limiting factor of request throughput is
typically memory access contention. On web nodes, our application typically
allocates 25MB per request. When we compare several generations of processors
under high load, we see dramatic gains in the maximum throughput of the node
when the hardware has a more advanced memory architecture.
> 

Brawny cores still beat wimpy cores, most of the time (Urs Holzle, Google)

- serialization/communication overheads grow
- inherently serial work dominates execution time
- always ignore SW dev costs; devs will have to invest into fighting latency
- the more workers, the higher the prob of stragglers
- more smaller systems can increase overall cluster cost if non-CPU costs can't
be scaled down accordingly
    - basic infrastructure: enclosures, cables disks, power supplies, network
    ports, cables, ...
    - higher % of DRAM for OS (non throughput related)
- lower utilization: easier to bin-pack larger bins than smaller bins
- parallel algos may be less efficient w smaller bins
    - eg if stopping condition based on global info, to avoid global comm/sync,
    use more local heuristics that are sloppier and more conservative, doing
    more work than necessary
- [http://research.google.com/pubs/archive/36448.pdf](http://research.google.com/pubs/archive/36448.pdf)