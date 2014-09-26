---
layout: post
title: "Creating Web Pages with Template Render"
date: 2013-06-22 18:41
comments: true
categories: 
---

I'm very fond of static website generators--they're a lot faster than
interpreted (php, ruby) or even most compiled (Java) based solutions. I'm using
[Octopress][1] to manage this blog currently, it's basically a set of
customizations, style files, and build scripts that extend the capabilities of
[Jeckyll][2], a comprehensive platform in its own right. I've also tried out the
node.js based [Docpad][3] and the pythonic [Hyde][4] and [Pelican][5].

While these all do a fairly good job of managing a standard blogging sites, in
my opinion they all suffer from trying to do a bit too much, to manage the whole
job of website creation, which makes it difficult to decouple small, specific
bits of functionality, like rendering just a few web pages from templates. Also,
I never really got into the [Jinja2][6] templating language: I don't like the
way that it (doesn't) handle inline code, or the fact that every call to a macro
has to be in the form of a *block*, or that every macro definition needs to do
something with the content included in the block, even if you didn't want to use
that feature. I much prefer the style of [Mako][7], but none of the popular
static website generators seem to support it.

For the Department's digital sign project, I just needed something that would
rending a single, but fairly complicated, web page in a way that would be easy
for me to customize, and I didn't want to deal with the overhead of a lot of
features aimed at things like blogging that I never intended to use. My first
thought was to hack something together with _Make_ and the mako-render script
that came packaged with Ubuntu, but whereas the static website generators did
more than I wanted, this seemed a little too simple. So, naturally, I decided to
hack together something on my own.

<!--more-->

In point of fact the mako template library makes it very easy to invoke the
rendering engine once you give it a few basic configuration parameters, so most
of the script is just parameter passing. Jinja2 is similarly easy, so I added
another function and threw it in as well, changing the name of the project from
mako-render2 to template-render. In the future I plan to add a few more features, 
such as support for Jeckyll-like Yaml front matter, while keeping the focus 
solidly on the task of rendering individual webpages.

User Manual
===========

    usage: template-render [-h] [-i INPUTFILE] [-e ENGINE] [-d TEMPLATE_DIRS]
                           [-t TEMPLATE] [-b BLOCK] [-m METAFILE] [-v VAR]
                           [-o OUTPUTFILE] [-ienc INPUT_ENCODING]
                           [-oenc OUTPUT_ENCODING]

    Render a file using templates.

    optional arguments:
      -h, --help            show this help message and exit
      -i INPUTFILE, --inputfile INPUTFILE
                            input file
      -e ENGINE, --engine ENGINE
                            templating engine
      -d TEMPLATE_DIRS, --template_dirs TEMPLATE_DIRS
                            : delimited template search path
      -t TEMPLATE, --template TEMPLATE
                            template to apply to input file
      -b BLOCK, --block BLOCK
                            template block to override
      -m METAFILE, --metafile METAFILE
                            metadata file in yaml format
      -v VAR, --var VAR     name=value pairs to be added to metadata
      -o OUTPUTFILE, --outputfile OUTPUTFILE
                            output file
      -ienc INPUT_ENCODING, --input_encoding INPUT_ENCODING
                            input encoding
      -oenc OUTPUT_ENCODING, --output_encoding OUTPUT_ENCODING
                            output encoding


The most important parameter is "-t", which is used to tell the script what
template should be used for rendering. The script also supports inclusion of
content from either a file or stdin with the "-i" option ("-" for stdin), and
this content is inserted into the block named with "-b". The "-d" option can be
used to provide a ":"-delimited search path for template files. By default the
script assumes mako format templates, but it also supports jinja2 templates
using the command line option "-e jinja2". Support for additional template
engines may be added in the future.

Templates are given access to a "meta" python hash that contains values supplied
by either yaml-format metadata files specified using the "-m" option or using
"-v" name=value pairs. The metadata files are read in the order given on the
command line, then the name=value pairs are applied, also in command line order.
So, it is possible to supply a site-wide metadata file and then override values
for specific pages. 

By default the script outputs the rendered document to stdout, but a filename
can also be given using the "-o" option. The script reads and writes utf-8 by
default, but these can be overridden using the "-ienc" and "-oenc" options
respectively.

Now, I just need a few more scripts to create my blog pages and manage the
build...

[1]: http://octopress.org/ "Octopress: A blogging framework for hackers"

[2]: http://jeckyllrb.com/ "Jeckyll"

[3]: http://docpad.org/ "Docpad"

[4]: http://hyde.github.com/ "Hyde: A static website generator"

[5]: http://blog.getpelican.com/ "Pelican Development Blog"

[6]: http://jinja.pocoo.org/ "Jinja2"

[7]: http://www.makotemplates.org/ "Mako Templates for Python"
