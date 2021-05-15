# Literal number types

This is an **subjective and highly condensed** summary of the various discussions on the Python-ideas list about having different literal number types.  The Python-ideas list exists since December 2006 whereas other lists (python-list, python-dev) have existed for much longer and similar discussions may have taken place at earlier times on those lists.

### Summary of the summary:

The suggestions that were most often made were to add a literal decimal either

* as a temporary step to eventually replace normal (binary) floats by decimals as the default in Python
* as an permanent additional syntax that would simplify writing (and reading) code dealing with decimals.
  * a similar suggestion has been made to introduce a syntax notation for rational (`fraction`) numbers.

The suggestions to have **decimals as default** are usually based on some advantage of decimals over floats as they are now documented on the Python site and reproduced in the next section. Since these arguments are well documented, I have focused on extracting the reasons for **not having decimals as default** as they were made in discussions on the Python-ideas list, since these counter-arguments are not documented.

The main reasons given for not going supporting having decimals instead of floats as the default were

* calculations performed with decimals are much slower than those with floats
* the ability to prototype float point based calculations in Python and, if needed for speed, translate to C or Fortran without needing to worry about the type.
  * related to this, interoperability with existing Fortran or C libraries (for example, with `numpy`) essentially require the use of floats.
* while the precision for decimals is configurable, its API is rather complex which contradict the idea that decimals would be friendlier to beginners than normal binary floats.
* Unlike other number types, including `fractions.Fraction`, there is no implicit conversion of decimals to floats when they are combined.

Finally, it is unclear that the need of decimals is really wide spread. A possible indicator might be having a large number of people regularly including something like

```python
from decimal import Decimal as D
# use D() as a convenient shorthand syntax
```

in packages uploaded to pypi.org.



## From the [Python documentation](https://docs.python.org/3/library/decimal.html)

