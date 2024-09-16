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
* Configuration of a single Organization running on two AAP servers - one being a 'production' AAP server and one being a 'development' AAP server.  The 'development' AAP server has a similar configuration as production but uses a different AAP manifest file and (optionally) different credentials, projects, templates and inventories.
* Configuration of multiple Organizations running many different AAP servers, with configuration split between:
    - Configuration that is common in all Organizations
    - Configuration that is unique to an Organization
    - Configuration that is unique to a specific Organization on a specific AAP server

## Official configuration options

The official documentation lists the parameters that can be supplied in the configuration files:

* [credential](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/content/module/credential/)
* [execution environment](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/content/module/execution_environment/)
* [inventory source](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/content/module/inventory_source/)
* [inventory](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/content/module/inventory/)
* [job template](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/content/module/job_template/)
* [project](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/content/module/project/)
* [workflow job template](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/content/module/workflow_job_template/
)

## Naming Conventions

Although not required, a standard naming convention for Ansible Automation Platform resources provides support teams with a consistent experience and allows automation and scripting tools to use pattern matching and regular expressions to audit and manipulate the environment.  This repository uses a naming approach similar to the one used for [satellite-configuration](https://github.com/rjo-uk/satellite-configuration) and includes some opinionated conventions.

### Overview

Avoid special characters such as `@ ! " ' , ? # : ;` in object names, as this allows objects to be manipulated easily in CSV and YAML formats.  If there is an established naming convention (eg for a platform) consider replicating it.  Although AAP objects can often be renamed, you may find that a new object is created instead.  As an example, if a Project is initially created as the name `prj-rhel-upgrade` and is subsequently updated in the code to `proj-rhel-7-upgrade`, you might end up with two projects.  This can cause confusion.  It is advisable to avoid this if possible by configuring your resources in the yaml files and performing some analysis before implementing the configuration.  The [controller_object_diff](https://github.com/redhat-cop/controller_configuration/blob/devel/plugins/lookup/controller_object_diff.py) lookup can also be used to identify differences between the live and desired configuration to see if these types of change have occurred or if manual configuration has been made.


### Credentials

| Naming Convention | Examples |
|       :---:       |  :---:   |
| cred _ < type (short form) > _ < username > [ _ environment ] | cred_mach_root |
| | cred_mach_root_production |
| | cred_sat6_admin |
| | cred_api_redhat_insights |
| | cred_api_snow_prod |
| | cred_api_snow_test |
| | cred_git_svc_git_ro |
| | cred_api_snow_prod |
| | cred_api_vcenter_prod |

### Inventories

| Naming Convention | Examples |
|       :---:       |  :---:   |
| inv _ < source > _ < instance, user initials or SNOW ID> _ < environment > | inv_satellite_production |
| | inv_vcenter_prod |
| | inv_vcenter_test |
| | inv_snow_prod |
| | inv_snow_test |
| | inv_git_mygitrepo_main |
| | inv_aap_REQ123456 |
| | inv_git_rjo_adhoc |

### Inventory Sources

| Naming Convention | Examples |
|       :---:       |  :---:   |
| inv _ src _ < source > _ < instance, user initials or SNOW ID> _ < environment > | inv_src_satellite_production |
| | inv_src_vcenter_prod |
| | inv_src_vcenter_test |
| | inv_src_snow_prod |
| | inv_src_snow_test |
| | inv_src_git_mygitrepo_main |
| | inv_src_aap_REQ123456 |
| | inv_src_git_rjo_adhoc |

### Inventory Groups

| Naming Convention | Examples |
|       :---:       |  :---:   |
| grp _ < desc> _ < instance, user initials or SNOW ID> _ < environment > | grp_satellite_upgrade |
| | grp_satellite_rhel9 |
| | grp_satellite_rhel9_4 |
| | grp_snow_datacenter1 |
| | grp_aap_rjo_testgroup |

Avoid using `.` in group names, for example use `RHEL9_4` rather than `RHEL9.4`.  See also https://docs.ansible.com/ansible/latest/network/getting_started/first_inventory.html for useful advice.

> Avoid spaces, hyphens, and preceding numbers (use floor_19, not 19th_floor) in your group names. Group names are case sensitive

### Projects

| Naming Convention | Examples |
|       :---:       |  :---:   |
| prj _ < desc> [ _ < personal identifier > ] | prj_rhel_soe |
| | prj_leapp |
| | prj_patching |
| | prj_patching_rjo |
| | prj_service |

### Templates

| Naming Convention | Examples |
|       :---:       |  :---:   |
| tpl _ < project description > _ < template description > [  _ < personal identifier > ] | tpl_rhel_soe_motd |
| | tpl_rhel_soe_chrony |
| | tpl_leapp_analysis |
| | tpl_patching_report |
| | tpl_patching_update |
| | tpl_service_restart |

### Workflows

| Naming Convention | Examples |
|       :---:       |  :---:   |
| wflow _ < short description based on templates used > [  _ < personal identifier > ] | wflow_ipu_rhel7 |
| | wflow_ipu_rhel8 |
| | wflow_ipu_rhel8_rjo |
| | wflow_rhel_soe |
