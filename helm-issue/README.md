# helm_repository inherits module_defaults from k8s_info

<!--- Verify first that your issue is not already reported on GitHub -->
<!--- Also test if the latest release and devel branch are affected too -->
<!--- Complete *all* sections as described, this form is processed automatically -->

##### SUMMARY
<!--- Explain the problem briefly below -->

`helm_repository` inherits `module_defaults` set for `k8s_info`, even
values that are not supported as arguments to `helm_repository`,
leading to failure.

##### ISSUE TYPE
- Bug Report

##### COMPONENT NAME
<!--- Write the short name of the module, plugin, task or feature below, use your best guess if unsure -->

helm_repository

##### ANSIBLE VERSION
<!--- Paste verbatim output from "ansible --version" between quotes -->
```paste below
$ ansible --version
ansible [core 2.11.1] 
  config file = None
  configured module search path = ['/home/sverrehu/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/sverrehu/.local/lib/python3.8/site-packages/ansible
  ansible collection location = /home/sverrehu/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/sverrehu/.local/bin/ansible
  python version = 3.8.5 (default, May 27 2021, 13:30:53) [GCC 9.3.0]
  jinja version = 3.0.1
  libyaml = True
```

##### COLLECTION VERSION
<!--- Paste verbatim output from "ansible-galaxy collection list <namespace>.<collection>"  between the quotes
for example: ansible-galaxy collection list community.general
-->
```paste below
$ ansible-galaxy collection list kubernetes.core

# /home/sverrehu/.local/lib/python3.8/site-packages/ansible_collections
Collection      Version
--------------- -------
kubernetes.core 1.2.1  
```

##### CONFIGURATION
<!--- Paste verbatim output from "ansible-config dump --only-changed" between quotes -->
```paste below
$ ansible-config dump --only-changed
```

##### OS / ENVIRONMENT
<!--- Provide all relevant information below, e.g. target OS versions, network device firmware, etc. -->
Various Linuxes.

##### STEPS TO REPRODUCE
<!--- Describe exactly how to reproduce the problem, using a minimal test-case -->

<!--- Paste example playbooks or commands between quotes below -->
Contents of `playbook.yml`:

```yaml
---
- name: Test
  hosts: localhost
  connection: local
  gather_facts: false
  module_defaults:
    kubernetes.core.k8s_info:
      api_key: "API_KEY"

  tasks:
    - kubernetes.core.helm_repository:
        name: bitnami
        repo_url: "https://example.com"
```

```shell
$ ansible-playbook -i localhost, playbook.yml
```

<!--- HINT: You can paste gist.github.com links for larger files -->

##### EXPECTED RESULTS
<!--- Describe what you expected to happen when running the steps above -->

I expected it to succeed, or to give an error based on the arguments
passed to `helm_repository`.

##### ACTUAL RESULTS
<!--- Describe what actually happened. If possible run with extra verbosity (-vvvv) -->

It fails with an error related to the `module_defaults` set for
`kubernetes.core.k8s_info`.

<!--- Paste verbatim command output between quotes -->
```paste below
PLAY [Test] ************************************************************************************************************

TASK [kubernetes.core.helm_repository] *********************************************************************************
fatal: [localhost]: FAILED! => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"}, "changed": false, "msg": "Unsupported parameters for (kubernetes.core.helm_repository) module: api_key. Supported parameters include: repo_name (name), repo_state (state), repo_username (username), repo_password (password), repo_url (url), binary_path."}

PLAY RECAP *************************************************************************************************************
localhost                  : ok=0    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
```

Running with verbatim logging, gives this, which might be a clue to what is happening:

```
redirecting (type: action) kubernetes.core.helm_repository to kubernetes.core.k8s_info
```
