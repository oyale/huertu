---
{"dg-publish":true,"permalink":"/sys-admin/gitlab-ci-manual-deployments/","dgPassFrontmatter":true}
---

- Vault for managing Ansible secrets and SSH keys
	- We can use it for directly [manage](https://www.hashicorp.com/blog/managing-ssh-access-at-scale-with-hashicorp-vault) or as a source of SSH key's passwords.
	- [Integration with GitLab CI](https://docs.gitlab.com/ee/ci/secrets/)
- ARA, for record both CI and manual deployments:
	- [ARA Records Ansible | ara.recordsansible.org](https://ara.recordsansible.org/)
- Gitlab
	- Write a template with the deployment job
		- Only runs on manual trigger
		- Maybe, we can also require a env var to be defined. Then, we can schedule a pipeline with that env preconfigured and trigger it manually.

- [ ] #task Self host Vault
- [ ] #task Self host ARA
- [ ] #task Configure Vault for Ansible Secrets
- [ ] #task Test if Vault-managed SSH is compatible with long-term keys
- [ ] #task Create a docker image for make deployments (Ansible, ARA config, ...)
- [ ] #task Write the `.gitlab-ci.yml` template
	- [ ] #task Retrieve secrets from Vault
	- [ ] #task Deploy: notify both success and failure