# JOSH Filtering Demo

This demo shows how to use [JOSH](https://josh-project.github.io/josh/) to filter Git repositories and create modular bundles.
<img width="1358" height="468" alt="image" src="https://github.com/user-attachments/assets/27ceb60a-36f8-45f0-9d4f-a558bc108009" />

## 1. Start JOSH Proxy

Start the JOSH proxy server to enable repository filtering:

```shell
docker run \
  --name josh-proxy \
  --detach \
  --publish 8000:8000 \
  --env JOSH_REMOTE=https://github.com \
  --env GIT_SSH_COMMAND="ssh -o PreferredAuthentications=publickey -o ForwardAgent=yes" \
  --volume josh-vol:/data/git \
  --platform linux/amd64 \
  joshproject/josh-proxy:latest >/dev/null
```

## 2. Clone Filtered Modules

Clone specific subdirectories as separate repositories using JOSH filtering:

### Module A (deliverables)
```shell
git clone http://localhost:8000/pelkiegr/josh-demo.git:/deliverables.git deliverables
cd deliverables && git log --oneline && cd ..
```

### Module B (demo-code)
```shell
git clone http://localhost:8000/pelkiegr/josh-demo.git:/demo-code.git demo-code
cd demo-code && git log --oneline && cd ..
```

## 3. Create Git Bundles

Create portable git bundles from each filtered module:

```shell
cd deliverables
git bundle create ../deliverables.bundle main
cd ..

cd demo-code
git bundle create ../demo-code.bundle main
cd ..
```

## 4. Verify Git Bundles

Verify the bundles were created correctly:

```shell
git bundle verify deliverables.bundle
git bundle verify demo-code.bundle
```

## 5. Combine Modules

Create a new repository combining both modules:

```shell
mkdir combined && cd combined
git init
git pull ../deliverables.bundle main
git log --oneline
git pull ../demo-code.bundle main
git log --oneline --graph
```
