So what about this
## Xblock?


```bash
sudo /edx/bin/pip.edxapp install hastexo-xblock
```

```python
"ADDL_INSTALLED_APPS": [
    "hastexo"
],
```


```python
"XBLOCK_SETTINGS": {
    "hastexo": {
        "terminal_url": "/terminal",
        "ssh_dir": "/edx/var/edxapp/terminal_users/ANONYMOUS/.ssh",
        "ssh_upload": false,
        "ssh_bucket": "identities",
        "launch_timeout": 300,
        "suspend_timeout": 120,
        "task_timeouts": {
            "sleep": 5,
            "retries": 60
        },
```


```python
        "js_timeouts": {
            "status": 10000,
            "keepalive": 15000,
            "idle": 600000,
            "check": 5000
        },
```


```python
        "providers": {
            "default": {
                "os_auth_url": "",
                "os_auth_token": "",
                "os_username": "",
                "os_password": "",
                "os_user_id": "",
                "os_user_domain_id": "",
                "os_user_domain_name": "",
                "os_project_id": "",
                "os_project_name": "",
                "os_project_domain_id": "",
                "os_project_domain_name": "",
                "os_region_name": ""
            },
		}
	}
	# ...
}
```


```xml
<sequential
  url_name="openstack_introduction_part_2_lab"
  display_name="Introduction Part 2 Lab"
  graded="true"
  format="Lab">

<vertical
  url_name="openstack_introduction_part_2_lab_unit"
  display_name="Lab">

<html
  url_name="openstack_introduction_part_2_lab_instructions"
  filename="openstack_introduction_part_2_lab_instructions"
  display_name="Introduction Part 2 Lab Instructions"/>
```


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

</vertical>
</sequential>
```


<!-- .slide: data-background-iframe="http://localhost:4200" data-background-size="contain" -->

Note: Needs shellinaboxd, screen session.
