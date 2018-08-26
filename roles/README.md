# Conceptual Design

### List available actions

```
tower-cli job_template list                                                                                                                                                           (master)
== ===================== ========= ======= ========
id         name          inventory project playbook
== ===================== ========= ======= ========
 7 vmware/vcenter/folder         2       6 run.yml
== ===================== ========= ======= ========
```

### See the required inputs for a job

```
tower-cli job_template survey 7                                                                                                                                                       (master)
{
  "spec": [
    {
      "required": true,
      "min": 0,
      "default": "",
      "max": 1024,
      "question_description": "Enter run_role",
      "choices": "",
      "variable": "run_role",
      "question_name": "Enter run_role",
      "type": "text"
    },
    {
      "required": true,
      "min": 0,
      "default": "",
      "max": 1024,
      "question_description": "Enter vcenter_hostname",
      "choices": "",
      "new_question": true,
      "variable": "vcenter_hostname",
      "question_name": "Enter vcenter_hostname",
      "type": "text"
    },
    {
      "required": true,
      "min": 0,
      "default": "",
      "max": 1024,
      "question_description": "Enter vcenter_username",
      "choices": "",
      "new_question": true,
      "variable": "vcenter_username",
      "question_name": "Enter vcenter_username",
      "type": "text"
    },
    {
      "required": true,
      "min": 0,
      "default": "",
      "max": 64,
      "question_description": "Enter vcenter_password",
      "choices": "",
      "new_question": true,
      "variable": "vcenter_password",
      "question_name": "Enter vcenter_password",
      "type": "password"
    },
    {
      "required": true,
      "min": null,
      "default": "False",
      "max": null,
      "question_description": "Enter vcenter_ssl",
      "choices": "True\nFalse",
      "new_question": true,
      "variable": "vcenter_ssl",
      "question_name": "Enter vcenter_ssl",
      "type": "multiplechoice"
    },
    {
      "required": true,
      "min": 0,
      "default": "",
      "max": 4096,
      "question_description": "Enter vcenter_folders",
      "choices": "",
      "new_question": true,
      "variable": "vcenter_folders",
      "question_name": "Enter vcenter_folders",
      "type": "textarea"
    }
  ],
  "description": "",
  "name": ""
}
```

### Sample Run

First we are going to create a yaml file for our extra inputs. This will
be provided programmatically but for now we will set them.

```
run_role: "vmware/vcenter/folder"
vcenter_hostname: "vcenter.domain.com"
vcenter_username: "admin"
vcenter_password: "password"
vcenter_ssl: "False"
vcenter_folders: "[{'folder_state': 'present', 'datacenter_name': 'Holland', 'folder_name': 'A', 'folder_type': 'vm'}, {'folder_state': 'present', 'datacenter_name': 'Holland', 'folder_name': 'B', 'folder_type': 'vm'}]"
```

Now let's run a noop check

```
tower-cli job launch --job-template=7 --job-type=check --extra-vars=@input.yml --monitor                                                                                              (master)
------Starting Standard Out Stream------

PLAY [Running] *****************************************************************


TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [check_vars : run_role should be defined] *********************************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_hostname should be defined] **************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_username should be defined] **************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_password should be defined] **************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_ssl should be defined] *******************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_folders should be defined] ***************
skipping: [localhost]

TASK [vmware/vcenter/folder : Manage folders on given datacenter] **************
skipping: [localhost] => (item={'folder_type': u'vm', 'folder_state': u'present', 'datacenter_name': u'Holland', 'folder_name': u'A'})
skipping: [localhost] => (item={'folder_type': u'vm', 'folder_state': u'present', 'datacenter_name': u'Holland', 'folder_name': u'B'})

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0


------End of Standard Out Stream--------
Resource changed.
== ============ =========================== ========== =======
id job_template           created             status   elapsed
== ============ =========================== ========== =======
36            7 2018-08-26T00:14:57.797826Z successful 17.095
== ============ =========================== ========== =======
```

Looks good. Now let's do a real run.

```
tower-cli job launch --job-template=7 --job-type=run --extra-vars=@input.yml --monitor                                                                                                (master)
------Starting Standard Out Stream------

PLAY [Running] *****************************************************************


TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [check_vars : run_role should be defined] *********************************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_hostname should be defined] **************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_username should be defined] **************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_password should be defined] **************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_ssl should be defined] *******************
skipping: [localhost]

TASK [vmware/vcenter/folder : vcenter_folders should be defined] ***************
skipping: [localhost]

TASK [vmware/vcenter/folder : Manage folders on given datacenter] **************
changed: [localhost] => (item={'folder_type': u'vm', 'folder_state': u'present', 'datacenter_name': u'Holland', 'folder_name': u'A'})
changed: [localhost] => (item={'folder_type': u'vm', 'folder_state': u'present', 'datacenter_name': u'Holland', 'folder_name': u'B'})

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0


------End of Standard Out Stream--------
Resource changed.
== ============ =========================== ========== =======
id job_template           created             status   elapsed
== ============ =========================== ========== =======
38            7 2018-08-26T00:18:02.663622Z successful 16.778
== ============ =========================== ========== =======
```

As a final example we are going to remove the folders we just created.

```
Resource changed.
== ============ =========================== ========== =======
id job_template           created             status   elapsed
== ============ =========================== ========== =======
40            7 2018-08-26T00:19:46.078648Z successful 16.593
== ============ =========================== ========== =======
```
