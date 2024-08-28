# aap-configuration

Sample configuration for Ansible Automation Platform

## Requirements

The [infra.controller_configuration](https://console.redhat.com/ansible/automation-hub/repo/validated/infra/controller_configuration/)(validated content) and [ansible.controller](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/) (certified content) collections MUST be installed in order for this playbook to work.

For local testing, the collections can be installed as follows:

* Install from Ansible Automation Hub by:
1.  Update your [ansible.cfg](ansible.cfg) file to include references to validated and certified content from Red at Automation Hub:

```
[galaxy]
server_list = automation_hub_published, automation_hub_validated

[galaxy_server.automation_hub_published]
url=https://console.redhat.com/api/automation-hub/content/published/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=CHANGEME

[galaxy_server.automation_hub_validated]
url=https://console.redhat.com/api/automation-hub/content/validated/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=CHANGEME

```

2. Replace `CHANGEME` with a valid token which can be obtained at the following URL: https://console.redhat.com/ansible/automation-hub/token.  See also see [Getting started with Red Hat APIs](https://access.redhat.com/articles/3626371)

3. Install the collections as the current user:

```
ansible-galaxy collection install infra.controller_configuration
ansible-galaxy collection install ansible.controller
```

By default, this will install into `~/.ansible/collections/ansible_collections/infra/controller_configuration/` and `~/.ansible/collections/ansible_collections/ansible/controller/`.

## Playbook

The playbook to configure AAP is named [aap-configuration.yml](aap-configuration.yml) and when combined with an appropriate inventory structure, can configure one or many AAP servers comprising of one or many Organizations.  Ansible tags can be supplied when running the playbook so that specific configuration items can be run or skipped.  The playbook only calls AAP roles if appropriate variables are defined.

## Inventory Structure

The documentation below covers the following scenarios:

* Configuration of a single Organization running on a single AAP server
* Configuration of a single Organization running on two AAP servers - one being a 'production' AAP server and one being a 'development' AAP server.  The 'development' AAP server has a similar configuration as production but uses a different AAP manifest file and (optionally) different credentials, projects, tempaltes and inventories.
* Configuration of multiple Organizations running many different AAP servers, with configuration split between:
    - Configuration that is common in all Organizations
    - Configuration that is unique to an Organization
    - Configuration that is unique to a specific Organization on a specific AAP server
