```xml
<hastexo
  display_name="Introduction Part 2 Lab"
  url_name="openstack_introduction_part_2_lab"
  stack_template_path="hot_lab.yaml"
  stack_user_name="training"
  provider="myprovider">
  <test>
    #!/bin/bash -e
    # alice containers deployed correctly
    count=$(sudo ssh alice lxc-ls | wc -l)
    [ $count -eq 22 ]
  </test>
</hastexo>
```
This is repetitive.


```xml
<conditional
  sources="block-v1:hastexo+hx201+201705-ansible+type@hastexo+block@openstack_introduction_part_1_lab"
  correct="True"
  message="You must complete {link} before this exercise will be made available.">
<hastexo
  display_name="Introduction Part 2 Lab"
  url_name="openstack_introduction_part_2_lab"
  stack_template_path="hot_lab.yaml"
  stack_user_name="training"
  provider="myprovider">
  <test>
    #!/bin/bash -e
    # alice containers deployed correctly
    count=$(sudo ssh alice lxc-ls | wc -l)
    [ $count -eq 22 ]
  </test>
</hastexo>
</conditional>
```

This is worse.


# DRY
olx-utils


```bash
pip install olx-utils
```


```bash
$ ls include/ -R
include/:
course.xml  html  sequential

include/html:
content.html  lab.html

include/sequential:
content.xml  lab.xml
```
Mako templates in `include/`


```bash
new_run.py -b newrun 2017-05-01 2017-10-31
```
Creates Git branch `run/newrun`


While we’re at it...


<!-- .slide: data-background-image="static/images/markdown-mark.svg" data-background-size="contain" -->


<!-- .slide: data-background-iframe="http://localhost:4200" data-background-size="contain" -->

Note: Needs shellinaboxd, screen session.
