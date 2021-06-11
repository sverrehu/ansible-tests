# PKCS#12 operations fail with "TypeError: password must be bytes-like" on the cryptography backend

<!--- Verify first that your issue is not already reported on GitHub -->
<!--- Also test if the latest release and devel branch are affected too -->
<!--- Complete *all* sections as described, this form is processed automatically -->

##### SUMMARY
<!--- Explain the problem briefly below -->
Some PKCS#12 operations that involve `passphrase`, which worked with
the `pyopenssl` backend, stopped working after `cryptography` was made
the default backend. Error message output may indicate that there are
missing calls to `to_bytes` for `self.passphrase`.

##### ISSUE TYPE
- Bug Report

##### COMPONENT NAME
<!--- Write the short name of the module, plugin, task or feature below, use your best guess if unsure -->
openssl_pkcs12 with `cryptography` backend.

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
  jinja version = 2.10.1
  libyaml = True
```

##### CONFIGURATION
<!--- Paste verbatim output from "ansible-config dump --only-changed" between quotes -->
```paste below
$ ansible-config dump --only-changed
```

##### OS / ENVIRONMENT
<!--- Provide all relevant information below, e.g. target OS versions, network device firmware, etc. -->
Tested on Linux and MacOS with a minimal example with no networking.

##### STEPS TO REPRODUCE
<!--- Describe exactly how to reproduce the problem, using a minimal test-case -->

<!--- Paste example playbooks or commands between quotes below -->
```yaml

```

<!--- HINT: You can paste gist.github.com links for larger files -->

##### EXPECTED RESULTS
<!--- Describe what you expected to happen when running the steps above -->


##### ACTUAL RESULTS
<!--- Describe what actually happened. If possible run with extra verbosity (-vvvv) -->

<!--- Paste verbatim command output between quotes -->
```paste below

```
