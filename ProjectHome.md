A high-throughput, unpredictable random number generator...

See http://www.irisa.fr/caps/projects/hipsor/ for more details.

I have ported HAVEGE to ATHLON64.

I have updated both 32 and 64 bit version of HAVEGE for ATHLON64 architecture. I have
  * modified the size of the inner loop (see `src/LoopBody.h`) to just fit into the instruction L1 cache.
  * replaced assembler function which calls RDTSC instruction with inline assembler (speed improvement by 20%)
  * replaced RDTSC assembler instruction with RDTSCP instruction (RDTSCP instruction is a serializing variant of the RDTSC instruction. See http://en.wikipedia.org/wiki/Time_Stamp_Counter)
  * replaced a lot of code like
```
inter = (*Pt0 >> (1)) ^ (*Pt0 << (31)) ^ HARD;
//Right circular shift by 1 bit XOR HARD
```
in the main loop `OneIteration.h` with inlined assembler code to call ROR/ROL assembler instruction. This result in 30% speed-up of the code. See [more detailed explanation](Changes.md).
  * changed type of internal variables from `int` into `unsigned int`. This was a bug in the original code. See [explanation.](Changes.md)
  * extensively tested the code

Please read [this wiki](PageName.md) for usage instructions.