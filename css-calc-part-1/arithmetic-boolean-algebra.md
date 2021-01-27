The Problem
=

So, my initial problem was to start with a proof of concept, a **BCD2LCD** (Binary coded decimal to Liquid Crystal Display) emulator.
This basically took the binary equivalent of the decimal value entered by a button and from that provide the required LCD segments.

Creating the LCD segments were not the issue here, but implementing the logic behind what turned on and what turned off when I clicked a 0 button or a 3 button was.

**Note: I had initially started playing with the idea of working out binary coded hexadecimal to LCD initially when I was designing for proof of concept, so the equation for Segment A differs from the final result used in the actual code. The code below is for the Segment A equation for hexadecimal to LCD.**

Basically what I would be dealing with was a half-byte register with four nibbles **W, X, Y and Z**; for the seven-segment LED display. 
If I was trying to replicate this in CSS, all of the nibbles would naturally  receive a 0 or 1 pulse; and then the CSS would process 
each of the pulses with the Boolean equations I determined, which would then result in each segment being turned on (1) or off (0) 
based on the result derived from each of the seven equations... From there an opacity would be determined for the LCD segment.

There are a few little tricks that can be implemented to create a suitable bit-based Boolean function; now while you can potentially just
create all possible logical functions from **NAND** gates, I wanted to base my calculations using **AND**, **OR** and **NOT** gates

