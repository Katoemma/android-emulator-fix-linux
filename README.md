# Fixing Android Emulator Crash: "The emulator process has terminated" on Linux (ubuntu tested)

## 📋 Problem

When launching an Android Virtual Device (AVD) such as **Pixel_9**, you encounter the following error:

```

The emulator process for AVD <device> has terminated.

````

This indicates the emulator crashed due to issues such as graphics incompatibility, KVM misconfiguration, or corrupted AVD state.

---

## ✅ Step-by-Step Solution for Linux

### 1. ✅ Confirm CPU Virtualization Support

Run:

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
````

If the result is `> 0`, your CPU supports virtualization.

---

### 2. ✅ Install KVM and Related Packages

```bash
sudo apt update
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager
```

---

### 3. ✅ Add User to `kvm` and `libvirt` Groups

Check group membership:

```bash
groups $USER
```

If `kvm` and `libvirt` are missing, add them:

```bash
sudo usermod -aG kvm,libvirt $USER
newgrp kvm  # or logout and log back in
```

---

### 4. ✅ Verify KVM Acceleration

Run:

```bash
kvm-ok
```

Expected output:

```
INFO: /dev/kvm exists
KVM acceleration can be used
```

---

### 5. 🔧 Force Software Rendering in Emulator

Edit the AVD configuration:

```bash
nano ~/.android/avd/Pixel_9.avd/config.ini
```

Add or modify these lines:

```ini
hw.gpu.enabled=no
hw.gpu.mode=off
```

Save and exit (`Ctrl + O`, `Enter`, `Ctrl + X`).

---

### 6. 🔄 Wipe AVD Data and Cold Boot

In **Android Studio**:

* Open **AVD Manager**
* Click the ▼ next to the device → **Wipe Data**
* Then → **Cold Boot Now**

---

### 7. 🧪 Run Emulator Manually to Debug

Use the terminal to see crash logs:

```bash
~/Android/Sdk/emulator/emulator -avd Pixel_9
```

This gives more insight into the cause.

---

### 8. 🧯 Recreate AVD (if needed)

If the problem persists:

1. Delete the crashing AVD.
2. Create a new AVD with:

   * Device: Pixel 4 or Pixel 6
   * Image: x86\_64, API 31–34
   * Graphics: **Software**

---

## ✅ Summary

* Enable KVM and virtualization
* Use software rendering to avoid GPU-related crashes
* Recreate or wipe AVD state if corrupted
