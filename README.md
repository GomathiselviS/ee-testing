# EE Test

## Prerequisites

- Set environment variables:
  ```bash
  export aws_access_key_id="YOUR_ACCESS_KEY"
  export aws_secret_access_key="YOUR_SECRET_KEY"
  export aws_region="us-east-1"
  export ANSIBLE_ROLES_PATH="/usr/share/ansible/collections/ansible_collections/<namespace>/<name>/tests/integration/targets"
  ```
  Replace `<namespace>` and `<name>` with the collection being tested (e.g., `amazon` and `aws`).

- Pull the execution environment image:
  ```bash
  podman pull registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:2.0
  ```

**Note:** The EE image should be modified based on the execution environment being tested.

## Dependent Collections

If the collection being tested has dependencies on community collections not included in the EE, add them to the `pre_tasks` section in `site.yaml`. For example, `amazon.aws` requires `community.aws`:

```yaml
pre_tasks:
  - name: Install required collections for amazon.aws
    command: ansible-galaxy collection install community.aws --force
    changed_when: true
    when: collection_namespace == "amazon" and collection_name == "aws"
```

## Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `collection_namespace` | `amazon` | Collection namespace |
| `collection_name` | `aws` | Collection name |
| `target` | (none) | Comma-separated list of specific targets to run |

## Usage

### Run all targets
```bash
ansible-navigator run site.yaml \
  --eei registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:2.0 \
  --mode=stdout \
  --penv aws_access_key_id \
  --penv aws_secret_access_key \
  --penv aws_region \
  --penv ANSIBLE_ROLES_PATH
```

### Run a specific target
```bash
ansible-navigator run site.yaml \
  --eei registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:2.0 \
  --mode=stdout \
  --penv aws_access_key_id \
  --penv aws_secret_access_key \
  --penv aws_region \
  --penv ANSIBLE_ROLES_PATH \
  -- -e "target=autoscaling_instance"
```

### Run multiple targets
```bash
ansible-navigator run site.yaml \
  --eei registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:2.0 \
  --mode=stdout \
  --penv aws_access_key_id \
  --penv aws_secret_access_key \
  --penv aws_region \
  --penv ANSIBLE_ROLES_PATH \
  -- -e "target=autoscaling_instance,ec2_instance,s3_bucket"
```

### Run with a different collection
```bash
ansible-navigator run site.yaml \
  --eei <your-ee-image> \
  --mode=stdout \
  --penv ANSIBLE_ROLES_PATH \
  -- -e "collection_namespace=kubernetes" -e "collection_name=core"
```

### Enable verbose output
Add `-- -vvv` at the end of the command:
```bash
ansible-navigator run site.yaml \
  --eei registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:2.0 \
  --mode=stdout \
  --penv aws_access_key_id \
  --penv aws_secret_access_key \
  --penv aws_region \
  --penv ANSIBLE_ROLES_PATH \
  -- -vvv
```
