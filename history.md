# Historical review

Aside for the current discussion about a trim function https://mail.python.org/archives/list/python-ideas@python.org/thread/5DKQ6HHINP6KPRYRWHODE34IZ4CRD4HO/ about a trim string function

This is an attempt to summarize the various discussions on the Python-ideas list about having different literal number types.  The Python-ideas list exists since December 2006 whereas other lists (python-list, python-dev) have existed for much longer and similar discussions may have taken place at earlier times on those lists.

## May 2008

The first mention of using a letter (d for decimal) as an indicator of a different number data type was made by [Leonardo Santagada](https://mail.python.org/archives/list/python-ideas@python.org/message/GRILJYKAUWUHXFVX72BTE3VUZMJUKGFG/) in a short thread about [Decimal as default ](https://mail.python.org/archives/list/python-ideas@python.org/thread/BR54HSJDQZ5B2ULUIHKAIVCUMX3WS6FR/). However, this idea was not discussed further on that thread.

## December 2008

The idea discussed in May was revived by Chris Rebert in the [Decimal literal?](https://mail.python.org/archives/list/python-ideas@python.org/thread/YVWZDMIQEAQVCE7RYIVKGR3KE6EPYPNR/) thread. In the first email, the following was proposed by Rebert:

1. making decimal.Decimal a built-in type, named "decimal" (or "dec" if that's too long?)
2. adding a literal syntax for decimals; the suggestion was to add a 'd' suffix to the float literal syntax, in accordance with the suggestion made in May 2008.
3. (in Python 4.0/Python 4000) making decimal the default instead of float, with floats instead requiring a 'f' suffix



