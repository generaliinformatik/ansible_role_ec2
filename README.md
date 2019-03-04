# Ansible role: creating EC2 instances

## Motivation
This role simplifies the process of creating EC2 instances with Ansible by
reducing the amount of input you'd normally have to feed the ec2_* modules with.
This is achieved by setting default values once, in `defaults/main.yml`, that
are used whenever you don't explicitly specify a required setting. On top of
that, there are some convenience functions like choosing the subnet with the
most free IP addresses or selecting the newest operating system image.

## Requirements
- Ansible 2.7
- boto3
- Jinja 2.10
  - add `jinja2_native = true` to your `ansible.cfg`

There are (breaking) incompatiblities with versions lower than listed above.
Using

`pip install --user --upgrade ansible boto3 jinja2`

will set you up just fine.

## Authorization
If you're not using IAM role privileges you have to set `aws_access_key` and `aws_secret_key` in a pre-task when using this role. The preferred way is that
you create a [vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html)
and include that in your play. Don't make a habit of hard-coding credentials.

## Usage
This repository is meant to be used as submodule from within your Ansible project:

`git submodule add https://github.com/generaliinformatik/ansible_role_ec2.git roles/ec2`

Set the appropriate default values for your AWS environment in `defaults/main.yml`.

### Example plays
A simple "hello world"-style example for an Ansible play using this role looks like this:

```yaml
- hosts: localhost
  vars:
    instance_configuration:     # the configuration dictionary
      myinstance:               # this name can be freely chosen
        name: MYINSTANCENAME    # this name will appear in the 'Name' tag
  pre_tasks:
    - name: set AWS credentials
      set_fact:
        aws_access_key: "foo"   # For demonstration purposes only - hard-coding
        aws_secret_key: "bar"   # credentials is a bad practice.
  roles:
    - ec2
```
This will create an instance named `MYINSTANCENAME` based on the default
settings as defined in `defaults/main.yml`.

A more elaborate example using more options:

```yaml
- hosts: localhost
  vars:
    instance_configuration:
      instance1:
        name: SERVER01
        instance_type: m4.large
        root_volume_size: 60         # value in GiB
      instance2:
        name: SERVER02
        instance_type: m4.4xlarge
        root_volume_size: 40         # value in GiB
        additional_volumes:
          - device_name: /dev/sdb
            volume_type: gp2
            volume_size: 100         # value in GiB
  pre_tasks:
   - name: get AWS credentials from vault
      include_vars:  # aws_access_key, aws_secret_key
        file: /etc/ansible/aws_vault.yml
  roles:
    - ec2
```

You can check `tasks/launch_ec2.yml` to see all the options that are passed to
`ec2_instance`; they can all be used in the `instance_configuration` dictionary.

## License
[MIT](LICENSE)
(c) 2019 Generali Informatik
