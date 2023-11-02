# Deploy and configure

derived from [Getting Started with the Elastic Stack and Docker Compose: Part 2](https://www.elastic.co/blog/getting-started-with-the-elastic-stack-and-docker-compose-part-2)

## Docker Compose up

```bash
docker compose up -d
```

Go to `https://localhost:5601`

Remember, since we have certificates in place now for Kibana, we’ll have to use HTTPS, so you may have to click any certificate warnings your browser gives you.

## Reconfigure output, add certificate

After navigating to Fleet -> Settings, we’ll want to look at the Outputs section and click on the Edit button under the Actions header.

### Hosts

`https://es01:9200/`, This is because the container that hosts the Fleet server understands how to communicate with the es01 container to send data.

### Elasticsearch CA trusted fingerprint (optional):

```bash
# copy ca.crt to /tmp/.
docker cp es-cluster-es01-1:/usr/share/elasticsearch/config/certs/ca/ca.crt /tmp/.
# get the fingerprint of the certificate
openssl x509 -fingerprint -sha256 -noout -in /tmp/ca.crt | awk -F"=" {' print $2 '} | sed s/://g
```

This will produce a value similar to: 

`5A7464CEABC54FA60CAD3BDF16395E69243B827898F5CCC93E5A38B8F78D5E72`

### Advanced YAML configuration:

```bash
cat /tmp/ca.crt
```

This will produce a value similar to: 

```
-----BEGIN CERTIFICATE-----
MIIDSjCCAjKgAwIBAgIVAM8dS+ByvrperJWJTD6LeCzQK2LcMA0GCSqGSIb3DQEB
CwUAMDQxMjAwBgNVBAMTKUVsYXN0aWMgQ2VydGlmaWNhdGUgVG9vbCBBdXRvZ2Vu
ZXJhdGVkIENBMB4XDTIzMTEwMTEyNTQxNloXDTI2MTAzMTEyNTQxNlowNDEyMDAG
A1UEAxMpRWxhc3RpYyBDZXJ0aWZpY2F0ZSBUb29sIEF1dG9nZW5lcmF0ZWQgQ0Ew
ggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDcBHBBWd4njYSKHuExwziO
qxsoFYN+QuZRwr6A7kM3oC99oLUjojVGdmXKzHc1p2pGZM6yKauxgvo/FEXd0aLr
sHLjhK2LU4Sayts36hTYzTk4Bgfrxi4+oFxjte44qTYKUmOZSs0PwKWEdGxE3xBP
TDCW8OOzZpylpTTVmOfkp9cRZ6kjLXRmPJi1H4KakDIpgWCtoQrWoOGm9BPl7eDg
4tYkZJRvbikH3WUqCY+vB+Hq4DsRBkJAMKA0F2Ir8SDhvzzRjAOiNQ/GNDH8zOnK
4byh25Ojh5NJfRy9BdQN/+4qYeg3gXj2lw4u4a5JsToCwrTgqGFcPLjh7xNGRP5T
AgMBAAGjUzBRMB0GA1UdDgQWBBTkI58Llv3kmzredREDdAYX/z+BcDAfBgNVHSME
GDAWgBTkI58Llv3kmzredREDdAYX/z+BcDAPBgNVHRMBAf8EBTADAQH/MA0GCSqG
SIb3DQEBCwUAA4IBAQA9Z50aeIl2F2OyAqXe2SVnx8c0zAJkHGfp+PIKUDNL/rld
NtYhGyGS0Y1+VyKx3E+/C7POKYjhC2Xkb6Mnjpx+vYnYoIkyObopyE1/kr2XWOOn
RrlPGGaRAFrUCZawLgoCcdOAz9XGIVk6RZgdj7tz44O8wLWYzvOaaMK7eSubl1Xg
6dFw1+FiXPt6Hsd7lAo3TB5fSUYbglRFQtNFm9Q3brjmans1aa9ZAGuYYVShb1GJ
XGAmvhrDFLpf5PC/TdA9gqLsG2Z3/Tk1y0miosWB4ESj4bikehYh2HPY5iQfB5+6
1mOeE7p7BzEQLWfvGxl8PN/Lhp0ILhviBCBcASzF
-----END CERTIFICATE-----
```

then change it to yaml format, making sure the indentation is correct.

```
ssl:
  certificate_authorities:
  - |
    -----BEGIN CERTIFICATE-----
    MIIDSjCCAjKgAwIBAgIVAKjAMOQXo3KfQJAT6ztFXbHu9cPGMA0GCSqGSIb3DQEB
    CwUAMDQxMjAwBgNVBAMTKUVsYXN0aWMgQ2VydGlmaWNhdGUgVG9vbCBBdXRvZ2Vu
    ZXJhdGVkIENBMB4XDTIzMTEwMTEzMDIzNloXDTI2MTAzMTEzMDIzNlowNDEyMDAG
    A1UEAxMpRWxhc3RpYyBDZXJ0aWZpY2F0ZSBUb29sIEF1dG9nZW5lcmF0ZWQgQ0Ew
    ggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCk/X8Hm6EVEesxjBhpSt2s
    Hnj6i+ChIvZImo5lUE3DNz9YX6bPOTO0ofr8Iup+IL4M4C7uFbx/dcjZ37MJxY9M
    21nh0u3yV+3lWXbGTOu4ScSOK/OQaY1tLYMl51n7yLBxVDyRSH71w/8YVoWpnjvk
    J2AB3E3gOTpSvONeIJpSriWFx3mdUkPWPYXURhi8So/8MQS9rqJcYWRihFropZG/
    sTMAMfoQLHllv6IDdwXzlA9TB5L+u0dlzeWsp6IE2J1wBi1z0vHPWVHofUI8O58x
    dxj15mUW4QNAMWFz3PP1eHMCUnm5r+KOM87ag4iGHyJ4Cc/gNz2MzoGDjKavPx8J
    AgMBAAGjUzBRMB0GA1UdDgQWBBS+cl4lDjC9PVHFLYS2h8bDznR1nDAfBgNVHSME
    GDAWgBS+cl4lDjC9PVHFLYS2h8bDznR1nDAPBgNVHRMBAf8EBTADAQH/MA0GCSqG
    SIb3DQEBCwUAA4IBAQAdA5NOQyjoP0vFl59HV3f25rUXzb3Go+0Yu7tcsv24xs7D
    KqSDcfxVdSnrbax8/iXboFqPP8Sizjs1bL9scUOu7u59Z6IrkEoyM8Lngcblnt2Y
    0BkTXAPMBODtIfLkvyUdEOsKKPJWA96rzy+w5QloT5CK5UTIzO2O9Tmn7Zo0ivvb
    bSm/SLPDTrbB5DvMeOQZZdIKGKDihfOfKA/6eadEv+TNxV5AUKBlFBDYGUD1FQny
    s33zpsAmw+0KnTWiIRkJ1O/7sNs1R9hS3XcnwUW/t4BaYgsbPz0g5WOSjG4FHcvy
    kBTnKuW58eBGBOhHHHwTsI1Q+luFaADZ/IPtIgpi
    -----END CERTIFICATE-----
```

Once the Save and Deploy is complete, head back to the Agent tab, click on your agent name, and you should see CPU and Memory utilization properly displayed, as well as Logs being populated.
