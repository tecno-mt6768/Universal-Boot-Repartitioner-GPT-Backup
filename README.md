<div align="center">

# Universal Boot Repartitioner + GPT Backup

[![Android](https://img.shields.io/badge/Android-11%2B-green.svg)](https://www.android.com/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Author](https://img.shields.io/badge/Author-isus203-orange.svg)](https://github.com/isus203)

**Universal script for safe boot repartitioning with automatic backup / Скрипт для безопасной переразметки boot с автоматическим бэкапом**

</dir>

**Universal tool to resize `boot_a` and `boot_b` partitions by merging them with unused `vendor_boot`.**

* **Author:** isus203
* **Version:** 1.3

### ⚠️ DISCLAIMER & COMPATIBILITY

**This script makes low-level changes to the GPT partition table.**

**⛔ DO NOT USE IF:**
1.  Your device has a manufacturer-specific partition layout lock (e.g., **Motorola** devices with a `partition` partition). **Result: Hard Brick.**
2.  There are **other system partitions** located *physically between* `boot` and `vendor_boot` (e.g., layout is `boot` -> `init_boot` -> `vendor_boot`). Expanding over them will destroy critical data.
3.  Your device runs on **Linux Kernel 5.x**.
4.  Your device actively uses `vendor_boot` (it is not empty).

**✅ REQUIREMENTS:**
* **Linux Kernel 4.1x**.
* `vendor_boot` partition exists but is **empty/unused**.
* `boot` and `vendor_boot` are adjacent (next to each other) in the partition table.

> **IMPORTANT:**
> * Incorrect sector configuration will lead to a **Hard Brick**.
> * You perform all actions at your own risk.
> * **ALWAYS** make a full backup before use.

### ℹ️ How it Works
1.  **Safety First:** Creates a full dump of the GPT layout.
2.  **Recovery Plan:** Generates a **Restore ZIP** in your backup folder before making changes.
3.  **Action:** Flashes the new `boot.img` into the resized partitions.
4.  **Logging:** Keeps a log of all operations.

### 🛠 CONFIGURATION GUIDE

**You CANNOT flash this archive "as is".** You must configure it for your specific device.

#### Step 1: Gather Data
1.  Boot into **TWRP**.
2.  Run `sgdisk` via Terminal or ADB to check your partition table:
    ```bash
    sgdisk -p /dev/block/mmcblk0
    # (Use /dev/block/sda for some devices)
    ```
3.  Write down the **Partition Numbers** and **Start/End Sectors** for `boot_a`, `boot_b`, `vendor_boot_a`, and `vendor_boot_b`.
4.  **Verify adjacency:** Ensure no other partitions exist between `boot` and `vendor_boot`.

#### Step 2: Edit the Script
1.  Unzip the archive.
2.  Open `META-INF/com/google/android/update-binary` with a text editor (VS Code, Notepad++).
3.  Edit the **CONFIGURATION** block at the top:

```bash
# 1. DEVICE
DEVICE="/dev/block/mmcblk0"  # Your block device

# 2. PATHS
EXTERNAL_PATH="/external_sd" # Where to save backups (/external_sd or /sdcard)

# 3. PARTITION NUMBERS (Check via sgdisk -p)
# Old boot partition numbers (to be removed)
OLD_BOOT_A_NUM="30"
OLD_BOOT_B_NUM="43"

# Old vendor_boot partition numbers (to be removed)
NEW_VENDOR_BOOT_A_NUM="31"
NEW_VENDOR_BOOT_B_NUM="44"

# New partition numbers (usually same as old boot)
NEW_BOOT_A_NUM="30"
NEW_BOOT_B_NUM="43"

# SECTORS (MOST IMPORTANT!)
# Format: START_SECTOR:END_SECTOR
# Logic: Start of old BOOT : End of old VENDOR_BOOT
BOOT_A_SECTORS="1003520:1200127" 
BOOT_B_SECTORS="1708032:1904639"
```
Step 4: Finalize & Flash
 * Place your desired boot.img in the root of the folder.
 * Ensure the included sgdisk binary is compatible with your CPU architecture.
 * Zip all files back together.
 * Flash via TWRP.
🆘 Emergency Recovery (Unbrick)
If something goes wrong but you can access TWRP:
 * Go to the folder defined in EXTERNAL_PATH.
 * Find the file: gpt_partition_backup_DATE_TIME.zip.
 * Flash it via TWRP. It will restore the original GPT table and boot image

📱 Tested Devices
 * Tecno Spark 9 Pro
 * Tecno Camon 19 Neo
 * Infinix Note 10 Pro NFC (X695C)
 * Chipsets: MTK
 * Android: 11, 12, 13
 * Port for Tecno Camon 19 Neo / Infinix Note 10 Pro
📥 Downloads
Download Template / Скачать шаблон
