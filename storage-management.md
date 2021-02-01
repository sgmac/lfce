# Storage


## Advance LVM

- You can use `pvscan` to see the raw devices that belong to a VG.

- Create a vggroup with  extent size `vgcreate vg_testing  /dev/sda1 -s 5M` 

- You can create  `lvcreate -l 12  my_vg /dev/sda1:1-4 /dev/sdb1:1-4 /dev/sdc1:1-4`


- **LVM mirror** You have 1 copy pluse how many mirrors? (since we have 3 disks ) `lvcreate -L 200M -m2 -n mirror vg_destination`


- **LVM stripe** I(stripe size) `lvcreate -L 20M -i3 -I64 -n stripe vg_destination`

- **LVM extend volume** `lvextend -L +10M /dev/vg_destination/vol`

- You can reduce the size of the logical volume, not while is mounted. You should shrink the filesystem before you shrink the LVM. `lvreduce -L -10M /dev/vg_destination/vol1`. You should run a `fsck` before mount it.

## Snapshots

- The snapshot does not need to be as big as the source. `lvcreate -s -n snap -L 5M /dev/vg1/vol1`

- Edit `/etc/lvm.conf` and change `snapshot_autoextended_threshold=50 | snapshot_autoextended_percent=50`. Then run `lvm dumpconfig`


## Thin provisioning

- `lvcreate -l 290 --thinpool thin_vol mythinvol`

```
sudo lvdisplay /dev/mythinvol/thinvol
  --- Logical volume ---
  LV Name                thinvol
  VG Name                mythinvol
  LV UUID                Hq0sRa-jp9O-KQoI-x2F8-ddz0-j3mJ-SKNdLb
  LV Write Access        read/write
  LV Creation host, time odroid, 2021-02-01 21:51:40 +0100
  LV Pool metadata       thinvol_tmeta
  LV Pool data           thinvol_tdata
  LV Status              available
  # open                 0
  LV Size                580,00 MiB
  Allocated pool data    0,00%
  Allocated metadata     11,72%
  Current LE             290
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:4
  ```

```
$ sudo lvcreate -V 1500G --thin -n client1 mythinvol/thinvol
  WARNING: Sum of all thin volume sizes (1,46 TiB) exceeds the size of thin pool mythinvol/thinvol and the size of whole volume group (<447,14 GiB).
  WARNING: You have not turned on protection against thin pools running out of space.
  WARNING: Set activation/thin_pool_autoextend_threshold below 100 to trigger automatic extension of thin pools before they get full.
  Logical volume "client1" created.
  ```
