# solus-os-panic-wipe
Securely erase all connected drives as quickly as possible on Solus OS.

## Usage:

To use this program on Solus, you'll need to:

1. Save it as a C file (e.g., ```panic_wipe.c```)
2. Compile it with:
```bash
gcc panic_wipe.c -o panic_wipe
```
3. Make it executable:
```bash
chmod +x panic_wipe
```
4. Install required dependencies:
```bash
sudo eopkg install -y cryptsetup blkdiscard xdg-utils
```

You can then use it similarly to the Qubes version, but on Solus.

### Features:

Simply running ```panic``` will trigger a wipe of all devices. A basic wipe consists of 4 steps:

- **Wipe the headers of all LUKS devices**:  
  For users running HDDs, this step alone should be enough to render the data irrecoverable.

- **Secure discard all data on all storage devices**:  
  Only some SSDs support secure discard.

- **Normal (insecure) discard on all storage devices**:  
  This accounts for SSDs without secure discard support but doesn't provide as strong security guarantees.

- **Writes a notice to every connected storage device**:  
  Informing any attacker that the data has been wiped irrecoverably.  
  This should deter them from attempting to force you to unlock your drive (e.g., legal threats or torture), since there's nothing to unlock.  
  *Note:* If the attacker suspects that you still have a usable backup, they might try to force you to unlock your backup instead.

The panic handler has multiple modes:

#### Shutdown modes

- **Immediate poweroff (default)**:  
  This mode is specified by running ```panic --immediate``` or by simply running ```panic``` with no options. It powers off the system as soon as the data is wiped.

- **Delayed poweroff**:  
  This mode is specified by running ```panic --delay <seconds>```. It waits ```<seconds>``` before powering off to allow the drive more time to TRIM discarded blocks.  
  This mode is potentially more vulnerable to cold boot attacks since the contents of RAM aren't deleted until power is lost.

- **Reboot**:  
  This mode is specified by running ```panic --reboot```. It reboots the system instead of powering off, allowing the drive to TRIM discarded blocks after RAM is cleared.  
  This mode trusts the BIOS to zero RAM immediately on power-on. On systems that fail to do this, cold boot attacks may be possible.

#### Wipe modes

- **DISCARD (default)**:  
  This wipe mode is effective on the widest range of drives.

- **OPAL *(untested)*:  
  This wipe mode tells OPAL self-encrypting drives to perform a factory reset.

---

### Installation:

1. **Download the script**:  
   Save it as a C file (e.g., ```panic.c```).

2. **Install required dependencies**:  
   Open a terminal and run:  
   ```bash
   sudo eopkg install -y gcc cryptsetup blkdiscard xdg-utils
   ```

3. **Compile the script**:  
   ```bash
   gcc panic.c -o panic
   ```

4. **Make it executable**:  
   ```bash
   chmod +x panic
   ```

5. **Install the binary**:  
   Move it to a directory in your system's ```$PATH``` (e.g., ```/usr/local/bin```):  
   ```bash
   sudo mv panic /usr/local/bin/
   ```

---

### Keybinding (Optional):

You can bind this script to a keybind for quick access. Ensure the keybind isn't something you'll accidentally trigger, as this script will irrecoverably erase ALL data on EVERY storage device it can find.

To bind it to a key, follow these steps:

1. **Edit your desktop environment's keyboard shortcuts**:  
   For example, in XFCE (the default desktop for Solus), go to **Settings > Keyboard > Application Shortcuts**.

2. **Add a new shortcut**:  
   - **Command**: ```/usr/local/bin/panic```
   - **Shortcut**: Choose a key combination that's hard to press accidentally (e.g., ```Ctrl+Shift+Delete```).

3. **Test the shortcut**:  
   Ensure it works as expected.

---

### Debugging:

You can test the script without performing any destructive actions by running:  
```bash
panic --dbg=dry-run
```

**Warning:** Arguments are processed in the order they're passed. To avoid data loss, ```--dbg=dry-run``` must be the *last* argument passed. Any arguments that come later will override its behavior.

For additional assurance, you can test the script in a VM or on a system without important data.

---

### Notes:

- **OPAL erase mode is untested**:  
  This feature has not yet been tested on Solus.

- **Passwordless sudo**:  
  To bind this script to a keybind, ensure that ```sudo``` is configured to run without a password prompt. This is necessary for non-interactive execution.

- **Use with caution**:  
  This script permanently wipes data. Use it only when absolutely necessary.

---

### Security Considerations:

- **Download securely**:  
  Download the script over a secure connection (e.g., HTTPS) to prevent tampering.

- **Review the source code**:  
  Before compiling and running the script, review the source code to ensure it hasn't been tampered with or contains malicious behavior.

- **Test in a safe environment**:  
  Always test the script in a non-critical environment before using it on your primary system.

- Download our [**lightweight & efficient clipboard cleaning tool for Solus OS**](https://github.com/Jeyso215/CleanSlate-Solus-OS)

---

By following these steps, you can set up and use the panic script on Solus 4.7 Endurance x86_64 to securely wipe your devices in emergency situations.
