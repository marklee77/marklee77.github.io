---
layout: post
title: "HARNESS Software Carpentry Bootcamp Retrospective"
date: 2015-09-04 14:34 +0100
comments: true
categories: 
---

![Alex Wolf Keynote](/images/20150715_143559.jpg)

In July 2015, the HARNESS project hosted a Software Carpentry bootcamp at the
SAP headquarters in Feltham, near London Heathrow. In addition to the standard
topics on task automation, version control, and programming in python, there
were additional modules on cloud computing, FPGA data-flow engines, and
distributed file systems. Representatives from both the Software Sustainability
Institute and the European Grid Infrastructure attended to provide additional
teaching support. The full list of topics covered was as follows:

- [Automating Tasks with the Unix Shell](https://swcarpentry.github.io/shell-novice/) (Alistair Grant, SSI)
- [Version Control with Git](https://swcarpentry.github.io/git-novice/) (Alistair Grant, SSI)
- [Building Programs with Python](https://swcarpentry.github.io/python-novice-inflammation/) (Mark Stillwell, HARNESS, ICL)
- [Managing Cloud Services with ConPaaS](https://harnesscloud.github.io/2015-07-15-feltham/conpaas/slides.pdf) (Guillaume Pierre, HARNESS, UR1)
- [Dataflow Programming with Maxeler](https://harnesscloud.github.io/2015-07-15-feltham/maxeler/CodeCarpentry-MaxelerDataflow1.pdf) (Peter Sanders, HARNESS, Maxeler)
- [EGI Federated Cloud for Open Science](https://documents.egi.eu/document/2551) (Diego Scardaci and Gergely Sipos, EGI)
- Distributed Filesystems with XtreemFS (Christoph Kleinweber, HARNESS, ZIB)

The first three modules; *Automating Tasks with the Unix Shell*, *Version
Control with Git*, and *Building Programs with Python*; were drawn from the
standard Software Carpentry curriculum, while three of the remaining four
modules were developed specially for this course to showcase HARNESS-related
technologies. In *Managing Cloud Services with ConPaaS*, Guillaume Pierre from
UR1 lead the students through using the [ConPaaS](http://conpaas.eu) web
service interface to deploy a WordPress blog. In *Dataflow Programming with
Maxeler*, Peter Sanders from Maxeler discussed how FPGA data flow engines
achieve computational speedup over conventional CPUs despite their lower Mhz
ratings by allowing users to create deep processing pipelines, and showed
students how to develop custom FPGA configurations using the Maxeler IDE. In
*Distributed Filesystems with XtreemFS*, Christoph Kleinweber from ZIB
explained the differences between networked and distributed filesystems and
showed students how to build a storage solution using XtreemFS. Finally, in
*EGI Federated Cloud for Open Science* Diego Scardaci and Gergely Sipos from
EGI, a separate project with collaborative ties to HARNESS, demonstrated how
students could access the EGI federated cloud, a multi-site research oriented
and publicly funded cloud targeted at computational researchers in the European
Union.

There was also a keynote on the topic of Heterogeneous Accelerators in the
Cloud given by Professor Alexander Wolf (HARNESS, ICL), and a demonstration of
the HARNESS cloud platform given by Gabriel Coutinho (HARNESS, ICL).

<!--more-->

## Response

This was a popular and well attended event. It was advertised primarily through
the web and social media, including facebook, twitter, LinkedIn, and various
targeted email lists. By the start of the event, 42 people had signed up and
there was growing waiting list. Of these, 25 people attended, which was
a slightly higher drop-off than expected, possibly due to difficulties in
reaching the location via public transport.

The informal feedback offered during the bootcamp was almost uniformly
positive. Overall, the learners seemed happy with the content and breadth of
topics covered. A number of students expressed that they were very happy to
have the opportunity to take a higher-level survey of recent developments and
cutting-edge technologies, even when there might not be immediate applications
for their particular research area. The attendees were particularly
enthusiastic about the keynote, and even though teaching ran late on the last
day, a solid majority remained to watch the demonstration of the HARNESS
technology preview.

## Survey Results

Those who signed up for the event were asked to complete pre-event surveys,
while those who actually attended were asked to complete post-event surveys as
well. The purpose of the pre-event surveys was to assess information about the
background and relative level of knowledge of those who would be attending in
order to properly target the lessons. The purpose of the post-event surveys was
to determine level of satisfaction and take on feedback that to improve the
lessons and future events.

Of the 42 people who signed up, 25 submitted responses to the pre-event survey,
which was a higher response rate than expected. Interestingly, 4 people who
attended the event did not respond to the survey, and similarly, 4 people who
responded to the survey did not attend the event. Most of the survey
respondents were researchers associated with universities in England, though
one was from Belfast and another from Italy, and three others remain of
indeterminate origin. There were 8 responses from females and 17 from males,
and, with only a few exceptions, those who responded appeared to be at
a relatively early career stage.

Of the 25 people who attended the boot camp, 10 responded to the post-event
survey, and all of these had previously responded to the pre-event survey.
There was some variation in the satisfaction with individual lessons, but none
stood out as being particularly unsatisfying to the students. The overall level
of reported satisfaction was good, with 7 out of 10 responses indicating that
the attendee was "satisfied" or "very satisfied" with the event, and only one
person reporting that they were "unsatisfied". Similarly, 8 out of 10 attendees
reported that they were "likely" or "very likely" to recommend similar events
to colleagues in the future, while only one person reported that they were
"unlikely" to recommend this or similar events.

## Conclusions

Overall, we were happy with the number of attendees and we believe that the
interest and level of engagement displayed were much higher than if we had
simply presented HARNESS in a more typical venue, such as a workshop or trade
show. The drop-off from 42 (our maximum capacity) signed up, to 25 attending
was disappointing, and likely had to do with difficulty in reaching the
location, so we will take this problem into account when planning similar
events in the future. There were no major technical glitches that ruined an
entire lesson, but there were some delays caused by improperly configured
software, and one of the cloud services did not respond well to the load it was
given, thus demonstrating the need to allocate sufficient time for debugging
and thoroughly test in advance.
