
Get current disk usage:

```
lsblk -nb -o FSUSED /dev/mmcblk0p2
```

Resize partition:

```
parted /dev/sdb resizepart 2 10G
resize2fs /dev/sdb2
```

