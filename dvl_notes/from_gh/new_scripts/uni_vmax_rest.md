# Enhanced Python Script with Pagination Support

I've updated the script to include pagination support with a default page size of 1000. This is particularly important for endpoints that might return large datasets like devices, initiators, or storage groups.

```python
import requests
import json
import csv
import logging
from concurrent.futures import ThreadPoolExecutor
from datetime import datetime, timedelta
import os
import time

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('vmax_data_collection.log'),
        logging.StreamHandler()
    ]
)

# Configuration
SERVERS = {
    'server1': {
        'ip': '10.0.0.1',
        'username': 'username1',
        'password': 'password1'
    },
    'server2': {
        'ip': '10.0.0.2',
        'username': 'username2',
        'password': 'password2'
    }
}

BASE_URL = "https://{ip}/univmax/restapi"
HEADERS = {
    'Content-Type': 'application/json',
    'Accept': 'application/json'
}

# Pagination configuration
PAGE_SIZE = 1000  # Default page size for paginated requests

# Output directory
OUTPUT_DIR = 'vmax_reports'
os.makedirs(OUTPUT_DIR, exist_ok=True)

def get_auth_token(server_config):
    """Authenticate and get session token"""
    url = BASE_URL.format(ip=server_config['ip']) + "/version"
    auth_url = BASE_URL.format(ip=server_config['ip']) + "/version/symmetrix/auth"
    
    try:
        # First get the API version
        response = requests.get(url, headers=HEADERS, verify=False)
        if response.status_code != 200:
            logging.error(f"Failed to get API version from {server_config['ip']}")
            return None
        
        version = response.json()['version']
        HEADERS['X-EMC-VERSION'] = version
        
        # Then authenticate
        auth_payload = {
            "username": server_config['username'],
            "password": server_config['password']
        }
        
        response = requests.post(
            auth_url,
            headers=HEADERS,
            data=json.dumps(auth_payload),
            verify=False
        )
        
        if response.status_code == 200:
            token = response.headers['EMC-CSRF-TOKEN']
            HEADERS['EMC-CSRF-TOKEN'] = token
            cookies = response.cookies
            logging.info(f"Successfully authenticated to {server_config['ip']}")
            return cookies
        else:
            logging.error(f"Authentication failed for {server_config['ip']}: {response.text}")
            return None
            
    except Exception as e:
        logging.error(f"Error during authentication to {server_config['ip']}: {str(e)}")
        return None

def get_paginated_data(url, cookies, server_ip, result_key):
    """Helper function to handle paginated API responses"""
    all_results = []
    params = {
        'page_size': PAGE_SIZE,
        'start_token': ''
    }
    
    while True:
        try:
            response = requests.get(
                url,
                headers=HEADERS,
                cookies=cookies,
                params=params,
                verify=False
            )
            
            if response.status_code == 200:
                data = response.json()
                if result_key in data:
                    all_results.extend(data[result_key])
                
                # Check if there are more pages
                if 'next_token' in data.get('links', {}) and data['links']['next_token']:
                    params['start_token'] = data['links']['next_token']
                else:
                    break
            else:
                logging.error(f"Failed to get paginated data from {url}: {response.text}")
                break
                
        except Exception as e:
            logging.error(f"Error getting paginated data from {url}: {str(e)}")
            break
    
    return all_results

def get_symmetrix_list(cookies, server_ip):
    """Get list of symmetrix arrays"""
    url = BASE_URL.format(ip=server_ip) + "/system/symmetrix"
    try:
        response = requests.get(url, headers=HEADERS, cookies=cookies, verify=False)
        if response.status_code == 200:
            return response.json()['symmetrixId']
        else:
            logging.error(f"Failed to get symmetrix list from {server_ip}: {response.text}")
            return []
    except Exception as e:
        logging.error(f"Error getting symmetrix list from {server_ip}: {str(e)}")
        return []

def get_array_general_info(symmetrix_id, cookies, server_ip):
    """Get general information about the array"""
    url = BASE_URL.format(ip=server_ip) + f"/system/symmetrix/{symmetrix_id}"
    try:
        response = requests.get(url, headers=HEADERS, cookies=cookies, verify=False)
        if response.status_code == 200:
            return response.json()
        else:
            logging.error(f"Failed to get general info for {symmetrix_id} from {server_ip}: {response.text}")
            return None
    except Exception as e:
        logging.error(f"Error getting general info for {symmetrix_id} from {server_ip}: {str(e)}")
        return None

def get_array_capacity(symmetrix_id, cookies, server_ip):
    """Get capacity information"""
    url = BASE_URL.format(ip=server_ip) + f"/sloprovisioning/symmetrix/{symmetrix_id}"
    try:
        response = requests.get(url, headers=HEADERS, cookies=cookies, verify=False)
        if response.status_code == 200:
            return response.json()
        else:
            logging.error(f"Failed to get capacity info for {symmetrix_id} from {server_ip}: {response.text}")
            return None
    except Exception as e:
        logging.error(f"Error getting capacity info for {symmetrix_id} from {server_ip}: {str(e)}")
        return None

def get_array_health(symmetrix_id, cookies, server_ip):
    """Get health status"""
    url = BASE_URL.format(ip=server_ip) + f"/system/symmetrix/{symmetrix_id}/health"
    try:
        response = requests.get(url, headers=HEADERS, cookies=cookies, verify=False)
        if response.status_code == 200:
            return response.json()
        else:
            logging.error(f"Failed to get health info for {symmetrix_id} from {server_ip}: {response.text}")
            return None
    except Exception as e:
        logging.error(f"Error getting health info for {symmetrix_id} from {server_ip}: {str(e)}")
        return None

def get_failed_disks(symmetrix_id, cookies, server_ip):
    """Get failed disks information"""
    url = BASE_URL.format(ip=server_ip) + f"/system/symmetrix/{symmetrix_id}/disk"
    try:
        response = requests.get(url, headers=HEADERS, cookies=cookies, verify=False)
        if response.status_code == 200:
            disks = response.json()['disk']
            failed_disks = [disk for disk in disks if disk['status'] != 'OK']
            return failed_disks
        else:
            logging.error(f"Failed to get disk info for {symmetrix_id} from {server_ip}: {response.text}")
            return []
    except Exception as e:
        logging.error(f"Error getting disk info for {symmetrix_id} from {server_ip}: {str(e)}")
        return []

def get_fa_status(symmetrix_id, cookies, server_ip):
    """Get FA (Front Adapter) status"""
    url = BASE_URL.format(ip=server_ip) + f"/system/symmetrix/{symmetrix_id}/director"
    try:
        response = requests.get(url, headers=HEADERS, cookies=cookies, verify=False)
        if response.status_code == 200:
            directors = response.json()['directorId']
            fa_directors = [dir for dir in directors if dir.startswith('FA')]
            fa_status = []
            
            for director in fa_directors:
                dir_url = BASE_URL.format(ip=server_ip) + f"/system/symmetrix/{symmetrix_id}/director/{director}"
                dir_response = requests.get(dir_url, headers=HEADERS, cookies=cookies, verify=False)
                if dir_response.status_code == 200:
                    fa_status.append(dir_response.json())
            
            return fa_status
        else:
            logging.error(f"Failed to get director info for {symmetrix_id} from {server_ip}: {response.text}")
            return []
    except Exception as e:
        logging.error(f"Error getting director info for {symmetrix_id} from {server_ip}: {str(e)}")
        return []

def get_initiators_hosts(symmetrix_id, cookies, server_ip):
    """Get list of initiators and hosts with pagination"""
    url = BASE_URL.format(ip=server_ip) + f"/sloprovisioning/symmetrix/{symmetrix_id}/initiator"
    return get_paginated_data(url, cookies, server_ip, 'initiatorId')

def get_devices(symmetrix_id, cookies, server_ip):
    """Get list of all devices with pagination"""
    url = BASE_URL.format(ip=server_ip) + f"/sloprovisioning/symmetrix/{symmetrix_id}/volume"
    return get_paginated_data(url, cookies, server_ip, 'volumeId')

def get_storage_groups(symmetrix_id, cookies, server_ip):
    """Get list of storage groups (views) with pagination"""
    url = BASE_URL.format(ip=server_ip) + f"/sloprovisioning/symmetrix/{symmetrix_id}/storagegroup"
    return get_paginated_data(url, cookies, server_ip, 'storageGroupId')

def get_symaudit(symmetrix_id, cookies, server_ip):
    """Get symaudit logs with pagination"""
    url = BASE_URL.format(ip=server_ip) + f"/system/symmetrix/{symmetrix_id}/audit_log"
    return get_paginated_data(url, cookies, server_ip, 'audit_log')

def get_symevent(symmetrix_id, cookies, server_ip):
    """Get symevent logs for last 24 hours with pagination"""
    end_time = datetime.utcnow()
    start_time = end_time - timedelta(days=1)
    
    url = BASE_URL.format(ip=server_ip) + f"/system/symmetrix/{symmetrix_id}/events"
    params = {
        'start_time': start_time.strftime('%Y-%m-%dT%H:%M:%SZ'),
        'end_time': end_time.strftime('%Y-%m-%dT%H:%M:%SZ'),
        'page_size': PAGE_SIZE,
        'start_token': ''
    }
    
    all_events = []
    
    while True:
        try:
            response = requests.get(
                url,
                headers=HEADERS,
                cookies=cookies,
                params=params,
                verify=False
            )
            
            if response.status_code == 200:
                data = response.json()
                if 'events' in data:
                    all_events.extend(data['events'])
                
                # Check if there are more pages
                if 'next_token' in data.get('links', {}) and data['links']['next_token']:
                    params['start_token'] = data['links']['next_token']
                else:
                    break
            else:
                logging.error(f"Failed to get symevent for {symmetrix_id} from {server_ip}: {response.text}")
                break
                
        except Exception as e:
            logging.error(f"Error getting symevent for {symmetrix_id} from {server_ip}: {str(e)}")
            break
    
    return all_events

def write_to_csv(data, filename, fieldnames=None):
    """Write data to CSV file"""
    if not data:
        return
        
    full_path = os.path.join(OUTPUT_DIR, filename)
    
    try:
        with open(full_path, 'w', newline='') as csvfile:
            if fieldnames:
                writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
                writer.writeheader()
                writer.writerows(data)
            else:
                if isinstance(data, list):
                    if data and isinstance(data[0], dict):
                        fieldnames = data[0].keys()
                        writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
                        writer.writeheader()
                        writer.writerows(data)
                    else:
                        writer = csv.writer(csvfile)
                        writer.writerows([[item] for item in data])
                else:
                    writer = csv.writer(csvfile)
                    writer.writerow([data])
        
        logging.info(f"Successfully wrote data to {full_path}")
    except Exception as e:
        logging.error(f"Error writing to CSV file {full_path}: {str(e)}")

def process_server(server_name, server_config):
    """Process a single server"""
    logging.info(f"Starting processing for server: {server_name}")
    
    # Authenticate
    cookies = get_auth_token(server_config)
    if not cookies:
        logging.error(f"Authentication failed for {server_name}. Skipping...")
        return
        
    server_ip = server_config['ip']
    
    # Get symmetrix list
    symmetrix_ids = get_symmetrix_list(cookies, server_ip)
    if not symmetrix_ids:
        logging.error(f"No symmetrix arrays found on {server_name}. Skipping...")
        return
        
    for symmetrix_id in symmetrix_ids:
        logging.info(f"Processing symmetrix array: {symmetrix_id}")
        
        # Create directory for this array
        array_dir = os.path.join(OUTPUT_DIR, f"{server_name}_{symmetrix_id}")
        os.makedirs(array_dir, exist_ok=True)
        
        # Get all data in parallel
        with ThreadPoolExecutor(max_workers=5) as executor:
            # Submit all tasks
            general_info_future = executor.submit(get_array_general_info, symmetrix_id, cookies, server_ip)
            capacity_future = executor.submit(get_array_capacity, symmetrix_id, cookies, server_ip)
            health_future = executor.submit(get_array_health, symmetrix_id, cookies, server_ip)
            failed_disks_future = executor.submit(get_failed_disks, symmetrix_id, cookies, server_ip)
            fa_status_future = executor.submit(get_fa_status, symmetrix_id, cookies, server_ip)
            initiators_future = executor.submit(get_initiators_hosts, symmetrix_id, cookies, server_ip)
            devices_future = executor.submit(get_devices, symmetrix_id, cookies, server_ip)
            storage_groups_future = executor.submit(get_storage_groups, symmetrix_id, cookies, server_ip)
            symaudit_future = executor.submit(get_symaudit, symmetrix_id, cookies, server_ip)
            symevent_future = executor.submit(get_symevent, symmetrix_id, cookies, server_ip)
            
            # Wait for all futures to complete and get results
            general_info = general_info_future.result()
            capacity = capacity_future.result()
            health = health_future.result()
            failed_disks = failed_disks_future.result()
            fa_status = fa_status_future.result()
            initiators = initiators_future.result()
            devices = devices_future.result()
            storage_groups = storage_groups_future.result()
            symaudit = symaudit_future.result()
            symevent = symevent_future.result()
        
        # Write data to CSV files
        write_to_csv(general_info, f"{server_name}_{symmetrix_id}_general_info.csv")
        write_to_csv(capacity, f"{server_name}_{symmetrix_id}_capacity.csv")
        write_to_csv(health, f"{server_name}_{symmetrix_id}_health.csv")
        write_to_csv(failed_disks, f"{server_name}_{symmetrix_id}_failed_disks.csv")
        write_to_csv(fa_status, f"{server_name}_{symmetrix_id}_fa_status.csv")
        write_to_csv(initiators, f"{server_name}_{symmetrix_id}_initiators.csv")
        write_to_csv(devices, f"{server_name}_{symmetrix_id}_devices.csv")
        write_to_csv(storage_groups, f"{server_name}_{symmetrix_id}_storage_groups.csv")
        write_to_csv(symaudit, f"{server_name}_{symmetrix_id}_symaudit.csv")
        write_to_csv(symevent, f"{server_name}_{symmetrix_id}_symevent.csv")
        
        logging.info(f"Completed processing for symmetrix array: {symmetrix_id}")
    
    logging.info(f"Completed processing for server: {server_name}")

def main():
    start_time = time.time()
    logging.info("Starting VMAX data collection script")
    
    # Process servers in parallel
    with ThreadPoolExecutor(max_workers=2) as executor:
        futures = []
        for server_name, server_config in SERVERS.items():
            futures.append(executor.submit(process_server, server_name, server_config))
        
        # Wait for all futures to complete
        for future in futures:
            future.result()
    
    end_time = time.time()
    logging.info(f"Script completed in {end_time - start_time:.2f} seconds")

if __name__ == "__main__":
    # Disable SSL warnings for self-signed certificates
    requests.packages.urllib3.disable_warnings()
    main()
```

