---
{"dg-publish":true,"permalink":"/sys-admin/gitlab-ci-manual-deployments/","dgPassFrontmatter":true}
---

- Vault for managing Ansible secrets and [SSH keys](https://www.hashicorp.com/blog/managing-ssh-access-at-scale-with-hashicorp-vault)
	- [Integration with GitLab CI](https://docs.gitlab.com/ee/ci/secrets/)
- ARA, for record both CI and manual deployments:
	- [ARA Records Ansible | ara.recordsansible.org](https://ara.recordsansible.org/)
- Gitlab
	- Write a template with the deployment job
		- Only runs on manual trigger
		- Maybe, we can also require a env var to be defined. Then, we can schedule a pipeline with that env preconfigured and trigger it manually.