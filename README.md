# rhpds.ftl

Ansible collection for RHDP Zero Touch (ZT) lab grading and runner setup.
Follows AgnosticD `core_workloads` naming conventions.

**Repo:** https://github.com/rhpds/rhpds-ftl

## Roles

| Role | Description |
|---|---|
| `ocp4_workload_zt_sa` | Creates namespace-scoped RoleBinding granting showroom SA admin access to student namespace. Enables ZT runner to manage resources without cluster-wide permissions. |
| `ocp4_workload_zt_load_test` | Triggers solve + validate on all ZT modules via runner API. Reports results via `agnosticd_user_info`. |
| `rhel_workload_zt_load_test` | Same as above for RHEL/VM-based showrooms. |

## Usage in AgV

```yaml
requirements_content:
  collections:
  - name: https://github.com/rhpds/rhpds-ftl.git
    type: git
    version: "{{ tag }}"

workloads:
  - agnosticd.namespaced_workloads.ocp4_workload_tenant_keycloak_user
  - agnosticd.namespaced_workloads.ocp4_workload_tenant_namespace
  - agnosticd.showroom.ocp4_workload_showroom
  - rhpds.ftl.ocp4_workload_zt_sa           # RoleBinding for runner
  # optional:
  # - rhpds.ftl.ocp4_workload_zt_load_test  # E2E load test
