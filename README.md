# rhpds.loadtester

Ansible collection for Zero Touch (ZT) load testing in RHDP labs.

Triggers solve + validation on all lab modules simultaneously via the
zerotouch-automation runner API — used to verify the grading engine works
end-to-end after provisioning.

## Roles

| Role | Lab type | Description |
|---|---|---|
| `zt_load_test_ocp` | OCP tenant labs | Discovers showroom route, triggers solve + validate on all modules, reports via `agnosticd_user_info` |
| `zt_load_test_rhel` | RHEL/VM labs | Same as above for VM-based showrooms |

## Usage (AgV common.yaml)

```yaml
requirements_content:
  collections:
  - name: https://github.com/rhpds/rhpds-loadtester.git
    type: git
    version: "{{ tag }}"

workloads: >-
  {{
    ['agnosticd.showroom.ocp4_workload_showroom', ...] +
    (
      ['rhpds.loadtester.zt_load_test_ocp']
      if zt_load_testing_enabled | default(false) | bool
      else []
    )
  }}

# Parameter in __meta__.catalog.parameters:
# - name: zt_load_testing_enabled
#   formLabel: "Run ZT Load Test"
#   openAPIV3Schema:
#     type: boolean
#     default: false
```

## Prerequisites

The ZT runner must already be deployed in the showroom namespace.
For OCP labs this is handled by the showroom ZT configuration
(`ocp4_workload_showroom` with `zero_touch_ui_enabled: true` and the
zerotouch helm chart which includes the runner container).

## Runner image

`quay.io/rhpds/zt-runner:v1.0.0` — unified image with:
- Python: `kubernetes`, `jmespath`, `netaddr`, `hvac`, `pyOpenSSL`, `passlib`, `boto3`
- Collections: `kubernetes.core`, `ansible.posix`, `community.general`,
  `community.crypto`, `community.hashi_vault`, `ansible.netcommon`,
  `community.mysql`, `community.postgresql`
