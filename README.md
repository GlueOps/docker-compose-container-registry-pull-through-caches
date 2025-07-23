# docker-compose-container-registry-pull-through-caches

# Deployment

To deploy in hetzner, go to Hetzner cloud console and look for the project called "container-registry-cache".

Create a new instance in hetzner cloud:
- 3 or more vCPU's (e.g. CPX21 or CPX32)
- Keep a public IPv4
- Remove IPv6 address
- Attach the firewall rule that blocks all incoming connections
- Use cloud-init config below but update the --authKey for tailscale.


You can create an authKey in tailscale as follows:

<img width="745" height="1164" alt="image" src="https://github.com/user-attachments/assets/bfced0e9-e3e0-4f89-87ca-21b0497ad988" />


# cloud-init/config:

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
  - ['tailscale', 'up', '--authkey=YOUR_TAILSCALE_AUTH_KEY_HERE', '--ssh']

  # Secure the root account by disabling password login
  - ['passwd', '-d', 'root']

  # Deploy the Docker Compose application
  # :exclamation:️ Replace the URL with the actual link to your compose file.
  - 'cd /opt && git clone https://github.com/GlueOps/docker-compose-container-registry-pull-through-caches.git'
  - 'cd /opt/docker-compose-container-registry-pull-through-caches && docker compose up -d'
```


Once you have an instance running, update the `dev-only-registry` DNS entry to use the new tailscale IP of the node you just added and assuming everything is working destroy the old hetzner node and remove it from tailscale
