# Running-GUI-Applications-Inside-Docker
## 🛠️ Methods to Run GUI Applications in Docker
### 1️⃣ Using X11 Forwarding
This method forwards the container’s GUI to your host display.

### Steps:
### 1. Allow connections to X server (Linux host):
```
xhost +local:docker
```
### 2. Run the Docker container with DISPLAY environment:
```
docker run -it \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  ubuntu:latest
```
### 3. Install GUI app inside the container:
```
apt update
apt install -y x11-apps
xclock
```
- The xclock window should appear on your host desktop.
  

## 2️⃣ Using VNC Server in the Container

For more flexible remote GUI access.

### 1. Dockerfile Example:
```
FROM ubuntu:latest
RUN apt update && apt install -y xfce4 xfce4-terminal x11vnc
RUN mkdir ~/.vnc
RUN x11vnc -storepasswd 1234 ~/.vnc/passwd
CMD ["x11vnc", "-forever", "-usepw", "-create"]
```

### 2. Build and run the image:
```
docker build -t gui-container .
docker run -d -p 5900:5900 gui-container
```

### 3. Connect via VNC viewer
- Use localhost:5900 with password 1234.
  
✅ This approach works across Linux, Windows, and macOS hosts.

# 3️⃣ Using XPRA (Seamless GUI Forwarding)

- XPRA allows forwarding a single application window instead of the whole desktop.

- You can install xpra in the container and connect via xpra attach from host.

- Lightweight alternative to full VNC.

# 4️⃣ Using Docker with GPU Support (Optional)

- If you’re running graphics-heavy applications (e.g., Blender, CAD software):
```
docker run --gpus all -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix my-gui-app
```

- Requires NVIDIA Docker / NVIDIA Container Toolkit.

- Allows hardware acceleration inside the container.
