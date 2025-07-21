# Bash, Perl, Python, and PowerShell Boilerplate Script Templates

Here are boilerplate templates for each scripting language with common best practices included.

## Bash Script Template

```bash
#!/usr/bin/env bash

# Bash Boilerplate Template
# Version: 1.0.0
# Author: Your Name
# Date: $(date +'%Y-%m-%d')

set -o errexit   # Exit on most errors
set -o nounset   # Disallow unset variables
set -o pipefail  # Use last non-zero exit code in pipelines
#set -o xtrace  # Uncomment for debugging

### Constants
readonly SCRIPT_NAME="$(basename "$0")"
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly VERSION="1.0.0"

### Functions
function usage() {
    cat <<EOF
${SCRIPT_NAME} v${VERSION}
Description of what this script does.

Usage: ${SCRIPT_NAME} [options] <required_arg>

Options:
  -h, --help      Show this help message
  -v, --version   Show version information
  -d, --debug     Enable debug mode
  -f, --file FILE Specify input file

Examples:
  ${SCRIPT_NAME} -f input.txt required_value
EOF
}

function log() {
    local level="$1"
    local message="$2"
    local timestamp=$(date +"%Y-%m-%d %T")
    echo "[${timestamp}] [${level}] ${message}"
}

function main() {
    # Main script logic goes here
    log "INFO" "Script started"
    
    # Example argument processing
    local input_file=""
    local required_arg=""
    
    while [[ "$#" -gt 0 ]]; do
        case "$1" in
            -h|--help) usage; exit 0 ;;
            -v|--version) echo "${VERSION}"; exit 0 ;;
            -d|--debug) set -o xtrace ;;
            -f|--file) input_file="$2"; shift ;;
            *) required_arg="$1" ;;
        esac
        shift
    done
    
    [[ -z "$required_arg" ]] && { log "ERROR" "Missing required argument"; usage; exit 1; }
    [[ -f "$input_file" ]] || { log "ERROR" "Input file not found: ${input_file}"; exit 1; }
    
    log "INFO" "Processing file ${input_file} with argument ${required_arg}"
    # Rest of your script logic
    
    log "INFO" "Script completed successfully"
}

### Main Execution
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    main "$@"
fi
```

## Perl Script Template

```perl
#!/usr/bin/env perl

# Perl Boilerplate Template
# Version: 1.0.0
# Author: Your Name
# Date: 2023-11-15

use strict;
use warnings;
use feature 'say';
use Getopt::Long qw(GetOptions);
use Pod::Usage qw(pod2usage);
use Carp qw(croak);

### Constants
my $VERSION = '1.0.0';
my $SCRIPT_NAME = $0;

### Variables
my ($help, $version, $debug, $file);
my $required_arg;

### Main Script
main();

### Subroutines
sub main {
    process_args();
    
    say "DEBUG: Starting script execution" if $debug;
    
    # Validate inputs
    unless ($required_arg) {
        croak "ERROR: Required argument missing";
    }
    
    if ($file && !-f $file) {
        croak "ERROR: Input file not found: $file";
    }
    
    # Main logic here
    say "Processing file $file with argument $required_arg" if $file;
    
    say "DEBUG: Script completed successfully" if $debug;
}

sub process_args {
    GetOptions(
        'help|h'    => \$help,
        'version|v' => \$version,
        'debug|d'   => \$debug,
        'file|f=s'  => \$file,
    ) or pod2usage(2);
    
    pod2usage(-verbose => 2, -exitval => 0) if $help;
    
    if ($version) {
        say "$SCRIPT_NAME version $VERSION";
        exit 0;
    }
    
    $required_arg = shift @ARGV;
}

__END__

=head1 NAME

script_name.pl - Brief description of script

=head1 SYNOPSIS

script_name.pl [options] required_arg

 Options:
   -h, --help      Show this help message
   -v, --version   Show version information
   -d, --debug     Enable debug mode
   -f, --file      Specify input file

=head1 DESCRIPTION

Detailed description of what this script does.

=cut
```

## Python Script Template

