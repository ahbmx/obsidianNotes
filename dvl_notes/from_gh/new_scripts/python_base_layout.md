Here’s a complete Python project layout and script blueprint to meet your requirements:

---

### 📁 Project Structure

```
dvl_collector/
├── dvl/
│   ├── __init__.py
│   ├── functionhelper.py
│   ├── sanhelper.py
│   └── pmaxhelper.py
├── main.py
└── requirements.txt
```

---

### 🔧 `dvl/functionhelper.py`

A place to put shared utility functions.

```python
# dvl/functionhelper.py
import logging

def setup_logger():
    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')
```

---

### 📡 `dvl/sanhelper.py`

Collects info from Brocade switches via SSH.

```python
# dvl/sanhelper.py
import paramiko
import logging

def collect_brocade_data(host, user, password):
    logging.info(f"Connecting to Brocade switch at {host}")
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    try:
        ssh.connect(host, username=user, password=password)
        stdin, stdout, stderr = ssh.exec_command("show version")
        output = stdout.read().decode()
        logging.info("Brocade data collected")
        return output
    except Exception as e:
        logging.error(f"Failed to collect Brocade data: {e}")
        return None
    finally:
        ssh.close()
```

---

### 💾 `dvl/pmaxhelper.py`

Collects info from PowerMax using SYMCLI.

```python
# dvl/pmaxhelper.py
import subprocess
import logging

def collect_powermax_data():
    logging.info("Collecting PowerMax data using SYMCLI")
    try:
        result = subprocess.run(["symcfg", "list"], stdout=subprocess.PIPE, stderr=subprocess.PIPE, check=True)
        return result.stdout.decode()
    except subprocess.CalledProcessError as e:
        logging.error(f"SYMCLI command failed: {e.stderr.decode()}")
        return None
```

---

### 🧠 `main.py`

The main driver script.

```python
# main.py
import argparse
import multiprocessing
import time
from dvl.functionhelper import setup_logger
from dvl.sanhelper import collect_brocade_data
from dvl.pmaxhelper import collect_powermax_data

# Example config
BROCADE_CONFIG = {'host': '192.168.1.10', 'user': 'admin', 'password': 'pass'}

def run_brocade():
    data = collect_brocade_data(**BROCADE_CONFIG)
    print("Brocade Data:", data)

def run_powermax():
    data = collect_powermax_data()
    print("PowerMax Data:", data)

def run_all():
    processes = [
        multiprocessing.Process(target=run_brocade),
        multiprocessing.Process(target=run_powermax)
    ]
    for p in processes:
        p.start()
    for p in processes:
        p.join()

def cron_mode(interval_minutes):
    while True:
        print("Running in cron mode...")
        run_all()
        time.sleep(interval_minutes * 60)

if __name__ == "__main__":
    setup_logger()

    parser = argparse.ArgumentParser(description="Data Collector")
    parser.add_argument('--brocade', action='store_true', help='Run Brocade collector')
    parser.add_argument('--powermax', action='store_true', help='Run PowerMax collector')
    parser.add_argument('--all', action='store_true', help='Run all collectors')
    parser.add_argument('--cron', type=int, help='Run in cron mode every N minutes')

    args = parser.parse_args()

    if args.cron:
        cron_mode(args.cron)
    elif args.all:
        run_all()
    else:
        if args.brocade:
            run_brocade()
        if args.powermax:
            run_powermax()
```

---

### 📦 `requirements.txt`

```text
paramiko
```

Run with:

* `python main.py --brocade`
* `python main.py --powermax`
* `python main.py --all`
* `python main.py --cron 10`  → Runs all every 10 minutes

---

Would you like a version using a config file (e.g. JSON or YAML) to avoid hardcoding credentials?