After a little research, I found that I could make this task easier for myself by converting each Boolean equation to a series of
mathematical equations using the following equivalents (referencing: https://en.wikipedia.org/wiki/Boolean_algebra#Basic_operations)

A **AND** B = A * B

A **OR** B = A + B - (A * B)

**NOT** A = 1 - A

where A and B can only equal 0 or 1... This process is simple enough when dealing with two inputs... but what does one do with four inputs
like W, X, Y and Z, I had to ask myself?

For example, my Boolean equation for Segment A (top of the LED display) for a hexadecimal to LCD (i.e. 0-9 and then A to F, to LCD) is:

(W **AND NOT**(X) **AND NOT**(Y)) **OR** (W **AND NOT**(Z)) **OR** (**NOT**(W) **AND** X **AND** Z) OR (**NOT**(W) **AND** Y) **OR** (X **AND** Y) **OR** (**NOT**(X) **AND NOT**(Z))

where W is the most significant bit, and Z is the least significant bit... What were the rules for dealing with multiple 
inputs in this instance? How would one proceed?

I kinda racked my brains trying to work out a means to do this... The AND representations within the Boolean equations were easy enough, as were the NOT representations, but the OR processes were killing me...

What I hadn't realized at first is that these Boolean equations can be solved to eliminate the OR references to a format that was easier to translate arithmetically...

The Process
=

What I found after a little testing and research was that WolframAlpha is a great tool for this sort of thing... We take our Boolean equation: 

(W **AND NOT**(X) **AND NOT**(Y)) **OR** (W **AND NOT**(Z)) **OR** (**NOT**(W) **AND** X **AND** Z) OR (**NOT**(W) **AND** Y) **OR** (X **AND** Y) **OR** (**NOT**(X) **AND NOT**(Z))

Next, we pop it into WolframAlpha.com's engine... 

I didn't realize until I tested it out that WolframAlpha is capable of re-writing logic equations... 
And it was also capable of re-writing them with respect to AND gates and NOT gates only... As a result, my equation was re-written 
as:

**¬(w ∧ x ∧ ¬y ∧ z) ∧ ¬(w ∧ ¬x ∧ y ∧ z) ∧ ¬(¬w ∧ x ∧ ¬y ∧ ¬z) ∧ ¬(¬w ∧ ¬x ∧ ¬y ∧ z)**

Now that I had my Boolean equation re-written with respect to AND and NOT gates, I converted the symbols back to the English equivalents so that I didn't get a migraine reading it:

**NOT**(w **AND** x **AND NOT**(y) **AND** z) **AND NOT**(w **AND NOT**(x) **AND** y **AND** z) **AND NOT**(**NOT**(w) **AND x AND NOT(y) AND NOT(z)) AND NOT(NOT(w) AND NOT(x) AND NOT(y) AND z)

Now, using the arithmetic rules I mentioned in my initial post, wrote the arithmetic equivalent out:

`(1-(w*x*(1-y)*z))*(1-(w*(1-x)*y*z))*(1-((1-w)*x*(1-y)*(1-z)))*(1-((1-w)*(1-x)*(1-y)*z))`

Next, I stuck this back into WolframAlpha, and waited...

What came out was a little... unexpected (see the wxyz.gif in this folder to see the result)...

Had a look? Yeah, I was kinda blown away myself... 

I knew this would take some time to simplify...

Luckily, WolframAlpha has an option to export certain answers into plain text... I took advantage of this and copied and pasted it into a text editor...

Here's the text equivalent:

**x^4y^4z^4w^4-2x^3y^4z^4w^4+x^2y^4z^4w^4-3x^4y^3z^4w^4+6x^3y^3z^4w^4
-3x^2y^3z^4w^4+3x^4y^2z^4w^4-6x^3y^2z^4w^4+3x^2y^2z^4w^4-x^4yz^4w^4
+2x^3yz^4w^4-x^2yz^4w^4-x^4y^4z^3w^4+2x^3y^4z^3w^4-x^2y^4z^3w^4
+3x^4y^3z^3w^4-6x^3y^3z^3w^4+3x^2y^3z^3w^4-3x^4y^2z^3w^4+6x^3y^2z^3w^4
-3x^2y^2z^3w^4+x^4yz^3w^4-2x^3yz^3w^4+x^2yz^3w^4-2x^4y^4z^4w^3+4x^3y^4z^4w^3
-2x^2y^4z^4w^3+6x^4y^3z^4w^3-12x^3y^3z^4w^3+6x^2y^3z^4w^3-6x^4y^2z^4w^3
+12x^3y^2z^4w^3-6x^2y^2z^4w^3+2x^4yz^4w^3-4x^3yz^4w^3+2x^2yz^4w^3+2x^4y^4z^3w^3
-4x^3y^4z^3w^3+2x^2y^4z^3w^3-x^3z^3w^3-6x^4y^3z^3w^3+16x^3y^3z^3w^3
-12x^2y^3z^3w^3+2xy^3z^3w^3+x^2z^3w^3+6x^4y^2z^3w^3-21x^3y^2z^3w^3
+19x^2y^2z^3w^3-4xy^2z^3w^3-2x^4yz^3w^3+10x^3yz^3w^3-10x^2yz^3w^3+2xyz^3w^3
+x^3z^2w^3-3x^3y^3z^2w^3+4x^2y^3z^2w^3-xy^3z^2w^3-x^2z^2w^3+7x^3y^2z^2w^3
-9x^2y^2z^2w^3+2xy^2z^2w^3-5x^3yz^2w^3+6x^2yz^2w^3-xyz^2w^3+x^4y^4z^4w^2
-2x^3y^4z^4w^2+x^2y^4z^4w^2-3x^4y^3z^4w^2+6x^3y^3z^4w^2-3x^2y^3z^4w^2
+3x^4y^2z^4w^2-6x^3y^2z^4w^2+3x^2y^2z^4w^2-x^4yz^4w^2+2x^3yz^4w^2-x^2yz^4w^2
-x^4y^4z^3w^2+2x^3y^4z^3w^2-x^2y^4z^3w^2+2x^3z^3w^2+3x^4y^3z^3w^2
-12x^3y^3z^3w^2+12x^2y^3z^3w^2-3xy^3z^3w^2-2x^2z^3w^2-3x^4y^2z^3w^2
+20x^3y^2z^3w^2-23x^2y^2z^3w^2+6xy^2z^3w^2+x^4yz^3w^2-12x^3yz^3w^2
+14x^2yz^3w^2-3xyz^3w^2-2x^3z^2w^2+5x^3y^3z^2w^2-7x^2y^3z^2w^2+2xy^3z^2w^2
+5x^2z^2w^2-12x^3y^2z^2w^2+22x^2y^2z^2w^2-10xy^2z^2w^2+y^2z^2w^2-2xz^2w^2
+9x^3yz^2w^2-20x^2yz^2w^2+10xyz^2w^2-yz^2w^2-2x^2zw^2-3x^2y^2zw^2+2xy^2zw^2
+xzw^2+5x^2yzw^2-3xyzw^2-x^3z^3w+2x^3y^3z^3w-3x^2y^3z^3w+xy^3z^3w+x^2z^3w
-5x^3y^2z^3w+7x^2y^2z^3w-2xy^2z^3w+4x^3yz^3w-5x^2yz^3w+xyz^3w+x^3z^2w
-2x^3y^3z^2w+3x^2y^3z^2w-xy^3z^2w-5x^2z^2w+5x^3y^2z^2w-13x^2y^2z^2w
+8xy^2z^2w-y^2z^2w+3xz^2w-4x^3yz^2w+15x^2yz^2w-10xyz^2w+yz^2w+xw-xyw
+3x^2zw+4x^2y^2zw-3xy^2zw-5xzw-7x^2yzw+9xyzw-2yzw+zw+x^2z^2+x^2y^2z^2
-xy^2z^2-xz^2-2x^2yz^2+2xyz^2-x+xy-x^2z-x^2y^2z+xy^2z+3xz+2x^2yz-4xyz+yz-z+1**

Simplifying this on the fly would be a pain... and it then occurred to me there was a way to reduce this considerably, as in each equation generated like the above was simply a polynomial, where the only possible values for w, x, y and z were either 0 or 1...

REMOVE ALL THE INDICES!

That's right, because w, x, y and z were essentially pulses, either with a value of 0 or 1, indicies in the format of
w^4 or x^3 or y^2 and so on were going to boil down to w, x, y or z; simply because 1^n = 1 and 0^n = 0 for n>0; so put simply, 
a couple of global replaces later, this "reduced" my equation to the following:

**xyzw-2xyzw+xyzw-3xyzw+6xyzw-3xyzw+3xyzw-6xyzw+3xyzw-xyzw+2xyzw-xyzw-xyzw+2xyzw
-xyzw+3xyzw-6xyzw+3xyzw-3xyzw+6xyzw-3xyzw+xyzw-2xyzw+xyzw-2xyzw+4xyzw-2xyzw
+6xyzw-12xyzw+6xyzw-6xyzw+12xyzw-6xyzw+2xyzw-4xyzw+2xyzw+2xyzw-4xyzw+2xyzw-xzw
-6xyzw+16xyzw-12xyzw+2xyzw+xzw+6xyzw-21xyzw+19xyzw-4xyzw-2xyzw+10xyzw-10xyzw
+2xyzw+xzw-3xyzw+4xyzw-xyzw-xzw+7xyzw-9xyzw+2xyzw-5xyzw+6xyzw-xyzw+xyzw-2xyzw
+xyzw-3xyzw+6xyzw-3xyzw+3xyzw-6xyzw+3xyzw-xyzw+2xyzw-xyzw-xyzw+2xyzw-xyzw+2xzw
+3xyzw-12xyzw+12xyzw-3xyzw-2xzw-3xyzw+20xyzw-23xyzw+6xyzw+xyzw-12xyzw+14xyzw
-3xyzw-2xzw+5xyzw-7xyzw+2xyzw+5xzw-12xyzw+22xyzw-10xyzw+yzw-2xzw+9xyzw-20xyzw
+10xyzw-yzw-2xzw-3xyzw+2xyzw+xzw+5xyzw-3xyzw-xzw+2xyzw-3xyzw+xyzw+xzw-5xyzw
+7xyzw-2xyzw+4xyzw-5xyzw+xyzw+xzw-2xyzw+3xyzw-xyzw-5xzw+5xyzw-13xyzw+8xyzw
-yzw+3xzw-4xyzw+15xyzw-10xyzw+yzw+xw-xyw+3xzw+4xyzw-3xyzw-5xzw-7xyzw+9xyzw
-2yzw+zw+xz+xyz-xyz-xz-2xyz+2xyz-x+xy-xz-xyz+xyz+3xz+2xyz-4xyz+yz-z+1**

Lastly, after re-writing the terms back into alphabetic order, I sliced up the equation into smaller pieces that could be 
inputted into WolframAlpha to get simplified snippets that were basically re-glued back together... Eventually my arithmetic 
equivalent of **(w AND NOT(x) AND NOT(y)) OR (w AND NOT(z)) OR (NOT(w) AND x AND z) OR (NOT(w) AND y) OR (x AND y) OR (NOT(x) AND NOT(z))** 
resulted in an algebraic equivalent of **4wxyz-wxy-3wxz+wx-2wyz+wz-2xyz+xy+2xz-x+yz-z+1**; which, when I tested it against my original 
truth table for Segment A, plugging in values of 0 or 1 for the sixteen different possibilities from 0000 to 1111 yielded the exact
results I had determined...

Now I just had to repeat this process with the other six segments... [Sigh]

**NOTE 2: The concept illustrated above was with respect to an earlier instance of determining the equations for displaying all 16 possible combinations for a hexadecimal display, and so the equation used is with respect to the values from 0-F... However, this same concept can be used for converting values 0 to 9 from their binary equivalents to LCD. I have determined the actual equations for decimal to LCD in the Digits.xlsx file and the "equations for digits.txt" text file located in this folder. Enjoy!**
