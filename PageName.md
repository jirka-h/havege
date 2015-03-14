# What does this package? #

This is a port of [HAVEGE2.0](http://www.irisa.fr/caps/projects/hipsor/) (HArdware Volatile Entropy Gathering and Expansion) to ATHLON64 architecture.

Tested & developed on the following CPU:
|CPU name|AMD Athlon 64 X2 4800+ EE (Brisbane)|
|:-------|:-----------------------------------|
|L1 cache|2 x64 +2 x64 KB (data+instruction)|
|Number of cores|2 |

Compiler used:<br>
gcc --version<br>
gcc (GCC) 4.2.1 (SUSE Linux)<br>

It should work on any ATHLON64 system with the same amount of L1 cache.<br>
<br>
I have updated both 32 and 64 bit version of HAVEGE for ATHLON64 architecture. I have<br>
<ul><li>modified the size of the inner loop (see <code>src/LoopBody.h</code>) to just fit into the instruction L1 cache.<br>
</li><li>replaced assembler function which calls RDTSC instruction with inline assembler (speed improvement by 20%)<br>
</li><li>replaced RDTSC assembler instruction with RDTSCP instruction (RDTSCP instruction is a serializing variant of the RDTSC instruction, see <a href='http://en.wikipedia.org/wiki/Time_Stamp_Counter'>http://en.wikipedia.org/wiki/Time_Stamp_Counter</a>)<br>
</li><li>extensively tested the code</li></ul>

<h1>How to use it?</h1>
<code>make all32</code>   - create 32-bit binaries<br>
<ul><li>HAVEGECRYPTONDATHLON<br>
</li><li>HAVEGENDATHLON<br>
</li><li>HAVEGESPEEDATHLON</li></ul>

<code>make all64</code>  - create 64-bit binaries<br>
<ul><li>HAVEGECRYPTONDATHLON64<br>
</li><li>HAVEGENDATHLON64<br>
</li><li>HAVEGESPEEDATHLON64</li></ul>

I highly recommend to build 64bit binaries on ATHLON64 CPU.<br>
<br>
<code>make test32</code><br>
<code>make test64</code> - runs NIST statistical tests. On average, ~500 tests are run on one dataset. Statistically, 1 test will fail on one dataset with probability ~30%<br>
<br>
<code>make clean32</code> - cleans binaries<br>
<code>make cleantest64</code> - cleans test results<br>
<code>make clean</code> - cleans everything (both 32 bit nad 64 bit binaries and tests results)<br>

<h1>How to link HAVEGE library with your code?</h1>
See <code>src/maincryptond.c</code> and <code>src/mainnd.c</code> for examples how to use the library.