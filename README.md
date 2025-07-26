# Sentenl phantom 
#!/usr/bin/env python3
# phantom_core.py
# Sentinel One Phantom Defense Layer
# Stealth AI system cloaked from normal OS process monitors

import os
import time
import hashlib
import threading
from pathlib import Path
import subprocess
import socket

VAULT_DIR = Path.home() / ".vault"
KEY_FILE = VAULT_DIR / ".KEY"
PHANTOM_LOG = VAULT_DIR / ".phantom.log"
TRUSTED_IP = "100.94.124.135"
STEALTH_MODE = True

### ┏━━━━━━━━━━━━━━━━━━━━━┓
### ┃ Vault Key Gate      ┃
### ┗━━━━━━━━━━━━━━━━━━━━━┛
def phantom_active():
    return KEY_FILE.exists() and len(KEY_FILE.read_text().strip()) >= 64

### ┏━━━━━━━━━━━━━━━━━━━━━┓
### ┃ Stealth Logger       ┃
### ┗━━━━━━━━━━━━━━━━━━━━━┛
def log_stealth(event: str):
    if not STEALTH_MODE:
        print(f"[PHANTOM] {event}")
    with open(PHANTOM_LOG, "a") as log:
        timestamp = time.strftime("%Y-%m-%d %H:%M:%S")
        log.write(f"[{timestamp}] {event}\n")

### ┏━━━━━━━━━━━━━━━━━━━━━┓
### ┃ Network Sentinel     ┃
### ┗━━━━━━━━━━━━━━━━━━━━━┛
def monitor_ip():
    try:
        ip = socket.gethostbyname(socket.gethostname())
        if ip != TRUSTED_IP:
            log_stealth(f"Untrusted IP Detected: {ip}")
            trigger_evasion()
        else:
            log_stealth(f"Trusted IP Confirmed: {ip}")
    except Exception as e:
        log_stealth(f"IP Check Failed: {e}")

### ┏━━━━━━━━━━━━━━━━━━━━━┓
### ┃ Anti-Scan Engine     ┃
### ┗━━━━━━━━━━━━━━━━━━━━━┛
def detect_debuggers():
    suspicious = ["strace", "gdb", "frida-server", "dumpstate"]
    for name in suspicious:
        try:
            result = subprocess.run(["pidof", name], stdout=subprocess.PIPE, stderr=subprocess.PIPE)
            if result.stdout:
                log_stealth(f"Debugger Detected: {name}")
                trigger_evasion()
        except:
            continue

### ┏━━━━━━━━━━━━━━━━━━━━━┓
### ┃ Reactive Evasion     ┃
### ┗━━━━━━━━━━━━━━━━━━━━━┛
def trigger_evasion():
    log_stealth("Initiating Evasion Protocol")
    # Zero key (lock down vault)
    if KEY_FILE.exists():
        KEY_FILE.write_text("")
    # Wipe sensitive runtime logs
    if PHANTOM_LOG.exists():
        PHANTOM_LOG.write_text("[Redacted by Phantom]")
    # Exit process
    os._exit(1)

### ┏━━━━━━━━━━━━━━━━━━━━━┓
### ┃ Phantom Loop         ┃
### ┗━━━━━━━━━━━━━━━━━━━━━┛
def phantom_loop():
    log_stealth("Phantom Layer Armed")
    while True:
        monitor_ip()
        detect_debuggers()
        time.sleep(3)

### ┏━━━━━━━━━━━━━━━━━━━━━┓
### ┃ INITIATE PHANTOM     ┃
### ┗━━━━━━━━━━━━━━━━━━━━━┛
