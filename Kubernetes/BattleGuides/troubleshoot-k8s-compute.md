# 🛠️ Kubernetes Debugging & Troubleshooting Guide with `kubectl` + `k9s`

This guide is a **one-stop reference** for real-world debugging of workloads running on Kubernetes clusters.
It combines native `kubectl` commands, **ephemeral debug containers**, **node-level root shells**, and **k9s plugins** to make cluster troubleshooting faster and more reproducible.

---

## 🚦 Table of Contents

- [✅ Prerequisites](#-prerequisites)
- [🟢 Troubleshooting Running Pods](#-troubleshooting-running-pods)
- [🟡 Duplicating a Pod for Deep Debugging](#-duplicating-a-pod-for-deep-debugging)
- [🟠 Ephemeral Debug Containers (Netshoot)](#-ephemeral-debug-containers-netshoot)
- [🖥️ GUI/VNC Pod for Full Desktop Access](#️-guivnc-pod-for-full-desktop-access)
- [🔍 Image Layer Inspection with Dive](#-image-layer-inspection-with-dive)
- [🏗️ Node-Level Debugging](#️-node-level-debugging)
- [📚 References](#-references)
  - [🏁 Quick Strategy Recap](#-quick-strategy-recap)


---

## ✅ Prerequisites

* **kubectl** v1.23+
* **k9s** latest (for plugin support)
* Cluster access with sufficient permissions (`kubectl auth can-i …`)
* Optional CLI tools:

  * [`kubectl-debug`](https://github.com/aylei/kubectl-debug) (if you prefer a different ephemeral debug experience)
  * [`dive`](https://github.com/wagoodman/dive) – Docker image layer explorer

---

## 🟢 Troubleshooting Running Pods

When you need quick insights into a running container:

```bash
# Attach to a running container (no restart)
kubectl exec -it <pod> -n <namespace> -- bash

# View logs (and follow)
kubectl logs -f <pod> -n <namespace>

# Describe pod events, env, volumes, etc.
kubectl describe pod <pod> -n <namespace>
```

With **k9s**:

* Navigate to the namespace → Pods → `l` for logs → `s` to shell exec.

---

## 🟡 Duplicating a Pod for Deep Debugging

Sometimes you need to reproduce the environment without disturbing the original workload.
Use the **kubectl-dup** plugin to clone any resource:

```bash
kubectl krew install dup

# Duplicate an existing pod into a "debuggable" clone
kubectl dup -k <pod-name> -n <namespace> --context <context>
```

🔗 **k9s Plugin**: [dup.yaml](https://github.com/derailed/k9s/blob/master/plugins/dup.yaml)

* Inside k9s, press the configured shortcut (`Shift-D`) to duplicate the selected resource.
* Additional shortcuts allow “duplicate + edit” or spawn debug-only versions of Deployments, StatefulSets, CronJobs, etc.

---

## 🟠 Ephemeral Debug Containers (Netshoot)

For one-off debugging sessions inside a running Pod, use **ephemeral containers**:

```bash
kubectl debug -it <pod> -n <namespace> \
  --image=nicolaka/netshoot:v0.13 \
  --target=<container> --share-processes -- bash
```

This launches a `netshoot` container **inside the pod’s namespaces** (network, PID, etc.) without restarting the original container.

🔗 **k9s Plugin**: [debug-container.yaml](https://github.com/derailed/k9s/blob/master/plugins/debug-container.yaml)
Shortcut: `Shift-D` when selecting a container.

---

## 🖥️ GUI/VNC Pod for Full Desktop Access

When a CLI is not enough—for example, inspecting rendering issues or running desktop tools—spin up a lightweight **browser-capable VNC container**:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ubuntu-browser-vnc
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ubuntu-browser-vnc
  template:
    metadata:
      labels:
        app: ubuntu-browser-vnc
    spec:
      containers:
      - name: ubuntu-browser
        image: consol/debian-xfce-vnc:latest
        ports:
        - containerPort: 5901    # VNC
        - containerPort: 6901    # noVNC (web)
        env:
        - name: VNC_PW
          value: "password123"
        - name: VNC_RESOLUTION
          value: "1280x1024"
```

Deploy and port-forward:

```bash
kubectl apply -f ubuntu-browser-vnc.yaml
kubectl port-forward deploy/ubuntu-browser-vnc 5901:5901 6901:6901
```

Access:

* **VNC Client:** `vnc://localhost:5901`
* **Browser (noVNC):** [http://localhost:6901](http://localhost:6901)

This is perfect for GUI-based debugging, browser testing, or running desktop utilities inside your cluster.

---

## 🔍 Image Layer Inspection with Dive

Need to analyze image size, layers, or identify bloat?

```bash
dive <image-name>
```

🔗 **k9s Plugin**: [dive.yaml](https://github.com/derailed/k9s/blob/master/plugins/dive.yaml)
Inside k9s → highlight container → press the configured shortcut (`d`) to launch `dive` directly against the image.

---

## 🏗️ Node-Level Debugging

For investigating **node-level issues** (disk pressure, kernel configs, etc.) you can spawn a privileged shell on a node:

```bash
kubectl krew install node-shell

kubectl node-shell <node-name>
```

This creates a temporary DaemonSet and gives you a **root shell** inside the node’s host namespace.

🔗 **k9s Plugin**: [node-root-shell.yaml](https://github.com/derailed/k9s/blob/master/plugins/node-root-shell.yaml)
Shortcut: `Ctrl-N` inside the Nodes view.

---

## 📚 References

* [Kubernetes: Ephemeral Containers](https://kubernetes.io/docs/tasks/debug/debug-application/debug-running-pod/#ephemeral-container)
* [k9s Plugins](https://github.com/derailed/k9s/tree/master/plugins)
* [kubectl-dup](https://github.com/vash/dup)
* [kubectl-node-shell](https://github.com/kvaps/kubectl-node-shell)
* [dive](https://github.com/wagoodman/dive)

---

### 🏁 Quick Strategy Recap

* **First glance:** `kubectl logs/describe` or k9s built-ins.
* **Ephemeral deep dive:** Attach a `netshoot` ephemeral container.
* **Clone for safety:** Use `kubectl dup` to duplicate the pod and experiment freely.
* **Need GUI tools?** Deploy the VNC browser pod.
* **Host problems?** Use `kubectl node-shell` for root-level node access.
* **Image bloat?** Inspect with `dive`.

This layered approach ensures you can debug **from container to node** without disrupting production workloads.

---
