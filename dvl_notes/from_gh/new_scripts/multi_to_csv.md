# File Processing Functions in Multiple Languages

Below are implementations in Bash, Perl, Python, and PowerShell that read a file, process variables, and output to a CSV file.

## 1. Bash Function

```bash
#!/bin/bash

# Function to process file and output to CSV
process_to_csv() {
    local input_file=$1
    local output_csv=$2
    local var1=$3
    local var2=$4
    
    # Write CSV header
    echo "Variable1,Variable2,LineNumber,LineContent" > "$output_csv"
    
    # Process input file
    line_number=1
    while IFS= read -r line; do
        # Process each line and write to CSV
        echo "\"$var1\",\"$var2\",\"$line_number\",\"${line//\"/\"\"}\"" >> "$output_csv"
        ((line_number++))
    done < "$input_file"
    
    echo "Processing complete. Output saved to $output_csv"
}

# Example usage:
# process_to_csv "input.txt" "output.csv" "value1" "value2"
```

## 2. Perl Function

```perl
#!/usr/bin/perl
use strict;
use warnings;
use Text::CSV;

sub process_to_csv {
    my ($input_file, $output_csv, $var1, $var2) = @_;
    
    open(my $in_fh, '<', $input_file) or die "Cannot open $input_file: $!";
    open(my $out_fh, '>', $output_csv) or die "Cannot open $output_csv: $!";
    
    my $csv = Text::CSV->new({ binary => 1, eol => "\n" });
    
    # Write CSV header
    $csv->print($out_fh, ['Variable1', 'Variable2', 'LineNumber', 'LineContent']);
    
    # Process input file
    my $line_number = 1;
    while (my $line = <$in_fh>) {
        chomp $line;
        $csv->print($out_fh, [$var1, $var2, $line_number, $line]);
        $line_number++;
    }
    
    close $in_fh;
    close $out_fh;
    
    print "Processing complete. Output saved to $output_csv\n";
}

# Example usage:
# process_to_csv("input.txt", "output.csv", "value1", "value2");
```

## 3. Python Function

```python
import csv

def process_to_csv(input_file, output_csv, var1, var2):
    """
    Read input file, process with variables, and output to CSV
    
    Args:
        input_file (str): Path to input file
        output_csv (str): Path to output CSV file
        var1 (str): First variable to include in output
        var2 (str): Second variable to include in output
    """
    with open(input_file, 'r', encoding='utf-8') as infile, \
         open(output_csv, 'w', newline='', encoding='utf-8') as outfile:
        
        writer = csv.writer(outfile, quoting=csv.QUOTE_ALL)
        
        # Write CSV header
        writer.writerow(['Variable1', 'Variable2', 'LineNumber', 'LineContent'])
        
        # Process input file
        for line_number, line in enumerate(infile, start=1):
            cleaned_line = line.strip()
            writer.writerow([var1, var2, line_number, cleaned_line])
    
    print(f"Processing complete. Output saved to {output_csv}")

# Example usage:
# process_to_csv("input.txt", "output.csv", "value1", "value2")
```

## 4. PowerShell Function

```powershell
function Process-ToCsv {
    param(
        [string]$InputFile,
        [string]$OutputCsv,
        [string]$Var1,
        [string]$Var2
    )
    
    # Read input file
    $lines = Get-Content -Path $InputFile
    
    # Prepare CSV output
    $csvData = @()
    
    # Add CSV header
    $csvData += [PSCustomObject]@{
        Variable1 = "Variable1"
        Variable2 = "Variable2"
        LineNumber = "LineNumber"
        LineContent = "LineContent"
    }
    
    # Process each line
    $lineNumber = 1
    foreach ($line in $lines) {
        $csvData += [PSCustomObject]@{
            Variable1 = $Var1
            Variable2 = $Var2
            LineNumber = $lineNumber
            LineContent = $line
        }
        $lineNumber++
    }
    
    # Export to CSV
    $csvData | Export-Csv -Path $OutputCsv -NoTypeInformation
    
    Write-Host "Processing complete. Output saved to $OutputCsv"
}

# Example usage:
# Process-ToCsv -InputFile "input.txt" -OutputCsv "output.csv" -Var1 "value1" -Var2 "value2"
```

## Common Features

All implementations:
1. Take an input file path, output CSV path, and two variables as parameters
2. Include the variables in each row of the output
3. Add line numbers from the original file
4. Include the original line content
5. Properly handle CSV formatting and escaping
6. Provide feedback when complete

You can customize the processing logic in each language as needed for your specific requirements.
