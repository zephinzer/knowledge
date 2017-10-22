# Domain Name System

## A record

Address record. Points to an IPv4 address.

Examples: `{"0.0.0.0", "41.2.0.1", "213.34.11.7", "255.255.255.255"}`

## AAAA record

Address record. Points to an IPv6 address.

Examples: `{"2001:0db8:85a3:0000:0000:8a2e:0370:7334"}`

## CNAME record

Canonical NAME record. Points to a server via hostname.

Examples: `{"username.hostname.com", "cnamehost.user12388312.acme.com"}`

## MX record

Mail eXchange record. Has a priority for fallbacks.

Examples: `{"mx1.zoho.com", "mx2.zoho.com"}`

## TXT record

TeXT records commonly used for domain vertification.

Examples: `{"foo", "bar"}`