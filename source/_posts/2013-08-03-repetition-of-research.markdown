---
layout: post
title: "Repetition of Research (Unintended)"
date: 2013-08-03 16:32:50 +0100
comments: true
categories: 
---
Most of us are familiar with the classical narrative of ongoing scientific
progress: that each new discovery builds upon previous ones, creating an ever
upward-rising edifice of human knowledge shaped something like an inverted
pyramid. There's also an idea that, in the semi-distant past of a few hundred
years ago one person could know all the scientific knowledge of his (or her)
time, but today the vast and ever-expanding amount of information available
means that scientists must be much more specialized, and that as time passes
they will become ever more specialized.

There is some truth to these ideas, but there are problems as well: when knew
knowledge is created, how is it added to the edifice? How do we make sure that
future scholars will know about it and properly reference it in their own works?
If a scientist must be incredibly specialized to advance knowledge, then what
does he (or she) do when just starting out? How does one choose a field of
research? And what happens when the funding for research into that area dries
up? Contrary to what we learned in grade school, a scientist cannot choose to
simply study, say, some obscure species of Peruvian moth and spend the next 40
years of summers in South America learning everything there is to know about it
without also spending some time justifying that decision to colleagues and
funding bodies.

<!--more-->

The answer to the first question is: no one, and everyone, and it isn't done
perfectly. New scientific knowledge is published, generally in refereed
conference proceedings and/or journals, but sometimes also online in the form of
research reports or blog posts. The good thing about refereed publications is
that they have been read by the author's (or authors') peers, who have rendered
a judgment that the work is of sufficient quality and novelty to merit adding it
to the official scientific literature. The bad thing is that this process is
slow and prone to errors--referees are human beings, and they work on
a voluntary basis, and they may only be partial experts on the topic of the
article in question. The fact of the matter is that it is extremely likely that
a lot of science is repeated, in fact that it makes sense that a lot of work
gets repeated since it actually takes less work to re-research some answers in
the lab (or, for mathematics, on the chalkboard) than it does to keep track of
every single research output produced by every researcher, everywhere, for all
time.

Let that sink in for a minute: There are costs associated with storing,
indexing, and searching vasts amounts of data. For some problems, for some
researchers, at some time, those costs may actually exceed the cost of simply
re-discovering the findings anew. An additional wrinkle is that the people
making the decision to embark on a new research project by definition do not
know how difficult it will be to properly research the problem, and are strongly
motivated *NOT* to discover that their proposed solution has already been
discovered by someone else (think: graduate students looking for interesting
thesis projects, or untenured junior faculty hoping to pad out their research
portfolio, or anyone submitting a grant proposal), so long as the omission is
not so completely obvious as to be embarrassing.

Consideration of the second point provides some more startling insights: yes,
researchers do become highly specialized in their knowledge, but this
specialization is *NOT* defined by an objective partitioning of the universe of
knowledge into discrete, manageable, non-overlapping subsets, but rather by
what journals or conferences they follow, who their colleagues are, and other
factors like their personal algorithms for sorting and selecting papers from
the constant flood of new information. The primary limitation on scientists is
how many papers they are physically and mentally capable of reading and
assimilating within a given timespan, and this value is likely a constant that
varies only somewhat from individual to individual. Further limiting the
potential for specialization is the fact that every paper ranges over a wide
variety of semi-related topics, in order to increase its chance of being deemed
sufficiently "interesting" and "novel" to warrant publication by a randomly
selected collection of "peers". It is not only possible, but likely, that there
are many different groups of researchers who study similar problems, but never
know about each other because they follow different conferences, and use
differing terminology. Further, they will use different basic assumptions when
designing and evaluating experiments, having different standards for what
constitutes "good" or "bad" solutions to related problems. For an example of
this, I'd like to highlight the almost complete disconnect between groups that
study scheduling algorithms for distributed systems, which take a very
"mathematical" and proof-oriented approach, falling back on studies of
heuristics for very abstract problem formulations when there are no reasonable
guaranteed algorithms, and groups that study "autonomic computing", which take
a much more biologically inspired approach to what is essentially the same
problem, starting from a more common-sense (but less mathematically tractable)
approach to defining success or failure, relying first on heuristics, but often
falling back on biologically inspired approaches like ant-colony optimization
or genetic algorithms.

So, a lot of research gets repeated, and for some people in some situations,
that might not be an entirely bad thing. Still, the situation seems something
less than optimal. Hence, there have been movements later toward not only
greater sharing of research data, both before, and after publication, but also
better sharing of data through the use of standardized, extensible formats like
XML or JSON, and semantic markup of metadata. By using global- and
discipline-standardized *ontologies*, or grammars that define relationships
between syntactic primitives, it is possible to develop general purpose
repositories that nonetheless allow for complicated queries and
discipline-specific reasoning about results. By using these standards and
technologies and applying well-though-out policies for *research data
management*, it should be possible to lower the bar for indexing and searching
the existing body of scientific knowledge (which to those on the inside looks
much less like an edifice and more like a maelstrom).

I plan on covering both the development of data management policies and why they
are a good idea, and enabling technologies for the storage and semantic markup
of data in future posts.
