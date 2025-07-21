# Running a Perl Script to Read a File Line by Line and Save Output to CSV in Linux

Here's how you can create and run a Perl script that reads a file line by line and saves the output to a CSV file in Linux:

## 1. Create the Perl Script

First, create a new file (e.g., `process_file.pl`) with the following content:

```perl
#!/usr/bin/perl
use strict;
use warnings;
use Text::CSV;

# Check for correct number of arguments
if (@ARGV != 2) {
    die "Usage: $0 <input_file> <output_csv>\n";
}

my ($input_file, $output_csv) = @ARGV;

# Create CSV object
my $csv = Text::CSV->new({ binary => 1, auto_diag => 1, eol => "\n" });

# Open input and output files
open(my $in_fh, '<', $input_file) or die "Could not open '$input_file': $!";
open(my $out_fh, '>', $output_csv) or die "Could not open '$output_csv': $!";

# Process each line
while (my $line = <$in_fh>) {
    chomp $line;
    
    # Split line into fields (modify this according to your needs)
    my @fields = split /\s+/, $line;  # Splits on whitespace
    
    # Write to CSV
    $csv->print($out_fh, \@fields);
}

# Close files
close $in_fh;
close $out_fh;

print "Processing complete. Output saved to $output_csv\n";
```

## 2. Make the Script Executable

Run this command to make the script executable:

```bash
chmod +x process_file.pl
```

## 3. Install Required Perl Module (if needed)

The script uses the `Text::CSV` module. If you don't have it installed, run:

```bash
sudo cpan install Text::CSV
```

## 4. Run the Script

Execute the script with your input file and desired output CSV file:

```bash
./process_file.pl input.txt output.csv
```

## Alternative Simple Version

If you prefer a simpler version without external modules (though less robust for complex CSV):

```perl
#!/usr/bin/perl
use strict;
use warnings;

if (@ARGV != 2) {
    die "Usage: $0 <input_file> <output_csv>\n";
}

my ($input_file, $output_csv) = @ARGV;

open(my $in_fh, '<', $input_file) or die "Could not open '$input_file': $!";
open(my $out_fh, '>', $output_csv) or die "Could not open '$output_csv': $!";

while (my $line = <$in_fh>) {
    chomp $line;
    my @fields = split /\s+/, $line;  # Modify splitting as needed
    
    # Simple CSV output (won't handle commas/quotes in data properly)
    print $out_fh join(',', @fields), "\n";
}

close $in_fh;
close $out_fh;

print "Processing complete. Output saved to $output_csv\n";
```

## Notes:
- The script splits each line on whitespace by default - modify the `split` pattern to match your input format
- The first version using `Text::CSV` is more robust for handling complex CSV requirements
- You may need to adjust the shebang line (`#!/usr/bin/perl`) if Perl is installed elsewhere (find with `which perl`)
