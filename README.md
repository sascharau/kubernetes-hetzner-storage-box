# Configuring Hetzner SMB StorageBox in Kubernetes

This document explains how to configure a Hetzner SMB StorageBox in a Kubernetes cluster. As an example, we demonstrate how to mount such a volume to a specific location within a Pod.

## Prerequisites

- A running Kubernetes cluster (version ...).
- Installed and configured SMB CSI driver (smb.csi.k8s.io). For more information, visit: https://github.com/kubernetes-csi/csi-driver-smb
- An active Hetzner SMB StorageBox. To learn more, visit: https://www.hetzner.com/storage/storage-box

## Configuration Steps

The `hetzner-storage-box.yaml` manifests are examples of how to define the StorageClass, the PersistentVolume (PV), and the PersistentVolumeClaim (PVC) for the Hetzner SMB StorageBox.

1. **StorageClass**: The StorageClass defines the provisioner and parameters (like the mount options and SMB source). Make sure you have correctly set the `username` and `password` in the SMB secret, which should correspond to your StorageBox credentials.

2. **PersistentVolume**: This represents the actual storage resource in your StorageBox. Here, the CSI driver is set to `smb.csi.k8s.io` and the `nodeStageSecretRef` is set to the SMB secret.

3. **PersistentVolumeClaim**: PVCs request a specific size or access mode for a PV. In our case, it references the StorageClass `storagebox-1`.

4. **Pod**: This is an example of a Pod that mounts the volume for writing logs.

### Example for how to save nginx logs

Here is an example of how you can mount it to a specific path in a Pod:

```yaml
...
spec:
  containers:
  - name: app
    image: nginx:latest
    volumeMounts:
    - name: hetzner-storage
      mountPath: /var/log/nginx
      subPath: logs/mycoolapp
  volumes:
  - name: hetzner-storage
    persistentVolumeClaim:
      claimName: storagebox-1
...
```

In this example, the logs of the nginx server in your application would be written directly to the StorageBox.

### Notes
To complete the setup, you need to have the SMB CSI driver installed and configured in your cluster. You can find more information and installation steps in the official [CSI driver documentation](https://kubernetes-csi.github.io/docs/drivers.html). For the SMB CSI driver specifically, you can refer to the [SMB CSI Driver documentation](https://github.com/kubernetes-csi/csi-driver-smb).