The [`decimal`](https://docs.python.org/3/library/decimal.html#module-decimal) module provides support for fast correctly-rounded decimal floating point arithmetic. It offers several advantages over the [`float`](https://docs.python.org/3/library/functions.html#float) datatype:

- Decimal “is based on a floating-point model which was designed with people in mind, and necessarily has a paramount guiding principle – computers must provide an arithmetic that works in the same way as the arithmetic that people learn at school.” – excerpt from the decimal arithmetic specification.

- Decimal numbers can be represented exactly. In contrast, numbers like `1.1` and `2.2` do not have exact representations in binary floating point. End users typically would not expect `1.1 + 2.2` to display as `3.3000000000000003` as it does with binary floating point.

- The exactness carries over into arithmetic. In decimal floating point, `0.1 + 0.1 + 0.1 - 0.3` is exactly equal to zero. In binary floating point, the result is `5.5511151231257827e-017`. While near to zero, the differences prevent reliable equality testing and differences can accumulate. For this reason, decimal is preferred in accounting applications which have strict equality invariants.

- The decimal module incorporates a notion of significant places so that `1.30 + 1.20` is `2.50`. The trailing zero is kept to indicate significance. This is the customary presentation for monetary applications. For multiplication, the “schoolbook” approach uses all the figures in the multiplicands. For instance, `1.3 * 1.2` gives `1.56` while `1.30 * 1.20` gives `1.5600`.

- Unlike hardware based binary floating point, the decimal module has a user alterable precision (defaulting to 28 places) which can be as large as needed for a given problem:

  ```
  >>> from decimal import *
  >>> getcontext().prec = 6
  >>> Decimal(1) / Decimal(7)
  Decimal('0.142857')
  >>> getcontext().prec = 28
  >>> Decimal(1) / Decimal(7)
  Decimal('0.1428571428571428571428571429')
  ```

- Both binary and decimal floating point are implemented in terms of published standards. While the built-in float type exposes only a modest portion of its capabilities, the decimal module exposes all required parts of the standard. When needed, the programmer has full control over rounding and signal handling. This includes an option to enforce exact arithmetic by using exceptions to block any inexact operations.

- The decimal module was designed to support “without prejudice, both exact unrounded decimal arithmetic (sometimes called fixed-point arithmetic) and rounded floating-point arithmetic.” – excerpt from the decimal arithmetic specification.

The module design is centered around three concepts: the decimal number, the context for arithmetic, and signals.


## May 2008

The first mention of using a letter (d for decimal) as an indicator of a different number data type was made by [Leonardo Santagada](https://mail.python.org/archives/list/python-ideas@python.org/message/GRILJYKAUWUHXFVX72BTE3VUZMJUKGFG/) in a short thread (total of 4 contributions) about [Decimal as default ](https://mail.python.org/archives/list/python-ideas@python.org/thread/BR54HSJDQZ5B2ULUIHKAIVCUMX3WS6FR/). However, this idea was not discussed further on that thread other than Guido van Rossum mentioning the following:

>  Not in 3.0, there are too many things that are subtly different. Perhaps at some point post 3.0 we can invent a mechanism whereby modules can enable this feature on a per-module basis, and then some number of revisions later we can change the default.

## December 2008

The idea discussed in May was revived by Chris Rebert in the [Decimal literal?](https://mail.python.org/archives/list/python-ideas@python.org/thread/YVWZDMIQEAQVCE7RYIVKGR3KE6EPYPNR/) thread, with 33 comments from 13 participants. In the first email, the following was proposed by Rebert:

1. making `decimal.Decimal` a built-in type, named "decimal" (or "dec" if that's too long?)
2. adding a literal syntax for decimals; the suggestion was to add a 'd' suffix to the float literal syntax, in accordance with the suggestion made in May 2008.
3. (in Python 4.0/Python 4000) making decimal the default instead of float, with floats instead requiring a 'f' suffix

During this discussion, various arguments were made supporting this idea. As mentioned above, most of these arguments can be found on the Python documentation about decimals and will not be repeated here. I am just attempting to identify the first time an important point was raised.

[Raymond Hettinger immediately replied](https://mail.python.org/archives/list/python-ideas@python.org/message/SBPVJAOETWLZVWD3AKNL3SPLW23MM6DS/) 

> It's a non-starter until there is a fast, clean C implementation of decimal. The current module is hundreds of times slower than binary floats.

A side discussion about speed followed during which [Leif Walsh wrote](https://mail.python.org/archives/list/python-ideas@python.org/message/I3DTUZO3FEIZALNM6FMML3R3QKRJXO2W/) 

> Including support for something like '1.1d' requires that we include the decimal module in builtins.  Now, I don't know that there's no way around this, but it seems like a slowdown for everyone just to let a few people type a bit less. 

[Adam Olsen also added](https://mail.python.org/archives/list/python-ideas@python.org/message/U4FB2VUVHMFQUJ53VQMDDSSBXTOGCKGD/) (about preferring normal floats to decimals) 

> Given the importance of C extensions to Python, interacting with C is the strongest argument here.  It's not an elegant reason, but it's very practical.

Observation: one of the reasons the suggestions of replacing floats by decimals as default seems to often be based on the belief that decimals are more accurate than floats. On that topic, [Terry Reedy wrote](https://mail.python.org/archives/list/python-ideas@python.org/message/SEMJYC62RNCS7GNVGJDCLUJ7QEVQBFKF/) 

> We have one by many definitions of 'accurate'.  Being off by a few or  even a hundred parts per quintillion is pretty good by some standards. ... The notion that decimal is more 'accurate' than float needs a lot of  qualification.  Yes, it is intended to give *exactly* the answer to  various financial calculations that various jurisdictions mandate, but  that is a rather specialized meaning of 'accurate'.

[Raymond Hettinger furthermore added](https://mail.python.org/archives/list/python-ideas@python.org/message/VI2DWAKW6RIYSST66X2DQ2KCM3DIQTL6/) 

> While we're mincing words, I would state the case differently. Neither "precision" or "accuracy" captures the essential difference between binary and decimal floating point.  It is all about what is "exactly representable". The main reason decimal is good for financial apps is that the numbers of interest  are exactly representable in decimal floating point but not in binary floating point. In a financial app, it can matter that 1.10 is exact rather than some nearby value representable in binary floating point, 0x1.199999999999ap+0.
> .
> Of course, there are other differences like control over rounding and variable precision, but the main story is about what is exactly representable.

[Cesare di Mauro mentioned](https://mail.python.org/archives/list/python-ideas@python.org/message/ZSGXZA53VMT6ISFW4ZVJJVQDB7KBMKO5/) that having decimal literals (instead of explicit function calls) could help improve speed.

[Jim Jewett mentioned](https://mail.python.org/archives/list/python-ideas@python.org/message/XP27DJZU6GNWCFMHL7QLSHZC3TR75TEP/) that using something like `a = 1234.567d` could be misinterpreted as indicating that `d` is a double-precision variable. [Marcin Kowalczyk](https://mail.python.org/archives/list/python-ideas@python.org/message/GNJ54EFG66THWZUEL2HKLI5GOPQHGQVP/) pointed out that C# uses `m` or `M` instead of `d` based on the mnemonic "money".

Note: in addition to the comment about `d` being possibly misinterpreted, Jim Jewett also wrote:

> My own hunch is that until Decimal is used enough that people start putting this sort of constructor into their personal libraries, it probably doesn't need a literal.

[Terry Reedy wrote](https://mail.python.org/archives/list/python-ideas@python.org/message/Q4FCP4J2TQEHEYJCRPD4VWQOFUWPDAEU/):

> There is an advantage of binary floats that you missed.  One can  prototype float functions in Python and then translate as necessary for  real speed to C  and get the same results (using the same compiler on  the same hardware).  But even prototypes need to run faster than  molasses. One can also use Python to glue together C (or Fortran) double  routines without translating the numbers.  The numerical module (now `numpy`) is over a decade old and was, I believe, Python's first killer app.

He also added, with regards to using `d` to denote a literal decimal, that perhaps `f` should be used for literal fractions:

> I would just as soon do the same for `fractions.Fraction`, perhaps 1 f/ 2  or 1///2.  Even with decimal literals, the functions would remain in the  importable module, just as with `math` and `cmath`.

## March 2009

A very brief tread [float vs decimal](https://mail.python.org/archives/list/python-ideas@python.org/thread/FYKVPCSLXZ3IXR3I2DWYHVAKXSGPNJUO/) [4 comments, 2 participants] on this topic, with no significant new argument made supporting a new syntax..



## Sept 2012

[A brief thread](https://mail.python.org/archives/list/python-ideas@python.org/thread/3IQDAQGVMPJBJRJXJUETAXJ2HE6AHFNL/) [5 comments, 5 participants], that had apparently started on python-dev, spilled over October.

[Calvin Spealman wrote](https://mail.python.org/archives/list/python-ideas@python.org/message/7TAOGNAQC6B2ASNHUNY3QH574JLBUHBN/)

>  I think learning Integer -> Decimal -> Float is a lot more natural than learning Integer -> Float -> Decimal. The Float type represents a specific hardware acceleration with data-loss tradeoffs, and the use should be explicit. I think that as someone learns, the limitations of Decimals will make a lot more sense than those of Floats.

This was countered by [Guido van Rossum](https://mail.python.org/archives/list/python-ideas@python.org/message/4UIEBMKG4UCKKIVHK73HSG5XH462FOTF/) as follows:

> Remember decimals have data loss too: they can't represent 1/3 any more accurately than floats can, and like floats they are limited to a certain number of digits after which they begin dropping precision even if the result *can* be represented exactly. It's just that they can represent 1/5 exactly, which happens to be culturally important to humans, and that the number of digits at which loss of precision happens is configurable. (And the API to configure it may actually make it more complex to learn.)



## August 2013

The discussion started by Oscar Benjamin [isinstance(Decimal(), Real) -> False?](https://mail.python.org/archives/list/python-ideas@python.org/thread/KOE3MQ5NSMGTLIH6IHAQWTIOELXG4AFQ/) [13 comments, 8 participants] is shown with a separate [second thread](https://mail.python.org/archives/list/python-ideas@python.org/thread/BTBJE3DDSYQKOGA3JEGPQTRFLRNJVMSA/) [17 comments, 6 participants] which contains relevant points about incompatibility of decimals and other floats.  

The firs thread started with this observation by Oscar Benjamin found in [PEP 3141](https://www.python.org/dev/peps/pep-3141/#the-decimal-type):

> After consultation with its authors it has been decided that the `Decimal` type should not at this time be made part of the numeric tower.

After a few comments, we find this observation by [Paul Moore](https://mail.python.org/archives/list/python-ideas@python.org/message/JVWSSDH5A7HIYTKSVKRWAU2KHEWDHE5B/)

> The difference is that there is no implicit conversion from Decimal to float:
```python
>  from decimal import Decimal as D
> D('1.5') + 2.3
> Traceback (most recent call last):
> File "<stdin>", line 1, in <module>
>   TypeError: unsupported operand type(s) for +: 'Decimal' and 'float'
> from fractions import Fraction as F
> F(1,2) + 2.3
> 2.8
```

Eventually, [Oscar Benjamin noted](https://mail.python.org/archives/list/python-ideas@python.org/message/SMR7MLB6WBF2IMT6DJYPSRYHVDSRQMLL/):

> Decimals are effectively stored internally as integers. They won't be processed by the FPU which operates on binary floating point types.

These two related threads contain many long comments that may be relevant to those interested in mathematical details between representations and handling of floats vs decimals in Python. Here's an attempt at an utterly incomplete and very selective summary of two main points (**with apologies for giving proper attributions**):

* normal (binary) floats are approximations of real numbers using base 2 with an implicit precision, (by using fixed number of decimals), which matches the architecture of modern CPUs.  ["Practicality beats purity."]
* decimals are approximations of real numbers using base 10, with a default precision (fixed number of decimals) which can be modified by specifying a given "context". Older CPUS, such as CPUs like the Intel 4004 and the IBM 360 expected to do most of their work in Binary Coded Decimal.

On the topic of "context", as noted by [Oscar Benjamin](https://mail.python.org/archives/list/python-ideas@python.org/message/GMJK3G3N4UEJ4K4WRPDPERR4YBM2UWOQ/):

> While it is possible to exactly convert an `int/str/float` to a `Decimal` with a precision that is higher than the current context, any arithmetic operations will be rounded to the context precision according to the context rounding mode (there are 8 different rounding modes and precision is any positive integer). This arithmetic rounding is actually *required* by the IEEE-854 standard unlike the exact conversion from arbitrary precision integers etc.



## March 2014

Extremely long thread which needs to be documented.



This comment by Guido https://mail.python.org/archives/list/python-ideas@python.org/message/S465V3AX52TSAFKW7P23CUX23CUHHKJA/ should definitely be included.





## Other sources

https://discuss.python.org/t/add-decimal128-as-the-builtin-decimal/2116/2

