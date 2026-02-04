# cmu-15213-labs
Labs and notes for CS:APP (15-213: Computer Systems: A Programmer’s Perspective).

## Useful Links
[labs](https://csapp.cs.cmu.edu/3e/labs.html)

[lectures](https://www.cs.cmu.edu/afs/cs/academic/class/18213-m25/www/lectures/)

## Environment Setup (for Apple Silicon Macs)
### Clone the repository
``` bash
cd ~/proj
git clone https://github.com/jjjllxx/cmu-15213-labs.git
```

### Install Docker Desktop
- Download and install Docker Desktop for Apple Silicon from [website](https://www.docker.com/products/docker-desktop).
- Sign in with your Docker Hub account after installation.

### Pull the Ubuntu (x86) image
``` bash
docker pull --platform linux/amd64 ubuntu:22.04
```
The `--platform linux/amd64` flag forces Docker to pull the x86_64 image, so it runs correctly under QEMU emulation on Apple M-series chips.

### Create and start the container
``` bash
docker run -it \
  --platform linux/amd64 \
  --name csapp \
  -v ~/proj/cmu-15213-labs:/proj \
  -w /proj \
  --cap-add=SYS_PTRACE \
  --security-opt seccomp=unconfined \
  ubuntu:22.04 /bin/bash
```
Explanation:
- `--platform linux/amd64` → run an x86_64 container (QEMU-emulated on M-chip).
- `--cap-add=SYS_PTRACE --security-opt seccomp=unconfined` → allow debugging tools such as gdb, valgrind, etc.
- `-v` → mount local folder into the container for easy development.
- `-w /proj` → set the working directory inside the container.

### (Optional) Verify the architecture
``` bash
uname -m # expected result: x86_64
``` 
### Install required packages inside the container
``` bash
apt update && apt -y upgrade
apt install -y build-essential gcc-multilib libc6-dev-i386 gdb binutils nasm make python3 python3-pip git valgrind file
```
(Optional) Install gdb enhancements
``` bash
python3 -m pip install --user pwntools
git clone https://github.com/pwndbg/pwndbg.git /opt/pwndbg || true
cd /opt/pwndbg && ./setup.sh || true
cd /proj
```
Notes:
- `gcc-multilib` and `libc6-dev-i386` enable 32-bit compilation (`-m32`), required by several labs.
- `pwntools` and `pwndbg` are optional but useful for debugging, binary analysis, and the bomb / attack labs.
### Set up VS Code (recommended)
1. Install the **Dev Containers** extension（Microsoft ID：ms-vscode-remote.remote-containers）
2. Ensure the docker container `csapp` is running.
``` bash
docker start csapp
``` 
3. In VS Code, open the **Remote Explorer** panel(on the left side). Under **Dev Containers**, right-click `ubuntu:22.04 csapp` → **Attach in Current Window**.
- When the new VS Code window opens, select **Open Folder** → `/proj`.
### Develop, compile, and test
Edit lab files in VS Code. Run compilation and tests from the terminal inside the container.
### Exit and re-enter the container
To leave:
``` bash
exit
```
To resume later (without losing installed tools):
``` bash
docker start -ai csapp
```