```python
#!/usr/bin/env python3

"""
Python Boilerplate Template
Version: 1.0.0
Author: Your Name
Date: 2023-11-15
"""

import sys
import os
import argparse
import logging
from typing import Optional

# Constants
VERSION = "1.0.0"
SCRIPT_NAME = os.path.basename(__file__)

# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(message)s",
    handlers=[
        logging.StreamHandler(sys.stdout)
    ]
)
logger = logging.getLogger(__name__)

def main() -> None:
    """Main script function."""
    args = parse_args()
    
    if args.debug:
        logger.setLevel(logging.DEBUG)
    
    logger.debug("Starting script execution")
    
    # Validate inputs
    if not args.required_arg:
        logger.error("Required argument missing")
        sys.exit(1)
        
    if args.file and not os.path.isfile(args.file):
        logger.error(f"Input file not found: {args.file}")
        sys.exit(1)
    
    # Main logic here
    logger.info(f"Processing file {args.file} with argument {args.required_arg}")
    
    logger.info("Script completed successfully")

def parse_args() -> argparse.Namespace:
    """Parse command line arguments."""
    parser = argparse.ArgumentParser(
        description="Description of what this script does.",
        formatter_class=argparse.ArgumentDefaultsHelpFormatter
    )
    
    parser.add_argument(
        "-v", "--version",
        action="version",
        version=f"%(prog)s {VERSION}"
    )
    
    parser.add_argument(
        "-d", "--debug",
        action="store_true",
        help="Enable debug mode"
    )
    
    parser.add_argument(
        "-f", "--file",
        type=str,
        help="Specify input file"
    )
    
    parser.add_argument(
        "required_arg",
        type=str,
        nargs="?",
        help="Required argument description"
    )
    
    return parser.parse_args()

if __name__ == "__main__":
    try:
        main()
    except Exception as e:
        logger.exception(f"Unexpected error: {e}")
        sys.exit(1)
```

## PowerShell Script Template

```powershell
<#
.SYNOPSIS
    Brief description of script
    
.DESCRIPTION
    Detailed description of what this script does
    
.PARAMETER File
    Specifies the input file
    
.PARAMETER Debug
    Enable debug output
    
.EXAMPLE
    PS> .\script_name.ps1 -File input.txt -RequiredArg value
    
.NOTES
    File Name      : script_name.ps1
    Version        : 1.0.0
    Author         : Your Name
    Creation Date  : $(Get-Date -Format "yyyy-MM-dd")
#>

[CmdletBinding()]
param (
    [Parameter(Mandatory=$false)]
    [string]$File,
    
    [Parameter(Mandatory=$false)]
    [switch]$Debug,
    
    [Parameter(Mandatory=$true, Position=0)]
    [string]$RequiredArg
)

begin {
    # Script constants
    $VERSION = "1.0.0"
    $SCRIPT_NAME = $MyInvocation.MyCommand.Name
    
    # Configure logging
    function Write-Log {
        param (
            [string]$Level,
            [string]$Message
        )
        
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        Write-Output "[$timestamp] [$Level] $Message"
    }
    
    if ($Debug) {
        $DebugPreference = "Continue"
    }
    
    Write-Debug "Starting script execution"
}

process {
    try {
        # Validate inputs
        if ($File -and -not (Test-Path -Path $File -PathType Leaf)) {
            Write-Log -Level "ERROR" -Message "Input file not found: $File"
            exit 1
        }
        
        # Main logic here
        Write-Log -Level "INFO" -Message "Processing file $File with argument $RequiredArg"
        
        # Rest of your script logic
        
        Write-Log -Level "INFO" -Message "Script completed successfully"
    }
    catch {
        Write-Log -Level "ERROR" -Message "Unexpected error: $_"
        exit 1
    }
}

end {
    Write-Debug "Script execution completed"
}
```

## Features Common Across All Templates

1. **Metadata**: Script name, version, author, and date
2. **Argument parsing**: Support for common flags (help, version, debug)
3. **Error handling**: Basic error checking and validation
4. **Logging**: Consistent logging format
5. **Documentation**: Usage instructions and examples
6. **Structure**: Clear separation of concerns (argument parsing, main logic, etc.)

You can customize these templates further based on your specific requirements and coding standards.
