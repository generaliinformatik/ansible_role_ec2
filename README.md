# Ansible role: creating EC2 instances

This repository is meant to be used as submodule from within your Ansible project.

`git submodule add https://github.com/generaliinformatik/ansible_role_ec2.git roles/ec2`

## Requirements
- Ansible 2.7
- Jinja 2.10
- boto3

There are (breaking) incompatiblities with versions lower than listed above.
But you should be fine with `pip install --user --upgrade ansible boto3 jinja2`.

## Authorization
If you're not using IAM role privileges you have to set `aws_access_key` and `aws_secret_key` in a pre-task when using this role. The preferred way is that you create a [vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) and include that in your play. Don't make a habit of hard-coding credentials.

## Usage
### Preparations
TODO needs rework, no longer accurate
After you've cloned this repository as a submodule below your `roles` directory, create a `vars/main.yml` inside the role directory and override variables as needed. You'll most likely want to set different `default_key_name`, `default_group`.

### Playbook
A most basic "hello world"-style example for a playbook looks like this.
```yaml
- hosts: localhost
  vars:
    instance_configuration:
      myinstance:   # this name has no relevance
        name: MYSCOPE_MYINSTANCENAME
  pre_tasks:
    - name: set AWS credentials
      set_fact:
        aws_access_key: "foo"   # Bad practice, for demonstration
        aws_secret_key: "bar"   # purposes only!
  roles:
    - ec2
```
This will create an instance named `MYSCOPE_MYINSTANCENAME` based on default settings as defined in `vars/main.yml`.

## License
[MIT](LICENSE)
(c) 2019 Generali Informatik
