
# Stratis Configuration Guide

### 1) 🛠️ Installation
```bash
dnf install stratisd stratis-cli
```

### 2) 🚀 Démarrage du service `stratisd`
```bash
systemctl start stratisd  # Start for the current session
systemctl enable stratisd  # Enable to start on boot
systemctl enable --now stratisd  # Enable and start immediately in real-time
```

### 3) 💾 Formater le disque
```bash
wipefs -a /dev/sdb  # Wipe all signatures on the disk
```

### 4) 🏞️ Création d'un pool Stratis
```bash
stratis pool create pool1 /dev/sdb /dev/sdc  # Create a pool using disks sdb and sdc
```

### 5) ➕ Ajout d'un disque pour le stockage de données
```bash
stratis pool add-data pool1 /dev/sdd  # Add a new disk (sdd) to the pool
```

### 6) 💾 Création d'une première cache
```bash
stratis pool init-cache pool1 /dev/sdc  # Initialize cache using disk sdc
```

### 7) ➕ Ajout d'une autre cache
```bash
stratis pool add-cache pool1 /dev/sde  # Add another cache disk (sde)
```

### 8) 🔍 Pour vérifier l'état du pool et des block devices
```bash
stratis pool list  # List the pools
stratis blockdev list  # List block devices in the pool
```

### 9) 🗄️ Création d'un filesystem
```bash
stratis filesystem create pool1 fs1  # Create a filesystem (fs1) in pool1
stratis filesystem list  # List filesystems
```

### 10) 🔌 Montage

- **Temporaire** :
```bash
mount /dev/stratis/pool1/fs1 /mnt  # Mount the filesystem temporarily
```

- **Permanent** :
```bash
vim /etc/fstab
# Ajouter la ligne suivante :
# UUID="XXXXXXXXXXXXXXX-XXXXXXX-XXXXX" /mnt xfs defaults,x-systemd.requires=stratisd.service 0 0

# Ensuite, pour monter le fichier systematiquement :
mount -a
```

### 11) 📸 Création d'un snapshot
```bash
stratis filesystem snapshot pool1 fs1 snapshot1  # Take a snapshot (backup) of fs1 in pool1
```

### 12) 🗑️ Suppression des couches Stratis

- **Démonter le filesystem** :
```bash
umount /dev/stratis/pool1/fs1  # Unmount the filesystem
```

- **Effacer la configuration de `/etc/fstab`** :
  Remove the related entry from `/etc/fstab` to ensure it is not mounted at boot.

- **Supprimer le snapshot et le filesystem** :
```bash
stratis fs destroy pool1 snapshot1  # Destroy the snapshot
stratis fs destroy pool1 fs1  # Destroy the filesystem
```

- **Supprimer le pool** :
```bash
stratis pool destroy pool1  # Destroy the pool and its associated resources
```


