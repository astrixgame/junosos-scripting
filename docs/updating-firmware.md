# Updating firmware

> [!IMPORTANT]
> Always verify downloaded image checksum to ensure image integrity!

## Download latest firmware

There are multiple ways to obtain needed firmware image
- [Juniper Downloads](https://support.juniper.net/support/downloads/) - Official Juniper downloads [*requires active account*]
- [Archive Downloads](https://archive.org/download/junos-srxsme) - Community-archived images for specific hardware

### Verify image checksum

1. Get official checksum from [Juniper Downloads](https://support.juniper.net/support/downloads/) for your hardware (you won't need an account)
2. Get downloaded image checksum using md5 `md5sum <image>.tgz` or using sha256 `sha256sum <image>.tgz`
3. Compare the two checksum strings

## Check your version

To check your current JunOS version use CLI command:
```
show version
```

## Flash new firmware

To flash a new firmware into Juniper you need follow few steps:

### 1. Create backup

Save current rescue configuration
```
request system configuration rescue save
```

also save autorecovery state using
```
request system autorecovery state save
```

you can also create an snapshot to an external storage
```
request system snapshot
```

### 2. Copy new firmware image

Copy the downloaded image file using scp (via ssh)
```
scp <image>.tgz root@<junos-ip>:/var/tmp/image.tgz
```

### 3. Flash & reboot

Finally flash the new firmware image
```
request system software add reboot /var/tmp/image.tgz
```

The system will validate the image, install it, and automatically reboot. This process may take 10-20 minutes depending on your hardware.

### 4. Verify upgrade

After the system reboots, verify the new firmware version:
```
show version
```

Check system status and ensure all components are functioning correctly:
```
show chassis hardware
show chassis alarms
```

### 5. Post-upgrade cleanup

Once you've confirmed the upgrade was successful, you can remove the firmware image from `/var/tmp/` to free up space:
```
file delete /var/tmp/image.tgz
```

## Troubleshooting

If the upgrade fails or the system doesn't boot properly:

1. **Boot from rescue configuration**: During boot, access the boot loader and select the previous JunOS version
2. **Restore rescue configuration**:
   ```
   request system configuration rescue delete
   ```
3. **Use autorecovery**: The system should automatically recover using the saved state
4. **Contact support**: If issues persist, gather logs using `show log messages` and contact Juniper support

## Best Practices

- Always perform upgrades during a maintenance window
- Test the upgrade in a lab environment first if possible
- Keep a console connection available during the upgrade
- Document your current configuration before upgrading
- Review release notes for known issues and compatibility
- Ensure adequate disk space in `/var/tmp/` (minimum 1GB recommended)