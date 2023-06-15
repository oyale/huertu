---
{"dg-publish":true,"permalink":"/sys-admin/poc-packer-at-coopdevs/","dgPassFrontmatter":true}
---


Create an image with a fully functional demo Odoo v14 instance and use it to provision a new brand server.

It must include:
- SSH keys & sysadmins conf
- Nginx configured
- Odoo
- PostgreSQL
- Monitoring exporters

We're using the [Hetzner Cloud Builder](https://developer.hashicorp.com/packer/plugins/builders/hetzner-cloud)

### Builders
```yaml
{
  "builders": [
    {
      "type": "hcloud",
      "token": "YOUR API KEY",
      "image": "ubuntu-22.04",
      "location": "nbg1",
      "server_type": "cx11",
      "ssh_username": "root"
    }
  ]
}
```

# File

```yaml
packer {
  required_plugins {
    name = {
      version = ">= 1.0.0"
      source  = "github.com/hashicorp/packer-plugin-hcloud"
    }
  }
}
source "hcloud" "coopdevs_test" {
  token = "YOUR API TOKEN"
  image = "ubuntu-22.04"
  location = "nbg1"
  server_type = "cx11"
  ssh_username = "root"
}

build {
  sources  = ["source.hcloud.coopdevs_test"]
}
```
### Add Ansible provisioner

```yaml
source "hcloud" "coopdevs_test" {
  token = "YOUR API TOKEN"
  image = "ubuntu-22.04"
  location = "nbg1"
  server_type = "cx11"
  ssh_username = "root"
  ssh_keys = ['id', 'id2', 'id3']
  snapshot_labels = {
    odoo: 'v14'
    release: '20230310'
  }

}

build {

  provisioner "ansible" {
    command       = "/Path/To/call_ansible.sh"
    playbook_file = "./playbook.yml"
  }

  sources  = ["source.hcloud.coopdevs_test"]
  
}
```
##### `call_ansible.sh`
```bash
#!/bin/bash
source /tmp/venv/bin/activate && ANSIBLE_FORCE_COLOR=1 PYTHONUNBUFFERED=1 /tmp/venv/bin/ansible-playbook "$@"