## Key Pagination Enhancements:

1. **Pagination Configuration**:
   - Added `PAGE_SIZE` constant set to 1000 as requested
   - This value can be easily modified if needed

2. **New Helper Function**:
   - Created `get_paginated_data()` to handle paginated API responses consistently
   - This function manages the pagination loop using `start_token` and `next_token`
   - Automatically handles the accumulation of results across pages

3. **Updated Endpoint Functions**:
   - Modified the following functions to use pagination:
     - `get_initiators_hosts()`
     - `get_devices()`
     - `get_storage_groups()`
     - `get_symaudit()`
     - `get_symevent()`

4. **Special Handling for Symevent**:
   - The symevent function has custom date filtering but now also includes pagination
   - Maintains the 24-hour filter while properly handling large result sets

5. **Error Handling**:
   - The pagination logic includes robust error handling
   - If an error occurs during pagination, it will log the error and return whatever data was successfully collected

## How Pagination Works:

1. The first request is made with the configured `page_size` and an empty `start_token`
2. The response includes:
   - A portion of the results (up to `page_size` items)
   - A `next_token` if more results are available
3. Subsequent requests use the `next_token` to get the next page of results
4. This continues until no more `next_token` is provided
5. All results are accumulated and returned as a single list

This approach ensures that all data is collected regardless of the total size, while maintaining manageable request sizes and avoiding timeouts or memory issues.

The script maintains all the original functionality while adding this important scalability feature for environments with large numbers of devices, initiators, or storage groups.
