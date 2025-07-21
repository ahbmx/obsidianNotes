# Handling Virtual Fabrics (VF) in Brocade SAN Documentation

Yes, when working with Virtual Fabrics (VF) on Brocade directors running FOS v9.1.1d2, you'll need to account for the VF configuration in both your documentation and command execution. Here's how to properly handle this:

## 1. Virtual Fabric Awareness in Documentation

### Diagram Modifications:
- Each virtual fabric should be represented as a separate logical fabric
- Indicate base switch and virtual fabric IDs in your diagrams
- Show any inter-VF routing connections

### Spreadsheet Modifications:
- Add a "Virtual Fabric ID" column to track which VF each port belongs to
- Include VF-level information like VF_ID and VF_Name

## 2. Command Execution in VF Environment

You have two approaches:

### Option A: Per-VF Execution
```bash
# First get list of all VFs
VF_LIST=$(ssh admin@switch_ip "fabricshow" | awk '/^VF:/ {print $2}')

# Then run commands in each VF context
for VF_ID in $VF_LIST; do
    echo "=== Virtual Fabric $VF_ID ==="
    ssh admin@switch_ip <<EOF
vf $VF_ID
fabricshow
switchshow
portshow
nsshow
EOF
done
```

### Option B: Using the -vf Option (available in many commands)
```bash
# Get VF information first
ssh admin@switch_ip "fabricshow"

# Then run commands with -vf parameter
VF_ID=128  # Example VF ID
ssh admin@switch_ip <<EOF
fabricshow -vf $VF_ID
switchshow -vf $VF_ID
portshow -vf $VF_ID
nsshow -vf $VF_ID
EOF
```

## 3. Enhanced Python Script for VF Environments

```python
import subprocess
import csv

def get_vf_info(switch_ip):
    """Get list of all virtual fabrics on a switch"""
    output = subprocess.run(
        f'ssh admin@{switch_ip} "fabricshow"',
        shell=True, capture_output=True, text=True
    )
    vfs = []
    for line in output.stdout.split('\n'):
        if line.startswith('VF:'):
            parts = line.split()
            vfs.append({'id': parts[1], 'name': parts[2], 'state': parts[3]})
    return vfs

def get_switch_info(switch_ip, vf_id=None):
    """Get switch information for a specific VF or default fabric"""
    cmd_prefix = f'vf {vf_id}; ' if vf_id else ''
    
    commands = {
        'fabricshow': 'fabricshow',
        'switchshow': 'switchshow',
        'portshow': 'portshow',
        'nsshow': 'nsshow'
    }
    
    results = {}
    for name, cmd in commands.items():
        output = subprocess.run(
            f'ssh admin@{switch_ip} "{cmd_prefix}{cmd}"',
            shell=True, capture_output=True, text=True
        )
        results[name] = output.stdout
    
    return results

# Example usage
switch_ip = 'CL100CA'
all_ports = []

for vf in get_vf_info(switch_ip):
    print(f"Processing VF {vf['id']} ({vf['name']})")
    info = get_switch_info(switch_ip, vf['id'])
    # Parse the output and add to all_ports with VF information
    # Add your parsing logic here
```

## 4. Key VF-Specific Commands

These commands are particularly useful in VF environments:

1. `fabricshow` - Shows all virtual fabrics and their states
2. `vfshow` - Displays virtual fabric information
3. `ivrshow` - Shows Inter-Virtual Fabric Routing configuration
4. `portcfgshow` - Displays port configuration including VF assignments
5. `portcfgvf` - Configures virtual fabric for a port

## 5. Sample Output Parsing for VF

When parsing command output in VF environments, look for these indicators:

- In `switchshow`: Ports will show their VF assignment
- In `fabricshow`: The first column indicates VF context
- In `portshow`: The header will indicate current VF context

## 6. Complete Documentation Approach

1. First document the physical chassis and their base configurations
2. Then document each virtual fabric as if it were a separate physical fabric
3. Finally document any inter-VF routing connections
4. Include VF-specific information in all tables and diagrams

Would you like me to provide specific examples of command outputs in VF mode or suggest any particular tools that handle VF documentation well?




# Virtual Fabric (VF) Command Examples and Output Parsing

Here are concrete examples of working with Virtual Fabrics in your Brocade environment, including command outputs and how to parse them:

## 1. Key VF-Specific Commands with Sample Outputs

### `fabricshow` - List all Virtual Fabrics
```bash
ssh admin@CL100CA "fabricshow"

# Sample Output:
Switch ID   Worldwide Name                 Enet IP Addr    FC IP Addr      Name
===============================================================================
1:    1     10:00:00:05:1e:80:7b:00       0.0.0.0         0.0.0.0         default
VF:   128   10:00:00:05:1e:80:7b:80       0.0.0.0         0.0.0.0         PROD_VF1
VF:   129   10:00:00:05:1e:80:7b:81       0.0.0.0         0.0.0.0         BACKUP_VF
```

