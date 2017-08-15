gandi_live_dns
----

This is a simple dynamic DNS updater for the
[Gandi](https://www.gandi.net) registrar. It uses their REST API to update
the zone file for a subdomain of a domain to point at the external IPv4
address of the computer it has been run from.

It has been developed on Debian 8 Jessie and tested on Debian 9 Sretch GNU/Linux using Python 2.7.

With the new v5 Website, Gandi has also launched a 
new REST API which makes it easier to communicate via bash/curl or python/requests.  

### Goal

You want your homeserver to be always available at `dynamic.mydomain.tld`.

#### API Key
First, you must apply for an API key with Gandi. Visit 
https://account.gandi.net/en/ and apply for (at least) the production API 
key by following their directions.

#### A DNS Record 
Create the DNS A Records in the GANDI Webinterface which you want to update if your IP changes. 

### Debian Package Requirements

`apt-get update && apt-get upgrade && apt-get install unzip python-requests python-args python-simplejson`

#### Git Clone or Download the Script
Download the Script from [GitHub](https://github.com/cavebeat/gandi_live_dns/archive/master.zip) and unzip it.  

or

`git clone https://github.com/cavebeat/gandi_live_dns.git` 

#### Script Configuration
Then you'd need to configure the script in the src directory.
Copy `example.config.py` to `config.py`, and put it in the same directory as the script.

Edit the config file to fit your needs. 

##### api_secret
Start by retrieving your API Key from the "Security" section in new [Gandi Account admin panel](https://account.gandi.net/) to be able to make authenticated requests to the API.
api_secret = '---my_secret_API_KEY----'

##### api_endpoint
Gandiv5 LiveDNS API Location
http://doc.livedns.gandi.net/#api-endpoint
'''
api_endpoint = 'https://dns.beta.gandi.net/api/v5'

##### domain
Your domain with the subdomains to be updated 
domain = 'mydomain.tld'

##### subdomains
All subdomains which should be updated. They get created if they do not yet exist. 
subdomains = ["subdomain1", "subdomain2", "subdomain3"]

The first domain is used to find out the actual IP in the Zone Records. 

#### Run the script
And run the script:
`
root@dyndns:~/gandi_live_dns-master/src# ./gandi_live_dns.py   
Checking dynamic IP:  127.0.0.1
Checking IP from DNS Record subdomain1:  127.0.0.1
IP Address Match - no further action
`

If your IP has changed, it will be detected and the update will be triggered. 


`
root@dyndns:~/gandi_live_dns-master/src# ./gandi_live_dns.py
Checking dynamic IP:  127.0.0.2
Checking IP from DNS Record subdomain1:  127.0.0.1
IP Address Mismatch - going to update the DNS Records for the subdomains with new IP 127.0.0.2
Status Code: 201 , DNS Record Created , IP updated for subdomain1
Status Code: 201 , DNS Record Created , IP updated for subdomain2
Status Code: 201 , DNS Record Created , IP updated for subdomain3
`

#### Command Line Arguments

`
root@dyndns:~/gandi_live_dns-master/src# ./gandi_live_dns.py -h
usage: gandi_live_dns.py [-h] [-f]

optional arguments:
  -h, --help     show this help message and exit
  -f, --force    force an update/create

`

The force option runs the script, even when no IP change has been detected. 
It will update all subdomains and even create them if they are missing in the 
Zone File/Zone UUID. This can be used if additional/new subdomains get appended to the conig file.  


This DynDNS updater is inspired by https://github.com/jasontbradshaw/gandi-dyndns which worked very well 
with the classic DNS from Gandiv4 Website and their XML-RPC API. 