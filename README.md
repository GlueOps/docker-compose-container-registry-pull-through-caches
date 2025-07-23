# docker-compose-container-registry-pull-through-caches

# To deploy in hetzner, just use this cloud-init config. Don't forget to update the hostname and the authkey for tailscale

```yaml
#cloud-config

# --- 1. System Configuration ---
# Sets the system hostname and manages the /etc/hosts file.
# :exclamation:️ Replace "container-registry-cache-for-dev" with your desired hostname.
hostname: container-registry-cache-for-dev
manage_etc_hosts: true

# --- 2. Setup Commands ---
runcmd:
  # Install Tailscale and Docker
  - 'curl -fsSL https://tailscale.com/install.sh | sh'
  - 'curl -fsSL https://get.docker.com -o get-docker.sh'
  - 'sh get-docker.sh'
  - 'sudo apt-get update && sudo apt install tmux git -y && sudo apt-get clean'

  # Configure and connect to Tailscale in a single step
  # :exclamation:️ Replace "tskey-auth-..." with your actual Tailscale auth key.
  - ['tailscale', 'up', '--authkey=XXXXXXXXXXXXXXXXXXXXXXXX', '--ssh']

  # Secure the root account by disabling password login
  - ['passwd', '-d', 'root']

  # Deploy the Docker Compose application
  # :exclamation:️ Replace the URL with the actual link to your compose file.
  - 'cd /opt && git clone https://github.com/GlueOps/docker-compose-container-registry-pull-through-caches.git'
  - 'cd /opt/docker-compose-container-registry-pull-through-caches && docker compose up -d'
```
