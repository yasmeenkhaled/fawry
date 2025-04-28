# fawry
# Q1 : Custom Command (mygrep.sh)

### objectives Q1
- Case-insensitive search
- Line number display
- Invert match option
- Error handling

## script 
``` bash
#!/bin/bash

usage() {
    echo "Usage: $0 [OPTIONS] PATTERN FILE"
    echo "Search for PATTERN in FILE and print matching lines."
    echo
    echo "Options:"
    echo "  -n,  Show line numbers."
    echo "  -v,  Invert the sense of matching."
    echo "  --help  Display this help message and exit."
    exit 1
}

ShowLineNumbers=false
InvertMatch=false

# Process each character in the option
process_option() {
    local opts="$1"
    for ((i=0; i<${#opts}; i++)); do
        case "${opts:$i:1}" in
            n)
                ShowLineNumbers=true
                ;;
            v)
                InvertMatch=true
                ;;
            *)
                echo "Error: Invalid Option: -${opts:$i:1}"
                usage
                ;;
        esac
    done
}

# Modify option parsing
while [[ $# -gt 0 ]]; do
    case "$1" in
        -[nv]*)
            # Handle combined options like -vn, -nv
            process_option "${1#-}"
            shift
            ;;
        -n)
            ShowLineNumbers=true
            shift
            ;;
        -v)
            InvertMatch=true
            shift
            ;;
        --help)
            usage
            ;;
        -*)
            echo "Error: Invalid Option: $1"
            usage
            ;;
        *)
            if [[ -z "$pattern" ]]; then
                pattern="$1"
            else
                file="$1"
            fi
            shift
            ;;
    esac
done

if [[ -z "$pattern" || -z "$file" ]]; then
    echo "Error: Missing PATTERN or FILE."
    usage
fi

if [[ ! -f "$file" ]]; then
    echo "Error: File not found: $file"
    exit 1
fi        

line_number=0
while IFS= read -r line; do
    ((line_number++))
    
    # Use grep with -E for case-insensitive match
    if [[ $InvertMatch == false ]] && echo "$line" | grep -qiE "$pattern"; then
        if [[ $ShowLineNumbers == true ]]; then
            echo "$line_number:$line"
        else
            echo "$line"
        fi
    elif [[ $InvertMatch == true ]] && ! echo "$line" | grep -qiE "$pattern"; then
        if [[ $ShowLineNumbers == true ]]; then
            echo "$line_number:$line"
        else
            echo "$line"
        fi
    fi

done < "$file"

```
     
## Output Examples :

- bash mygrep.sh hello testfile.txt
> Hello world
> 
> HELLO AGAIN

- bash mygrep.sh -n hello testfile.txt
> 1:Hello world
> 
> 4:HELLO AGAIN

- bash mygrep.sh -v hello testfile.txt
> This is a test
> 
> another test line
> 
> Don't match this line
> 
> Testing one two three

- bash mygrep.sh -vn hello testfile.txt
> 2:This is a test
> 
> 3:another test line
> 
> 5:Don't match this line
> 6:Testing one two three


## Make script executable

![image](https://github.com/user-attachments/assets/2ece2c3f-3f94-40fd-9ef0-1d79ead7bbaa)


## Output Screen :

![image](https://github.com/user-attachments/assets/d660d0a5-4a60-4e6c-9368-c6bd36cbf462)





#  Q2 : Internal web dashboard Troubleshooting

# Verify DNS Resolution:

```
# Check local DNS servers
cat /etc/resolv.conf

# Test DNS resolution using local nameservers
nslookup internal.example.com

# Test DNS resolution using Google DNS
nslookup internal.example.com 8.8.8.8

```

# Network Connectivity Diagnostics:

```
# Trace DNS resolution path
tracepath internal.example.com

# Ping to verify basic network connectivity
ping -c 4 internal.example.com

# Check network interfaces
ip addr
ifconfig

# Verify routing table
ip route

```


#  Service Reachability Tests :

 ```
# Test web service connectivity
curl http://internal.example.com
telnet internal.example.com 80
telnet internal.example.com 443

# Check open ports
sudo netstat -tuln | grep -E '80|443'
sudo ss -tuln | grep -E '80|443'


```

# Issue Categories 

# Incorrect DNS Configuration

  ```
# Manually edit DNS servers
sudo nano /etc/resolv.conf

  ```


# DNS Caching Problems

```
# Flush DNS cache
sudo systemd-resolve --flush-caches
sudo resolvectl flush-caches

```


# Firewall Blocking

```
# Check firewall status
sudo ufw status
sudo firewall-cmd --list-all

# Temporarily disable firewall for testing
sudo ufw disable
sudo systemctl stop firewalld
```


# Routing Problems
```
# Check routing table
ip route

# Add static route if needed
sudo ip route add default via <gateway-ip>

```


# Service Not Running

```
# Check service status
systemctl status nginx
systemctl status apache2

# Restart web service
sudo systemctl restart nginx
```



# Misconfigured Virtual Hosts

```
# Check web server configuration
sudo nginx -t
sudo apachectl configtest

```

# Local DNS Override

# Temporary Local Testing
```
# Edit hosts file to bypass DNS
sudo nano /etc/hosts

# Add entry
192.168.1.100 internal.example.com
```



# Persistent DNS Configuration

```
# Using systemd-resolved
sudo systemd-resolve --interface=eth0 --set-dns=8.8.8.8

# Using NetworkManager
nmcli con mod "your-connection" ipv4.dns "8.8.8.8"
nmcli con up "your-connection"
```
