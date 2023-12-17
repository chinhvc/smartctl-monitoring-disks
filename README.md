# smartctl-monitoring-disks
Research about S.M.A.R.T tool to monitor disk status

```bash
sudo smartctl -a /dev/sdb
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-5.10.102.1-microsoft-standard-WSL2] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Vendor:               Msft
Product:              Virtual Disk
Revision:             1.0
Compliance:           SPC-3
User Capacity:        274,877,906,944 bytes [274 GB]
Logical block size:   512 bytes
Physical block size:  4096 bytes
LU is thin provisioned, LBPRZ=0
>> Terminate command early due to bad response to IEC mode page
A mandatory SMART command failed: exiting. To continue, add one or more '-T permissive' options.


for ((i=1; i<=n; i++)); 
do 
	sudo smartctl -a /dev/sdb | awk 'NR>4' | sed -e '/^>>/,$d'| jq -Rs 'split("\n") | map(split(":") | map(gsub(" "; "")) | select(length > 1) | {(.[0]): .[1]}) | add'; 
done```

# Explaination
- Only print begin at 5th lines in output and filter all lines begin with character >> and remains
```sudo smartctl -a /dev/sdb | awk 'NR>4' | sed -e '/^>>/,$d'```

# Using jq command to convert the output into JSON
jq -Rs 'split("\n") | map(split(":") | map(gsub(" "; "")) | select(length > 1) | {(.[0]): .[1]}) | add'; 
