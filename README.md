# AML Bot / PureFi blockchain address blacklist

This blacklist is regularly maintained and updated by internal company staff from different sources, including automated scam detection systems. 

Live documentation and playground:

https://blacklist.purefi.io/swagger-ui/index.html#/BlackList

https://blacklist.amlbot.com/swagger-ui/index.html#/BlackList

# Access
API Key is required to access blacklist. Please contact sales@amlbot.com

# Fields description
  ```
{
  "address": "0x675c65C60CF0E4fE3443c7daA76aa94cFd59D443",
  "addressInfo": {
    "network": "ETHER",
    "type": "UNKNOWN",
    "createdAt": "2024-05-20 10:46:10",
    }
  }
```

1. ```address``` = blacklist address value
2. ```network``` = blockchain name
3. ```type``` = type of blacklisted address, i.e. "scam", or "malware", or "honeypot".
4. ```createdAt``` = date/time address was reported
