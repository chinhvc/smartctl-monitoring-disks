# smartctl-monitoring-disks

## Research about S.M.A.R.T tool to monitor disk status

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
```
# Solution
```bash
for ((i=1; i<=n; i++)); 
do 
	sudo smartctl -a /dev/sdb | awk 'NR>4' | sed -e '/^>>/,$d'| jq -Rs 'split("\n") | map(split(":") | map(gsub(" "; "")) | select(length > 1) | {(.[0]): .[1]}) | add'; 
done
```

# Explaination

```bash
sudo smartctl -a /dev/sdb | awk 'NR>4' | sed -e '/^>>/,$d':
```
- `sudo smartctl -a /dev/sdb`: This command uses smartctl to retrieve information about the specified device `/dev/sdb`.
- `awk 'NR>4'`: Filters the output from smartctl, displaying only lines starting from line number 5 and onwards (NR>4 means "number of records greater than 4").
- `sed -e '/^>>/,$d'`: Uses sed to remove all lines from the line that starts with >> until the end of the output. It discards any error-related messages captured by smartctl.

```bash
jq -Rs 'split("\n") | map(split(":") | map(gsub(" "; "")) | select(length > 1) | {(.[0]): .[1]}) | add':
```

- `jq -Rs`: Invokes jq in raw input mode (-R) to read input as a single string (-s), rather than a stream of JSON objects.

- `'split("\n") | map(split(":") | map(gsub(" "; "")) | select(length > 1) | {(.[0]): .[1]}) | add'`: This is a series of jq filters to manipulate and process the string input. Breaking it down:
  - `split("\n")`: Splits the input string into an array based on newline characters.
  - `map(split(":") | map(gsub(" "; "")) | select(length > 1) | {(.[0]): .[1]})`: For each line (now an array element):
  - `split(":")`: Splits each line into an array using the colon : as a separator.
  - map(gsub(" "; "")): Removes any spaces from the resulting array elements.
  - `select(length > 1)`: Filters out array elements with a length less than or equal to 1.
  - `{(.[0]): .[1]}`: Constructs objects with key-value pairs, taking the first and second elements of the array as key and value, respectively.
  - `add`: Merges all the resulting objects into a single JSON object.

This whole command sequence takes the output from smartctl, processes it to extract key-value pairs separated by :, removes spaces, filters the valid pairs, and constructs a JSON object with these pairs.

All answers provide by ChatGPT 3.5, I tested on 3 AI ChatGPT/Copilot/Bard (all is free edtion)