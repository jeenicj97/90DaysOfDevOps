
# Day 13 – Linux Volume Management (LVM)

## Task
Learn LVM to manage storage flexibly – create, extend, and mount volumes.

## Challenge Tasks

### Task 1: Check Current Storage
Run: `lsblk`, `pvs`, `vgs`, `lvs`, `df -h`

  ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/deecbee42cebd6132821130bdc98462aebb8ef3e/2026/day-13/task1.png)

### Task 2: Create Physical Volume
```bash
pvcreate /dev/nvme1n1 /dev/nvme2n1
pvs
```

 ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/deecbee42cebd6132821130bdc98462aebb8ef3e/2026/day-13/task2.png)

### Task 3: Create Volume Group
```bash
vgcreate tws_vg /dev/nvme1n1 /dev/nvme2n1
vgs
```
 ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/deecbee42cebd6132821130bdc98462aebb8ef3e/2026/day-13/task3.png)
 
### Task 4: Create Logical Volume
```bash
lvcreate -L 10G tws_lv tws_vg
lvs
```
 ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/deecbee42cebd6132821130bdc98462aebb8ef3e/2026/day-13/task4.png)

### Extra Commands
 ```bash
pvdisplay
vgdisplay
lvdisplay
```

### Task 5: Format and Mount
```bash
mkdir /mnt/tws_lv_mount
mkfs.ext4 /dev/tws_vg/tws_lv (FORMATTING)
mount /dev/tws_vg/tws_lv /mnt/tws_lv_mount (MOUNTING)
df -h /mnt/tws_lv_mount
```
### Disk mounting
```bash
mkdir /mnt/tws_disk_mount
mkfs -t ext4 /dev/nvme3n1
mount /dev/nvme3n1 /mnt/tws_disk_mount
```

 ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/deecbee42cebd6132821130bdc98462aebb8ef3e/2026/day-13/task5.png)
 
### Task 6: Extend the Volume
```bash
lvextend -L +5G /dev/tws_vg/tws_lv
df -h 
```
 ![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/deecbee42cebd6132821130bdc98462aebb8ef3e/2026/day-13/task6.png)
---

## Commands Used
- lsblk
- pvcreate
- vgcreate
- lvcreate
- mkfs.ext4
- mkfs -t ext4
- mount
- lvextend
- pvdisplay
- vgdisplay
- lvdisplay

## Key Learnings
- Difference between PV, VG, LV
- How LVM allows flexible storage management
- How to extend storage dynamically
