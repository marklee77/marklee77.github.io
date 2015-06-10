---
layout: post
title: "Ansible deployment of Ansible-provisioned Docker containers"
date: 2014-10-05 20:58:26 +0100
comments: true
categories: 
  - devops
  - ansible
  - docker
---

I've been working with [Ansible](http://ansible.com) quite a lot for the past
year, but until this weekend I really hadn't had much of a chance to get
acquainted with [Docker](http://docker.io). There's a lot of talk on the
Internet about how these two different pieces of technology "naturally"
compliment each other, but clearly there's also a lot of confusion as well, and
there are still a few hard interface problems that need to be sorted out.

When I first hear that Ansible could be used to both provision and deploy
Docker containers, I naturally assumed that this meant I'd be able to adapt my
existing Ansible workflow, possibly with the addition of some new modules to
use, and have access to the power of Docker, but this hasn't proven to be the
case *at all*.

In fact, the two functionalities, deployment and provisioning, are completely
disconnected from each other. To build images and deploy containers there are
two new modules: docker_image and docker, that work pretty much like you'd
expect. It should be noted that the docker_image module can really only be used
to build images using existing Dockerfiles, and in fact there is little that it
can to do actually affect the internal workings of the image. To provision,
that is configure, an image, an entirely different approach is required: the
playbook or role needs to be copied into the container filesystem and then run
as a deployment to localhost. The problem with this, of course, is that this
makes it very difficult to coordinate configuration and state information. This
probably has a lot to do with the approach to containerized deployment
emphasized by Docker, wherein images are relatively static, and configuration
is done at deploy time by running scripts in linked containers. This may even be 
the best way to do things, but it does make it difficult to adapt existing 
playbooks and roles to work with docker.

<!--more-->

For the past few days I'd been contemplating an approach based on three
separate git repositories for each project: one repository for the ansible
playbook to do the provisioning, one repository for a Dockerfile that pulls in
the first playbook to build the image, and a final repository for the ansible
playbook that manages the deployment to remote target systems. This is not
completely unreasonable for small projects, but it would quickly become
unwieldy for something with a lot of moving parts and subprojects, like, say,
[OpenStack](http://openstack.org). At some point it occurred to me that the
repositories containing the configuration playbook and Dockerfile could be
combined, and from there it was a small (but fiddly and error-prone) step to
figure out an approach that would let me keep every sub project in a single
repository.

My first project of this type is an extension of my existing role to deploy
[MariaDB](http://mariadb.org) on [Ubuntu](http://ubuntu.com):
<https://github.com/marklee77/ansible-role-mariadb>. This project is
organized as an ansible role, suitable for inclusion in larger projects. By
default, the role deploys MariaDB in the traditional way, but if Docker is
available on the target then the role can perform a sort of ju-jitsu maneuver,
copying itself to the target system and then instructing the target to use the
"standard" mode of the remote copy to build a local image. There are still
a few things to be worked out, like the best way to manage persistent state and
ensure that containers are restarted on reboots, but I think it's a clever bit
of work and I'm really quite happy with it.

Suggestions, comments, and (on-topic) rants are welcome below.
