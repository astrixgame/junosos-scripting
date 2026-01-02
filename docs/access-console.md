# Access console

There are two primary methods to access a Juniper device console: serial console connection (recommended for initial setup) and SSH access after network configuration.

## Serial Console Access (Primary Method)

Serial console is the most reliable way to access Juniper devices, especially for initial configuration or troubleshooting when network access is unavailable.

### Using Screen (Linux/macOS)

1. Connect the serial cable to your Juniper device's console port
2. Identify the serial device (usually `/dev/ttyUSB0` on Linux or `/dev/tty.usbserial` on macOS):
   ```bash
   ls /dev/ttyUSB* # Linux
   ls /dev/tty.usbserial* # macOS
   ```

3. Connect using screen with 9600 baud rate:
   ```bash
   screen /dev/ttyUSB0 9600
   ```

4. Press Enter to activate the console

To exit screen: Press `Ctrl+A` then `K` and confirm with `y`

### Using PuTTY (Windows/Linux)

1. Download and install [PuTTY](https://www.putty.org/)
2. Connect the serial cable to your Juniper device's console port
3. Open PuTTY and configure:
   - **Connection type**: Serial
   - **Serial line**: COM3 (Windows) or /dev/ttyUSB0 (Linux)
   - **Speed**: 9600
4. Click "Open" to connect

## Network Access via Default Configuration

Juniper devices with factory default configuration allow for easy initial access through any network port.

### Default Network Behavior

> [!NOTE]
> The default configuration works on **any Ethernet port**, not just the first one (ge-0/0/0).

When a Juniper device is in its default factory state:

1. **Plug into any port**: Connect your computer to any available Ethernet port on the device
2. **Wait for DHCP**: After 1-2 minutes, the device will automatically assign your computer an IP address in the `192.168.1.0/24` subnet
3. **Device IP**: The Juniper device uses the IP address `192.168.1.1`

### SSH Access with Default Config

Once your computer has an IP address in the 192.168.1.0/24 range, you can SSH directly:

```bash
ssh root@192.168.1.1
```

**Default credentials:**
- **Username**: `root`
- **Password**: (no password - just press Enter)

> [!IMPORTANT]
> Default configuration with no password is insecure and should only be used for initial setup. Always configure proper authentication before deploying in production.

### Example Initial Setup Session

```bash
# Connect via SSH
ssh root@192.168.1.1

# Enter CLI mode
cli

# Set root password
configure
set system root-authentication plain-text-password
# Enter new password twice

# Set hostname
set system host-name my-juniper-device

# Configure management interface
set interfaces ge-0/0/0 unit 0 family inet address 10.0.0.1/24

# Commit changes
commit
```

## Troubleshooting

**No IP address assigned:**
- Wait up to 5 minutes for DHCP
- Check cable connection
- Verify factory default configuration: `show configuration | display set`

**Cannot SSH to 192.168.1.1:**
- Verify your IP is in 192.168.1.0/24: `ip addr` (Linux) or `ipconfig` (Windows)
- Try pinging: `ping 192.168.1.1`
- Use serial console instead

**Serial console shows no output:**
- Verify baud rate is 9600
- Check cable is connected to CONSOLE port (not AUX)
- Press Enter several times to activate
