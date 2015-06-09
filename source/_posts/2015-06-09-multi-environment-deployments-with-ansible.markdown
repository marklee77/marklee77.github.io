---
layout: post
title: "Ansible Multi-OS Playbooks"
date: 2015-06-09 13:26:28 +0100
comments: true
categories: 
  - ansible
  - devops
---

{% raw %}

Lately I've been tinkering with some of my Ansible roles to improve the support
for multiple-environment deployments. Previously, I'd been using a somewhat
naive approach of simply including environment-specific task lists using
conditionals in tasks/main.yml like so:

``` yaml
- include: ubuntu-install.yml
  when: ansible_distribution == "Ubuntu"

- include: centos-install.yml
  when: ansible_distribution == "CentOS"
```

However there are a number of problems with this approach: 1) it requires
keeping and maintaining separate task lists for each environment, 2)
deployments to different environments are done sequentially rather than in
parallel, and 3) it pollutes the output from ansible with lots of skipped tasks
for unused environments.

In general, it is better to use environment-specific variables with the same
task list for different environments, this reduces the maintenance overhead,
allows for parallel deployment, and reduces the size of the output.

<!--more-->

Environment-specific variables can be loaded by using "include_vars" and
"with_first_found" as in the following preamble:


``` yaml
- include: ubuntu-install.yml
- name: gather os specific variables
  include_vars: "{{ item }}"
  with_first_found:
    - files:
        - "{{ ansible_distribution|lower }}-{{
              ansible_distribution_version }}.yml"
        - "{{ ansible_distribution|lower }}-{{
              ansible_distribution_release }}.yml"
        - "{{ ansible_distribution|lower }}-{{
              ansible_distribution_major_version }}.yml"
        - "{{ ansible_distribution|lower }}.yml"
        - "{{ ansible_os_family|lower }}.yml"
        - defaults.yml
      paths:
        - ../vars
```

Note that this allows for graceful fallback to support different environments
in the same category; For an Ubuntu Trusty system, Ansible would look in the
vars/ subdirectory, and load the first vars file found from the following
sequence: "ubuntu-14.04.yml", "ubuntu-trusty.yml", "ubuntu-14.yml",
"ubuntu.yml", and, finally, "debian.yml".

There is still a problem that different environments may require slightly
different build steps. Consider the steps for installing nova compute on
different operating systems. On Ubuntu trusty and precise, the steps are
basically the same, *except* that on precise it is necessary to first enable
the ubuntu cloud archive. On CentOS, not only are the package names different,
but we have to use the yum package manager instead of apt. Also, there are
a number of prerequisite packages that need to be in place before even trying
to install anything from openstack.

There aren't always perfect answers for every situation, but if the steps for
the different environments can be represented at a reasonable level of
abstraction then it may be possible to take advantage of the "action" keyword
to come up with a solution. For example, rather than "install apt packages" and
"install rpms", a generic "install packages" step that could be run in parallel
on different environments might look like this:

``` yaml
- name: ensure required packages are installed
  action: "{{ package_info.pkg_mgr }}"
  args: package_info.args
  with_items: package_info.pkgs
  when: package_info.pkgs|length > 0
```

The "when" keyword above is critical for steps where some environments may need
to install a set of packages, but others won't. For several of my roles there
are steps that look like the above, except there is also
a "package_info.pre_pkgs" step to install prerequisites, and it isn't hard to
imagine that for some deployments it might be necessary to have
a "package_info.post_pkgs" as well.

I could include more details, but if you've gotten this far then you may as well 
take a look at some of the multi-environment roles that I have on github:

- [ansible-role-mysql](https://github.com/marklee77/ansible-role-mysql)
- [ansible-role-keystone](https://github.com/marklee77/ansible-role-keystone)
- [ansible-role-nova-compute](https://github.com/marklee77/ansible-role-nova-compute)

The docker role supports a wide variety of environments, the keystone role only
supports Ubuntu trusty and precise (though if someone wanted to submit a pull
request I wouldn't complain), while the ansible-role-nova-compute role supports
Ubuntu trusty and CentOS 6.5 (due to a requirement at work). If anyone has any
criticisms or suggestions for a better way of doing things, then please feel
free to email me or comment below.

{% endraw %}
