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
Playbooks and dummy PKCS#12 file available at
<https://github.com/sverrehu/ansible-tests/tree/master/pkcs12-issue>

```yaml
---
- name: Test
  hosts: localhost
  connection: local
  gather_facts: false

  tasks:
    - name: Extract certificate from PKCS#12 file
      community.crypto.openssl_pkcs12:
        action: parse
        src: "./foo.example.com.p12"
        path: "./foo.example.com.crt"
        passphrase: "foobar"
        select_crypto_backend: cryptography
```

<!--- HINT: You can paste gist.github.com links for larger files -->

##### EXPECTED RESULTS
<!--- Describe what you expected to happen when running the steps above -->
Expected the certificate to be output to a file, and the output from
the run to look like this:

```text
$ ansible-playbook -i localhost, pyopenssl.yml

PLAY [Test] **********************************************************************************************************
**

TASK [Extract certificate from PKCS#12 file] *************************************************************************
**
ok: [localhost]

PLAY RECAP ***********************************************************************************************************
**
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

##### ACTUAL RESULTS
<!--- Describe what actually happened. If possible run with extra verbosity (-vvvv) -->
No certificate, and an error message:

<!--- Paste verbatim command output between quotes -->
```paste below
$ ansible-playbook -i localhost, cryptography.yml

PLAY [Test] ************************************************************************************************************

TASK [Extract certificate from PKCS#12 file] ***************************************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: TypeError: password must be bytes-like
fatal: [localhost]: FAILED! => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3"}, "changed": false, "module_stderr": "Traceback (most recent call last):\n  File \"/usr/local/lib/python3.8/dist-packages/cryptography/utils.py\", line 34, in _check_byteslike\n    memoryview(value)\nTypeError: memoryview: a bytes-like object is required, not 'str'\n\nDuring handling of the above exception, another exception occurred:\n\nTraceback (most recent call last):\n  File \"/home/sverrehu/.ansible/tmp/ansible-tmp-1623409195.1135283-4864-19636323110417/AnsiballZ_openssl_pkcs12.py\", line 100, in <module>\n    _ansiballz_main()\n  File \"/home/sverrehu/.ansible/tmp/ansible-tmp-1623409195.1135283-4864-19636323110417/AnsiballZ_openssl_pkcs12.py\", line 92, in _ansiballz_main\n    invoke_module(zipped_mod, temp_path, ANSIBALLZ_PARAMS)\n  File \"/home/sverrehu/.ansible/tmp/ansible-tmp-1623409195.1135283-4864-19636323110417/AnsiballZ_openssl_pkcs12.py\", line 40, in invoke_module\n    runpy.run_module(mod_name='ansible_collections.community.crypto.plugins.modules.openssl_pkcs12', init_globals=dict(_module_fqn='ansible_collections.community.crypto.plugins.modules.openssl_pkcs12', _modlib_path=modlib_path),\n  File \"/usr/lib/python3.8/runpy.py\", line 207, in run_module\n    return _run_module_code(code, init_globals, run_name, mod_spec)\n  File \"/usr/lib/python3.8/runpy.py\", line 97, in _run_module_code\n    _run_code(code, mod_globals, init_globals,\n  File \"/usr/lib/python3.8/runpy.py\", line 87, in _run_code\n    exec(code, run_globals)\n  File \"/tmp/ansible_community.crypto.openssl_pkcs12_payload_uxziulp8/ansible_community.crypto.openssl_pkcs12_payload.zip/ansible_collections/community/crypto/plugins/modules/openssl_pkcs12.py\", line 762, in <module>\n  File \"/tmp/ansible_community.crypto.openssl_pkcs12_payload_uxziulp8/ansible_community.crypto.openssl_pkcs12_payload.zip/ansible_collections/community/crypto/plugins/modules/openssl_pkcs12.py\", line 722, in main\n  File \"/tmp/ansible_community.crypto.openssl_pkcs12_payload_uxziulp8/ansible_community.crypto.openssl_pkcs12_payload.zip/ansible_collections/community/crypto/plugins/modules/openssl_pkcs12.py\", line 432, in check\n  File \"/tmp/ansible_community.crypto.openssl_pkcs12_payload_uxziulp8/ansible_community.crypto.openssl_pkcs12_payload.zip/ansible_collections/community/crypto/plugins/modules/openssl_pkcs12.py\", line 474, in parse\n  File \"/tmp/ansible_community.crypto.openssl_pkcs12_payload_uxziulp8/ansible_community.crypto.openssl_pkcs12_payload.zip/ansible_collections/community/crypto/plugins/modules/openssl_pkcs12.py\", line 587, in parse_bytes\n  File \"/tmp/ansible_community.crypto.openssl_pkcs12_payload_uxziulp8/ansible_community.crypto.openssl_pkcs12_payload.zip/ansible_collections/community/crypto/plugins/module_utils/crypto/cryptography_support.py\", line 447, in parse_pkcs12\n  File \"/usr/local/lib/python3.8/dist-packages/cryptography/hazmat/primitives/serialization/pkcs12.py\", line 28, in load_key_and_certificates\n    return backend.load_key_and_certificates_from_pkcs12(data, password)\n  File \"/usr/local/lib/python3.8/dist-packages/cryptography/hazmat/backends/openssl/backend.py\", line 2486, in load_key_and_certificates_from_pkcs12\n    utils._check_byteslike(\"password\", password)\n  File \"/usr/local/lib/python3.8/dist-packages/cryptography/utils.py\", line 36, in _check_byteslike\n    raise TypeError(\"{} must be bytes-like\".format(name))\nTypeError: password must be bytes-like\n", "module_stdout": "", "msg": "MODULE FAILURE\nSee stdout/stderr for the exact error", "rc": 1}

PLAY RECAP *************************************************************************************************************
localhost                  : ok=0    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
```

##### WORKAROUND
Force use of old backend by adding the following to the
`openssl_pkcs12` invokation:

```text
select_crypto_backend: pyopenssl
```

With this in place, our plays are running again.
