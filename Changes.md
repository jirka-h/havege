# Explanation of modifications #

## Circular bitshift ##
I have replaced a lot of code like
```
inter = (*Pt0 >> (1)) ^ (*Pt0 << (31)) ^ HARD;
//Right circular shift by 1 bit XOR HARD
```
in the main loop `OneIteration.h` with inlined assembler code to call ROR/ROL assembler instruction. This result in 30% speed-up of the code.

I have defined right circular bitshift function as
```
__inline__ unsigned int ror32f(unsigned int operand, unsigned int width) {
  __asm__ __volatile__ ("rorl %b1, %0"
                        : "+mr" (operand)
                        : "Jc" (width)
                        );
	return operand;
}
```
Explanation:<br>
<code>+mr</code> => + means that it's used for both input&output<br>
<code>mr</code> means that it can be memory or register<br>
<code>Jc</code> => means either integer in range 0..63 or c register<br>
<br>
<code>%b1,%0</code><br>
<code>%b1</code> => width of the first operand is 8 bits. <br>
<code>rorl</code> => means that we are working with m32 (32 bit memory) - Important for memory arguments.<br>
<code>rorq</code> => means that we are working with m64 (64 bit memory) - Important for memory arguments.<br>


<h2>Why I have moved from <code>int</code> to <code>unsigned int</code></h2>
I have discovered this bug when moving from<br>
<pre><code>inter = (*Pt0 &gt;&gt; (1)) ^ (*Pt0 &lt;&lt; (31)) ^ HARD;<br>
//Right circular shift by 1 bit XOR HARD<br>
</code></pre>
to<br>
<pre><code>ror32f(*Pt0,1);<br>
</code></pre>
The original code is intended to do circular bitshift. However, <code>Pt0</code> is declared as <code>int*</code>. So it's signed number and gcc compiler will use SAR / SAL ARITHMETIC bitshift instructions. SAR instruction will copy the sign bit and give the different results than SHR instruction (logical bitshift instruction). See <a href='http://www.arl.wustl.edu/~lockwood/class/cs306/books/artofasm/Chapter_6/CH06-3.html#HEADING3-42'>detailed explanation</a>.<br>
<br>
The results of logical circular shift (ROR/ROL - function ror32f) are not the same as result of two shifts combined together through OR/XOR operator:<br>
<br>
<pre><code>-189                            11111111111111111111111101000011<br>
ror32f(-189,5)                  00011111111111111111111111111010<br>
(-189 &gt;&gt; 5) | (-189 &lt;&lt; 27)      11111111111111111111111111111010<br>
(-189 &gt;&gt; 5) ^ (-189 &lt;&lt; 27)      11100111111111111111111111111010<br>
</code></pre>

As long as you will use <code>ror32f</code> function defined above there is no difference whether <code>int</code> or <code>unsigned int</code> is used. On the other hand, the original code<br>
<pre><code>inter = (*Pt0 &gt;&gt; (1)) ^ (*Pt0 &lt;&lt; (31)) ^ HARD;<br>
//Right circular shift by 1 bit XOR HARD<br>
</code></pre>
works as intended only for <code>unsigned int</code>. Therefore I have decided to move to <code>unsigned int</code> to make the results consistent.