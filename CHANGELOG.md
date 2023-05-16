# Changelog

## 0.2.9 (May 16, 2023)

### Changes

- A new tag, 'IdenagoUpdate', has been added to be used with the 'idena_node_mgmt.yaml' playbook. It will update the `idena-go` node client to the version that has been set in `idena_go_ver` [variable](https://github.com/ltraveler/idena-sibling/blob/main/group_vars/main/vars). The value `latest` will update the node client to the latest release version.

## 0.2.7 (May 15, 2023)

### Changes

- Allow 'latest' as a valid string option for 'idena_go_ver' variable to download the latest version of the idena-go node client.

## 0.2.5 (May 12, 2023)

### Bugfix

- `net-tools` is required for running shared node installation playbook.

## 0.2.3 (May 10, 2023)

### Changes

- The 'droplet_ip' variable has been deprecated. All the required data will now be taken from the droplet response.

## 0.2.1 (Apr 20, 2023)

### Bugfix

- Small bug fixes and little improvements

## 0.2.0 (Apr 18, 2023)

### Changes

- The playbooks in the repository have been completely refactored. Please check the [README.md](https://github.com/ltraveler/idena-sibling/blob/main/README.md) for full information about the changes.

## 0.1.1 (Feb 28, 2023)

### Bugfix

- Fixed mistake in variables configuration file. ✧ The host variable template was set incorrectly, resulting in incorrect records being produced in the idena-go configuration. This issue has been resolved.

## 0.1.0 (Feb 16, 2023)

### Changes

- Complete Idena Shared Node deployment circle

## 0.0.1 (Feb 10, 2023)

### Changes

- Pre-alpha release
