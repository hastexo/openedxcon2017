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
