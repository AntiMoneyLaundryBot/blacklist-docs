# AMLBot / PureFi blockchain address blacklist

This blacklist is regularly maintained and updated by internal company staff from different sources, including automated scam detection systems. 

Live documentation and playground:

https://blacklist.purefi.io/swagger-ui/index.html#/BlackList

https://blacklist.amlbot.com/swagger-ui/index.html#/BlackList

# Access
API Key is required to access blacklist. Please contact kyc@amlbot.com

# Fields description
  ```
{
  "address": "TFWzDGKox7TLyRfVivFdDHAp5x7BVD3j8N",
  "addressInfo": {
    "network": "TRON",
    "type": "terrorism financing",
    "createdAt": "2024-05-22T10:16:38.658Z",
    "deletedAt": null
  }
}
```

1. ```address``` = blacklist address value
2. ```network``` = blockchain name
3. ```type``` = type of blacklisted address, i.e. "scam", or "malware", or "honeypot".
4. ```createdAt``` = date/time address was reported

# Type values

Possible type values are taken from the following list: 

```
Abuse Reported
ATMs
Banned By Contract
blocking by Court order
carding
child exploitation
Child Sexual Abuse Material
CoinEx Fund Drainer
CoinEx related
Conti Hacking
Conti Leaks Hacking
dark
darknet marketplaces
darknet services
DeFi Exploit
Dharma Hacking
Drainer
enforcement action
EXMO Stolen Coins
Extortion / Ransom
fraudulent exchanges
GainBitcoin Scam
gambling services
Hack
Hacker
Hamas Terrorism
Hydra Nested (Illicit)
illegal services
Illicit Reported
licensed exchanges
licensed p2p exchanges
Liquid Stolen Coins
liquidity pools
Malware
Master Extortion / Ransom
miners
Mining Voucher Scam
mixing services
money laundering
online marketplaces
online wallets
payment processors
Pending Review
phishing
phishing attack
Phishing scam
Platypus finance exploiter
Plus Token Scam
Political Organization
ransom
Robbinhood Extortion / Ransom
Ronin Stolen Coins
Russian Terrorism
sanctions
Scam
scam
seized assets
Stolen Coins
stolen coins
Stolen funds
SUEX Nested (Illicit)
Terrorism
terrorism financing
theft reported
unlicensed exchanges
unlicensed p2p exchanges
US Enforcement
US OFAC Sanctions
User Reported
```
