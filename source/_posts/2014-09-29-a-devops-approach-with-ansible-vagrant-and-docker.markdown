---
layout: post
title: "DevOps, Systems Administration, and the Culture of Fear"
date: 2014-09-29 13:09:28 +0100
comments: true
categories: 
---

Today at the [HARNESS project](http://www.harness-project.eu) integration team
meeting I gave a brief 
[presentation](http://www.slideshare.net/marklee1977/devops-39666655) wherein I 
discussed [DevOps](http://en.wikipedia.org/wiki/DevOps) and some of my favorite 
tools, namely [Ansible](http://ansible.com), [Vagrant](http://vagrantup.com), 
and [Docker](http://docker.io).

The central thesis of my talk was that a modern devops approach, based in
version-controlled configuration management implemented using industry-standard
deployments tools and continuous integration and rolling updates is a must for
creating robust distributed systems with lots of moving parts. Interactive
configuration of cloud systems and virtual machines leads to unreproducible,
poorly documented, error-prone "brittle" systems. It is the equivalent of
collaborative authoring of documents by emailing word processor files, or
software version control by having a backup/ directory containing
numbered/dated zip archives. This leads to a "culture of fear" wherein people
are afraid to try to fix known problems for fear of "making things worse", and
every roll out of of a new api or set of features becomes a dreaded nightmare.

Overall, the talk was well received. We also discussed the Vagrant workflow, and
how, while Docker can be used to provision containers that function the same
way as "lightweight" virtual machines, ideally each container should really
only be running the minimum number of processes, following the single
responsibility principle.

