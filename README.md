# docker-compose-container-registry-pull-through-caches


# Requirements

Install the basics:
```bash
curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh && sudo apt-get update && sudo apt install tmux git -y && sudo apt-get clean
```



# To deploy

```bash
git https://github.com/GlueOps/docker-compose-container-registry-pull-through-caches.git
cd docker-compose-container-registry-pull-through-cache/
docker compose up -d
```
