# Self-hosted supabase and n8n starter kit hosted on free tiered Oracle Cloud

**Self-hosted Supabase and n8n Starter Kit** is an respository that easily manages and
quickly bootstraps a fully featured Local AI and Low Code development
environment which includes Open WebUI for an interface to chat with your N8N agents. 

This is an updated version of coleam00's that integrates the deployment of supabase to replace the requirements of Qdrant and postgres containers.
Ansible was selected to manage packages, docker, and provide idempotency.
Oracle Cloud free tiered service was selected for it's "Always Free" compute instances, choose
from ARM 4-core, 24GB VM with a 200GB disk or 2 AMD 1-core, 1GB VM with 200GB disk.

[coleam00's Local AI Starter Kit](https://github.com/coleam00/local-ai-packaged) (https://github.com/coleam00/ai-agents-masterclass/tree/main/local-ai-packaged)
[Original Local AI Starter Kit from n8n](https://github.com/n8n-io/self-hosted-ai-starter-kit)

### What’s included

✅ [**Self-hosted n8n**](https://n8n.io/) - Low-code platform with over 400
integrations and advanced AI components

✅ [**Supabase**](https://supabase.com/) - Open source database as a service -
most widely used database for AI agents

✅ [**Ollama**](https://ollama.com/) - Cross-platform LLM platform to install
and run the latest local LLMs

✅ [**Open WebUI**](https://openwebui.com/) - ChatGPT-like interface to
privately interact with your local models and N8N agents

✅ [**Flowise**](https://flowiseai.com/) - No/low code AI agent
builder that pairs very well with n8n


## Prerequisites
Before you begin, make sure you have the following:

- [Python](https://www.python.org/downloads/) - Required to run ansible
- [Git/GitHub Desktop](https://desktop.github.com/) - For easy repository management
- [Docker/Docker Desktop](https://www.docker.com/products/docker-desktop/) - Required to run all services
- [Ansible](https://www.ansible.com/) - Used to deploy and manage environment
- [Oracle Cloud Account](https://signup.oraclecloud.com/) - Sign up for free tier compute instance, Create instance, save ssh private key

## Installation

Install Ansible on your local machine and start up your terminal emulator
Clone the repository and navigate to the project directory:
```bash
git clone https://github.com/petechon/self-hosted-supabase-n8n.git
cd self-hosted-supabase-n8n/ansible
```

Before running ansible, you need to set up your environment variables for ansible, n8n, and Supabase.

Make a copy of `inventory.yml.example` and rename it to `inventory.yml` in the ansible/inventory directory
    1. In `inventory.yml`, change `PUBLIC_IP_OF_YOUR_ORACLE_CLOUD_INSTANCE` to the external IP address of your Oracle Cloud instance.
    2. In `inventory.yml`, change `LOCATION_OF_YOUR_PRIVATE_SSH_KEY_PROVIDED_BY_ORACLE_CLOUD` to the location of the saved private SSH 
        key provided by Oracle Cloud on your local machine.
            [Oracle Cloud OS Image Consideration]
            - If you created your instance with Oracle Linux 8 instead of Canonical Ubuntu, then in `inventory.yaml`,
                change `ansible_user: ubuntu` to `ansible_user: opc`  

Make a copy of `.env.example` and rename it to `.env` in the ansible/roles/docker/files directory
1. Set the following required environment variables:
   ```bash
############
# Secrets
# YOU MUST CHANGE THESE BEFORE GOING INTO PRODUCTION
############

POSTGRES_PASSWORD=your-super-secret-and-long-postgres-password
JWT_SECRET=your-super-secret-jwt-token-with-at-least-32-characters-long
ANON_KEY=generate-secret-key-from-self-hosting-guide
SERVICE_ROLE_KEY=generate-secret-key-from-self-hosting-guideDaYlNEoUrrEn2Ig7tqibS-PHK5vgusbcbo7X36XVt4Q
DASHBOARD_USERNAME=supabase
DASHBOARD_PASSWORD=this_password_is_insecure_and_should_be_updated
SECRET_KEY_BASE=generate-your-secret-key
VAULT_ENC_KEY=your-encryption-key-32-chars-min


###########
# n8n
# YOU MUST CHANGE THESE BEFORE GOING INTO PRODUCTION
###########

POSTGRES_DB=n8n
N8N_ENCRYPTION_KEY=your-encryption-key-32-chars-min
N8N_USER_MANAGEMENT_JWT_SECRET=your-encryption-key-32-chars-min
   ```

   > [!IMPORTANT]
   > Follow Supabase's [self-hosting guide](https://supabase.com/docs/guides/self-hosting/docker#securing-your-services)
   > Make sure to generate secure random values for all secrets. Never use the example values in production.

---

Start in the ansible directory, and run the ansible playbook `update_packages.yml` to update the os packages
 ```bash
  ansible-playbook update_packages.yml
 ```

If connectivity and package updates are successful, then run the ansible playbook `docker_install.yml` to install and deploy
    the supabase and n8n containers
 ```bash
  ansible-playbook docker_install.yml
 ```

If successful, you'll need to open up ports in Oracle Cloud for access to the UIs.
In the Oracle Cloud dashboard/Home, select hamburger icon, Networking, Virtual Cloud Networks
Select the vcn-xxxxxxxx-xxxx
On the left side, select Security Lists
Select and modify the `Default Security List for vcn-xxxxxxxx-xxxx` to include the destination port(s) you want to allow

