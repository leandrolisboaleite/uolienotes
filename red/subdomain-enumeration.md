# Subdomain Enumeration

dnsrecon -t brt -d dominio.com



site:www.exemplo.com site:\*.exemplo.com



[https://crt.sh](http://crt.sh/) and [https://ui.ctsearch.entrust.com/ui/ctsearchui](https://ui.ctsearch.entrust.com/ui/ctsearchui)



{% embed url="https://github.com/aboul3la/Sublist3r" %}

./sublist3r.py -d acmeitsupport.thm



ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.exemplo.com" -u http://IP

Because the above command will always produce a valid result, we need to filter the output. We can do this by using the page size result with the -fs switch. Edit the below command replacing {size} with the most occurring size value from the previous result.

ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://MACHINE\_IP -fs {size}



