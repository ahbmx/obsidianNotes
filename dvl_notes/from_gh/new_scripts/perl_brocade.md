Great — with passwordless SSH, the script simplifies a bit. And switching the output format to CSV is straightforward.

---

### ✅ **Updated Features:**

* Uses **passwordless SSH** (via key-based auth).
* **Runs Brocade commands** on each switch.
* **Stores output in CSV format** (one CSV per command, each row is: `switch_ip`, `parsed_data`).

---

### 🧾 **Updated Perl Script (`brocade_collect_csv.pl`):**

```perl
#!/usr/bin/perl
use strict;
use warnings;
use Net::OpenSSH;
use Text::CSV;
use File::Path qw(make_path);

# Configuration
my @switches = (
    '10.0.0.1',
    '10.0.0.2'
);

my @commands = qw(firmwareshow alishow switchshow);
my $output_dir = "csv_outputs";

make_path($output_dir) unless -d $output_dir;

# Initialize CSV writers
my %csv_writers;
foreach my $cmd (@commands) {
    my $file = "$output_dir/${cmd}.csv";
    open my $fh, '>', $file or die "Cannot write to $file: $!";
    my $csv = Text::CSV->new({ binary => 1, eol => $/ });
    $csv->print($fh, ['switch_ip', 'parsed_data']);
    $csv_writers{$cmd} = { fh => $fh, csv => $csv };
}

# Process switches
foreach my $host (@switches) {
    print "Connecting to $host...\n";
    my $ssh = Net::OpenSSH->new($host, timeout => 10);
    if ($ssh->error) {
        warn "Couldn't connect to $host: " . $ssh->error;
        next;
    }

    foreach my $cmd (@commands) {
        print "Running '$cmd' on $host...\n";
        my ($output, $err) = $ssh->capture2($cmd);

        if ($err) {
            warn "Error running $cmd on $host: $err";
            next;
        }

        my @parsed_lines;

        for my $line (split /\n/, $output) {
            chomp $line;

            if ($cmd eq 'firmwareshow' && $line =~ /v(\d+\.\d+\.\d+)/) {
                push @parsed_lines, "Firmware v$1";
            }
            elsif ($cmd eq 'alishow' && $line =~ /^alias:\s*(\S+)/) {
                push @parsed_lines, "Alias: $1";
            }
            elsif ($cmd eq 'switchshow' && $line =~ /switchName:\s*(\S+)/) {
                push @parsed_lines, "Switch Name: $1";
            }
        }

        my $csv = $csv_writers{$cmd}->{csv};
        my $fh = $csv_writers{$cmd}->{fh};
        if (@parsed_lines) {
            foreach my $line (@parsed_lines) {
                $csv->print($fh, [$host, $line]);
            }
        } else {
            $csv->print($fh, [$host, "No matching data"]);
        }
    }
}

# Close CSV files
foreach my $writer (values %csv_writers) {
    close $writer->{fh};
}

print "CSV output written to $output_dir/\n";
```

---

### 📁 **Output Example**:

**firmwareshow\.csv**

```
switch_ip,parsed_data
10.0.0.1,Firmware v9.1.0
10.0.0.2,Firmware v9.1.1
```

**alishow\.csv**

```
switch_ip,parsed_data
10.0.0.1,Alias: SERVER01
10.0.0.1,Alias: STORAGE01
```

---

### 🔧 Tips:

* You can further enhance parsing for more specific fields.
* This CSV output can be easily imported into Excel, Python, etc.
* Add `Log::Log4perl` or similar for structured logging if you want verbosity control.

Would you like to expand this to include error logs or JSON export?
