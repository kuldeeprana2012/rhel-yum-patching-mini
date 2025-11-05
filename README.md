# Jenkins + Ansible RHEL YUM Patching Project

## Environment
- Jenkins + Ansible Master: 10.198.91.107 (RHEL)
- RHEL Client: 10.198.91.55 (SSH configured)

## Steps
1. Copy this folder to Jenkins master: `/var/lib/jenkins/workspace/rhel-yum-patching-mini`
2. Ensure Ansible is installed:
   ```bash
   sudo yum install -y ansible
   ```
3. Verify SSH from Jenkins to client:
   ```bash
   sudo -u jenkins ssh ec2-user@10.198.91.55 "hostname"
   ```
4. Create Jenkins pipeline job (SCM: Git) → use Jenkinsfile from repo.
5. Run build → will patch RHEL client using Ansible.
