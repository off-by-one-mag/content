---
title: ???
subtitle: ???
todos:
  - conclusion
  - regex section
  - bad data section
  - string find section
  - better intro
  - example code
  - proof
---

HTML is difficult to escape; before too long, most any task will involve
parsing, generating, or manipulating the markup. Let's imagine that we wanted
to build a browser plugin which automatically inserted links to relevant
definition, citations, etc. throughout the document. At what level of
abstraction should we process the document? Let's consider four possibilities.

## OOP All The Things

Isn't this sort of manipulation what the **D**ocument **O**bject **M**odel is
for? We're trying to avoid modifying the text of attributes, tags, and the
like, but we can certainly iterate over all of the _text_ content on the page
and replace as needed, right? Here, it's a good idea to think about what form
the HTML is in at the moment our plugin runs, and what form it should be in
once we finish.

If our environment is a running browser, we most likely need to only think in
terms of the DOM. Sans implementation details, this is more or less the
representation that the browser uses to track the content. By modifying the
DOM structures, we're effectively modifying the in-memory data.

If instead, we are acting as a _string_ filter, parsing and transforming the
data into the DOM may well be overkill. Shuttling data between representations
can take a lot of time. What are other options?

## Regular Expressions

As we have a string problem, we probably want to reach for regular
expressions. This rule engine is wonderful, _assuming_ we can determine the
correct rules. Thus far we can describe our needs as a search and replace
which doesn't affect attributes, tags, and other HTML markup. Luckily, the
markup is defined to use specific delimiters (angle brackets). If we can
assume that the HTML we're processing is using tags correctly (see the last
section), we simply need to look for text which is _not_ between open and
close brackets.

```
>[^<]*<
```

This achieves our end, search for all text that (1) follows a greater-than,
(2) is followed by a less-than, and (3) contains no less-thans (i.e. does not
contain the beginning of the next tag). We'd most likely want to split the
string into chunks, one set representing this "in-between" text and an
interleaved set representing the tag content. The logic here is a bit hard to
follow, however, as we're searching for a negation. Instead, we could search
for the _tags_ and process all _other_ text.

```
<[^>]*>
```
We'd continue to split the text as before, but modifying only the strings
which were in-between our matches (rather than the matches themselves). 

While this approach is significantly faster than parsing the HTML properly, we
can write a faster, equivalent algorithm based entirely on string search.
