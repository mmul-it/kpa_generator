kpa_generator
=========================

This role uses a [KPA Project](https://github.com/mmul-it/kpa) to generate
[Marp](https://marp.app/#get-started) and [Pandoc](https://pandoc.org/MANUAL.html) compatible Markdown files usable to
create beautiful presentations in `html`, `pdf` and `ppt` format with their
agenda.
By default it automates the creation of a pdf slides set and their pdf agenda.

[![Lint and test project](https://github.com/mmul-it/kpa_generator/actions/workflows/main.yml/badge.svg)](https://github.com/mmul-it/kpa_generator/actions/workflows/main.yml)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-kpa_generator-blue.svg)](https://galaxy.ansible.com/mmul/kpa_generator)


Role Variables
--------------

To get a minimal slides set you can rely on the defaults, and then change
everything you need to customize your result:

```yaml
---

# Location of your KPA project
kpa_project_dir: "{{ playbook_dir }}"

# Schedule Markedown output file destination
agenda_output_file: "{{ kpa_project_dir }}/slides.schedule.md"

# Marp Markdown output file destination
marp_output_file: "{{ kpa_project_dir }}/slides.md"

# Marp theme (default, gaia, uncover or a custom theme)
marp_theme: default

# Add slide number
marp_paginate: false

# Author & Copyright info
marp_author: 'My author'
marp_copyright: '© 2023 My copyright'

# Cover slide details (set cover: true on marp_slides entry)
# marp_cover_background_image to pass an image path/url
marp_cover_background_color: '#FFCCE1'
marp_cover_template: "templates/cover.md.j2"
marp_cover_header: null
marp_cover_footer: "**{{ marp_author }}** | _{{ marp_copyright }}_"

# Chapter slide details
marp_chapter_background_color: '#FAFFC7'
marp_chapter_template: "templates/chapter.md.j2"

# Common slides background color
# marp_background_image to use an image path/url
marp_background_color: '#D7EEFF'
marp_header: true  # Will show {{ marp_title }} | {{ marp_author }}
marp_footer: true  # Will show {{ slide.chapter }} | {{ slide.title }}

# Slide set title
marp_title: "My slides set"

# Slide list, this is what KPA (https://github.com/mmul-it/kpa) is meant for
marp_slides:
  # Cover slide (doesn't need content:)
  - cover: true
    title: "{{ marp_title }}"
    subtitle: "DAY ONE"
  # First Knowledge Pod
  - chapter: 'DAY ONE - First Knowledge Pod'
    title: 'Topic 1'
    chapter_header: "A custom header for this specific chapter ONE"
    content: "{{ kpa_project_dir }}/contents/knowledge-pod-1.md"
  # Second Knowledge Pod
  - chapter: 'DAY ONE - Second Knowledge Pod'
    title: 'Topic 2'
    chapter_footer: "A custom footer for this specific chapter TWO"
    content: "{{ kpa_project_dir }}/contents/knowledge-pod-2.md"
```

Example Playbook
----------------

To test this role and generate a simple Markdown usable with Marp, just use the
[tests/kpa_generator.yml](tests/kpa_generator.yml)
playbook as it is:

```yaml
---

- hosts: localhost
  gather_facts: false
  connection: local
  name: Use a KPA Project to create a Marp Markdown compatible file
  roles:
    - role: ../..
```

And execute it using `ansible-playbook`:

```console
> ansible-playbook tests/kpa_generator.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Use a KPA Project to create a Marp Markdown compatible file] *******************************************************************************************************

TASK [../.. : Create Marp slides markdown] ****************************************************************************************************************
changed: [localhost]

TASK [../.. : Create schedule markdown] *******************************************************************************************************************
changed: [localhost]

PLAY RECAP ************************************************************************************************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

This will generate two files:

1. A file named [tests/slides.md](tests/slides.md) that will be usable for marp,
   like this:

   ```console
   > docker run \
     --rm \
     -e MARP_USER=1000:1000 \
     -e LANG=en_US.UTF-8 \
     -v $PWD:/home/marp/app/ \
     marpteam/marp-cli --html true tests/slides.md
   [  INFO ] Converting 1 markdown...
   [  INFO ] tests/slides.md => tests/slides.html
   ```

   That will give you a [tests/slides.html](tests/slides.html) presentation.

2. A file named [tests/slides.schedule.md](tests/slides.schedule.md) that will
   be usable by `pandoc`, like this:

   ```console
   > pandoc tests/slides.schedule.md -o tests/slides.schedule.pdf
   ```

   That will give you a [tests/slides.schedule.pdf](tests/slides.schedule.pdf)
   pdf containing the schedule taken from the slides.
   Note: `pandoc` supports templates that can be used to customize the pdf.

For details about using this role in the training and documentation context,
have a look at the [KPA GitHub project page](https://github.com/mmul-it/kpa).

License
-------

MIT

Author Information
------------------

Raoul Scarazzini ([rascasoft](https://github.com/rascasoft))
