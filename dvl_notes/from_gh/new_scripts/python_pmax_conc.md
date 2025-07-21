Great — let's enhance the script with:

---

### ✅ Enhancements:

1. **CSV output**: Saves key sections like general info, capacity, initiators, etc.
2. **Logging**: Tracks execution and errors to a logfile.
3. **Parallel requests**: Speeds up requests per array using `concurrent.futures`.

---

### 🛠 Updated Script

This version will:

* Save **one CSV per data type per array**.
* Log activity to `unisphere_query.log`.
* Use a `data/` folder to store outputs.

```python
import requests
import logging
import csv
import os
from datetime import datetime, timedelta
from requests.auth import HTTPBasicAuth
from concurrent.futures import ThreadPoolExecutor
import urllib3

# Disable SSL warnings for self-signed certs
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

# Setup Logging
logging.basicConfig(filename="unisphere_query.log", level=logging.INFO,
                    format="%(asctime)s - %(levelname)s - %(message)s")

# Configuration
UNISPHERE_HOSTS = [
    "https://unisphere1.example.com:8443",
    "https://unisphere2.example.com:8443"
]

USERNAME = "admin"
PASSWORD = "your_password"
HEADERS = {"Content-Type": "application/json"}
DATA_DIR = "data"
os.makedirs(DATA_DIR, exist_ok=True)

# Time window for events
now = datetime.utcnow()
start_time = (now - timedelta(days=1)).strftime('%Y-%m-%dT%H:%M:%SZ')

# ---- Helper Functions ----

def get_json(url, base_url, symm_id=None, params=None):
    full_url = f"{base_url}{url}" if not symm_id else f"{base_url}{url.format(symm_id=symm_id)}"
    try:
        response = requests.get(full_url, headers=HEADERS, auth=HTTPBasicAuth(USERNAME, PASSWORD), verify=False, params=params)
        response.raise_for_status()
        return response.json()
    except Exception as e:
        logging.error(f"Error fetching {full_url}: {e}")
        return {}

def write_csv(filename, fieldnames, rows):
    with open(os.path.join(DATA_DIR, filename), 'w', newline='') as f:
        writer = csv.DictWriter(f, fieldnames=fieldnames)
        writer.writeheader()
        for row in rows:
            writer.writerow(row)

# ---- API Calls ----

def fetch_and_save_array_data(base_url):
    symm_ids = get_json("/univmax/restapi/system/symmetrix", base_url).get("symmetrixId", [])

    for symm_id in symm_ids:
        logging.info(f"Processing array {symm_id} from {base_url}")
        
        # General Info
        gen = get_json("/univmax/restapi/system/symmetrix/{symm_id}", base_url, symm_id)
        write_csv(f"{symm_id}_general_info.csv", gen.keys(), [gen])

        # Capacity
        cap = get_json("/univmax/restapi/system/symmetrix/{symm_id}/capacity", base_url, symm_id)
        write_csv(f"{symm_id}_capacity.csv", cap.keys(), [cap])

        # Health
        health = get_json("/univmax/restapi/system/symmetrix/{symm_id}/health", base_url, symm_id)
        write_csv(f"{symm_id}_health.csv", health.keys(), [health])

        # Disks
        disks = get_json("/univmax/restapi/system/symmetrix/{symm_id}/disk", base_url, symm_id)
        write_csv(f"{symm_id}_disks.csv", ["disk_id", "status"], disks.get("disk", []))

        # Ports
        fa = get_json("/univmax/restapi/system/symmetrix/{symm_id}/port", base_url, symm_id)
        write_csv(f"{symm_id}_fa_ports.csv", ["symmetrixPortKey"], fa.get("symmetrixPort", []))

        # Initiators
        inits = get_json("/univmax/restapi/sloprovisioning/symmetrix/{symm_id}/initiator", base_url, symm_id)
        write_csv(f"{symm_id}_initiators.csv", ["initiatorId"], inits.get("initiatorId", []))

        # Hosts
        hosts = get_json("/univmax/restapi/sloprovisioning/symmetrix/{symm_id}/host", base_url, symm_id)
        write_csv(f"{symm_id}_hosts.csv", ["hostId"], hosts.get("hostId", []))

        # Volumes
        vols = get_json("/univmax/restapi/sloprovisioning/symmetrix/{symm_id}/volume", base_url, symm_id)
        write_csv(f"{symm_id}_volumes.csv", ["volumeId"], vols.get("volumeId", []))

        # Masking Views
        views = get_json("/univmax/restapi/sloprovisioning/symmetrix/{symm_id}/maskingview", base_url, symm_id)
        write_csv(f"{symm_id}_masking_views.csv", ["maskingViewId"], views.get("maskingViewId", []))

        # Symaudit
        audit = get_json("/univmax/restapi/system/symmetrix/{symm_id}/audit", base_url, symm_id, {"startDate": start_time})
        write_csv(f"{symm_id}_symaudit.csv", audit.get("fields", ["time", "description"]), audit.get("auditRecords", []))

        # Symevent
        events = get_json("/univmax/restapi/system/symmetrix/{symm_id}/alert", base_url, symm_id, {"startDate": start_time})
        write_csv(f"{symm_id}_symevents.csv", events.get("fields", ["time", "description"]), events.get("alertRecords", []))

# ---- Main ----

def main():
    with ThreadPoolExecutor(max_workers=4) as executor:
        executor.map(fetch_and_save_array_data, UNISPHERE_HOSTS)

if __name__ == "__main__":
    main()
```

---

### 📁 Output:

All CSVs will be saved in a `data/` directory like:

```
data/
├── 000197900000_general_info.csv
├── 000197900000_capacity.csv
├── ...
```

---

### 🔄 Optional Add-ons (ask if needed):

* Excel `.xlsx` output
* Email alerts for failed disks/events
* Integration with ServiceNow or Slack
* HTML dashboard

Would you like to customize any fields or narrow the data returned?
