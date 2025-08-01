# AMLBot / PureFi blockchain address blacklist

This blacklist is regularly maintained and updated by internal company staff from different sources, including automated scam detection systems. 

Live documentation and playground:

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

# Examples
```
{
  "address": "0x3bc3eb780be7b08e88e0c2b289edbe60dde3830d",
  "addressInfo": {
    "network": "ethereum",
    "type": "stolen_coins",
    "createdAt": "2024-06-08T10:40:45.517Z",
    "deletedAt": null
  }
}
```

``` 
{
  "address": "bc1qa00swfw3450z3mamf6u0zu2afzspxvnh3zjy8g",
  "addressInfo": {
    "network": "bitcoin",
    "type": "stolen_coins",
    "createdAt": "2024-06-08T10:40:13.913Z",
    "deletedAt": null
  }
}
```

``` 
{
  "address": "cosmos17fm6kk9r98nhvas49p5lzzr5qtkmh5hruwf692",
  "addressInfo": {
    "network": "cosmos",
    "type": "stolen_coins",
    "createdAt": "2024-06-08T10:39:25.049Z",
    "deletedAt": null
  }
}
```

# Type values

Possible type values are taken from the following list: 

| Type                    | Label                     | Description                                                                                   |
|-------------------------|---------------------------|-----------------------------------------------------------------------------------------------|
| child_exploitation      | Child Exploitation        | Entities associated with child exploitation.                                                  |
| dark_market             | Dark Market               | An online marketplace which operates via darknets and is used for trading illegal products for cryptocurrency. |
| dark_service            | Dark Service              | An organization which operates via darknets and offers illegal services for cryptocurrency.    |
| enforcement_action      | Enforcement Action Related| The entity is subject to legal proceedings with the judicial authorities.                     |
| exchange_fraudulent     | Fraudulent Exchange       | Exchanges involved in exit scams, illegal behavior, or whose funds have been confiscated by government authorities. |
| gambling                | Gambling                  | Coins associated with unlicensed online games                                                 |
| illegal_service         | Illegal Service           | Coins associated with illegal activities.                                                     |
| malware                 | Malware                   | Malware                                                                                       |
| mixer                   | Mixing Service            | Coins that passed via a mixer to make tracking difficult or impossible. Mixers are mainly used for money laundering. |
| ransom                  | Extortion / Ransom        | Coins obtained by extortion or blackmail.                                                     |
| sanctions               | Sanctions                 | Entities subject to sanctions.                                                                |
| scam                    | Scam                      | Coins that were obtained by deception, Phishing address, Address poisoning                    |
| stolen_coins            | Stolen Coins              | Theft, Coins obtained by stealing someone else's cryptocurrency.                              |
| terrorism_financing     | Terrorism                 | Entities associated with terrorism financing.                                                 |
