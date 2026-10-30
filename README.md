## Getting started

### 1. Clone the repository

```bash
git clone https://github.com/jogajaga/statuspulse-infrastructure.git
cd statuspulse-infrastructure
```

### 2. Configure the inventory

```bash
cp inventory.example.ini inventory.ini
```

Edit `inventory.ini` and set:

- `ansible_host`: target server IP address or DNS name.
- `ansible_port`: target SSH port.
- `ansible_user`: target SSH user.
- `ansible_ssh_private_key_file`: private key path on the control node.
- `ansible_python_interpreter`: Python path on the target server.

The SSH port must already be configured on the target server. This playbook does not change the SSH daemon configuration.

### 3. Configure the GitLab URL

Edit the variables in `gitlab.yml`:

```yaml
gitlab_url: "http://YOUR_SERVER_IP"
gitlab_ssh_port: 2498
```

Set `gitlab_ssh_port` to the SSH port clients use to reach GitLab.

The external URL is currently defined in the playbook and must be updated separately from the inventory address.

### 4. Check connectivity and syntax

```bash
ansible -i inventory.ini gitlab -m ping
ansible-playbook -i inventory.ini gitlab.yml --syntax-check
```

The Ansible ping module checks SSH access and Python execution; it is not an ICMP ping.

### 5. Run the playbook

```bash
ansible-playbook -i inventory.ini gitlab.yml -K
```

Enter the target user's sudo password when prompted.

The first run downloads the GitLab package and performs initial configuration. This can take several minutes.

If a firewall is enabled, allow access to the configured SSH port and HTTP port 80 from the appropriate networks.

## Verification

Open the configured GitLab URL in a browser.

Check readiness from the control node:

```bash
curl --fail http://YOUR_SERVER_IP/-/readiness
```

Check services on the GitLab VM:

```bash
sudo gitlab-ctl status
```

Run the playbook again to check that the existing configuration is maintained without unnecessary changes.

## Initial administrator access

The initial administrator username is `root`.

Retrieve the generated password on the GitLab VM:

```bash
sudo cat /etc/gitlab/initial_root_password
```

The password file is temporary. Change the password after signing in and create a separate account for daily use.

Never commit passwords or private SSH keys.