### `vfshow` - Detailed VF Information
```bash
ssh admin@CL100CA "vfshow 128"

# Sample Output:
Virtual Fabric ID......................128
Virtual Fabric Name....................PROD_VF1
...
Ports assigned.........................1/0-7,2/0-7
```

### `portshow` in VF Context
```bash
ssh admin@CL100CA "vf 128; portshow"

# Sample Output (note the VF indication):
switchName:     CL100CA
switchType:     100.0
switchState:    Online   
switchMode:     Native
switchRole:     Principal
switchDomain:   1
switchId:       fffc01
switchWwn:      10:00:00:05:1e:80:7b:80
port  0: id N8 Online   FC F-Port  50:00:09:72:00:11:22:33 (HOST1)
port  1: id N8 Online   FC F-Port  50:00:09:72:00:11:22:44 (HOST2)
```

## 2. Parsing VF Information

### Python Parser for `fabricshow`
```python
def parse_fabricshow(output):
    vfs = []
    for line in output.split('\n'):
        if line.startswith('VF:'):
            parts = line.split()
            vfs.append({
                'type': 'VF',
                'id': parts[1],
                'wwn': parts[2],
                'name': parts[4]
            })
        elif line.startswith(('1:', '2:', '3:')):  # Base switch
            parts = line.split()
            vfs.append({
                'type': 'Base',
                'id': parts[0].strip(':'),
                'wwn': parts[2],
                'name': parts[4] if len(parts) > 4 else 'default'
            })
    return vfs
```

### Perl Parser for `portshow` with VF
```perl
sub parse_portshow {
    my ($output) = @_;
    my @ports;
    
    foreach my $line (split(/\n/, $output)) {
        if ($line =~ /port\s+(\d+):\s+id\s+(\S+)\s+(\S+)\s+FC\s+(\S+)\s+(\S+)/) {
            push @ports, {
                port => $1,
                state => $3,
                type => $4,
                wwn => $5
            };
        }
    }
    
    return @ports;
}
```

## 3. Complete VF-Aware Data Collection Script

```python
import subprocess
from collections import defaultdict

def collect_vf_data(switch_ip):
    # Step 1: Get all VFs on the switch
    fabricshow = subprocess.run(
        f'ssh admin@{switch_ip} "fabricshow"',
        shell=True, capture_output=True, text=True
    ).stdout
    
    vfs = parse_fabricshow(fabricshow)
    
    # Step 2: Collect data per VF
    results = defaultdict(dict)
    
    for vf in vfs:
        vf_id = vf['id']
        print(f"Collecting data for {vf['type']} fabric {vf_id} ({vf['name']})")
        
        # Get switchshow
        switchshow = subprocess.run(
            f'ssh admin@{switch_ip} "vf {vf_id}; switchshow"',
            shell=True, capture_output=True, text=True
        ).stdout
        results[vf_id]['switchshow'] = parse_switchshow(switchshow)
        
        # Get portshow
        portshow = subprocess.run(
            f'ssh admin@{switch_ip} "vf {vf_id}; portshow"',
            shell=True, capture_output=True, text=True
        ).stdout
        results[vf_id]['portshow'] = parse_portshow(portshow)
        
        # Get nsshow
        nsshow = subprocess.run(
            f'ssh admin@{switch_ip} "vf {vf_id}; nsshow"',
            shell=True, capture_output=True, text=True
        ).stdout
        results[vf_id]['nsshow'] = parse_nsshow(nsshow)
    
    return results
```

## 4. Special Considerations for VF Environments

1. **ISLs Between VFs**: Document any Inter-Virtual Fabric Routing (IVR) connections
   ```bash
   ssh admin@CL100CA "ivrshow"
   ```

2. **Port Assignments**: Track which physical ports are assigned to which VFs
   ```bash
   ssh admin@CL100CA "portcfgshow"
   ```

3. **License Verification**: Ensure proper licenses for VF features
   ```bash
   ssh admin@CL100CA "licenseshow"
   ```

## 5. Example CSV Output Structure with VF

| Switch   | VF_ID | VF_Name  | Slot | Port | Type  | State | Speed | WWN               | Connected_To       |
|----------|-------|----------|------|------|-------|-------|-------|-------------------|--------------------|
| CL100CA  | 128   | PROD_VF1 | 1    | 0    | FC    | Online| 32G   | 10:00:00:11:22:33 | HOST1              |
| CL100CA  | 128   | PROD_VF1 | 1    | 1    | FC    | Online| 32G   | 10:00:00:11:22:34 | HOST2              |
| CL100CA  | 129   | BACKUP_VF| 2    | 0    | FC    | Online| 16G   | 10:00:00:11:22:35 | TL200CA:128/1/0    |

## 6. Visualization Tips for VF Environments

1. Use different colors for different VFs
2. Show physical chassis as containers for virtual fabrics
3. Clearly label IVR connections between VFs
4. Include VF ID and name in all node labels

Would you like me to provide specific examples for:
1. Documenting IVR configurations?
2. Handling mixed VF/non-VF environments?
3. Best practices for VF port allocation tracking?


