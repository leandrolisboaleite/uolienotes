# Subdomain Enumeration

dnsrecon -t brt -d dominio.com



site:www.exemplo.com site:\*.exemplo.com



[https://crt.sh](http://crt.sh/) and [https://ui.ctsearch.entrust.com/ui/ctsearchui](https://ui.ctsearch.entrust.com/ui/ctsearchui)



{% embed url="https://github.com/aboul3la/Sublist3r" %}

./sublist3r.py -d acmeitsupport.thm



ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.exemplo.com" -u http://IP

Because the above command will always produce a valid result, we need to filter the output. We can do this by using the page size result with the -fs switch. Edit the below command replacing {size} with the most occurring size value from the previous result.

ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://MACHINE\_IP -fs {size}



## Trocando user agent

curl -v -H "User-Agent: Mozilla" site.com





#### Script recon longatto



```
#!/bin/bash
for palavra in $(cat lista2.txt)
do
resposta=$(curl -s -H "User-Agent: DesecTool" -o  /dev/null -w "%{http_code}" $1/$palavra/)
if [resposta == "200"]
then
echo "Diretorio encontrado: $1/$palavra"
fi
done

```



<pre class="language-sh"><code class="lang-sh"><strong>#!/bin/bash
</strong>
echo "hehe xD"
cat &#x3C;&#x3C; "EOF"
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣤⡶⠿⠿⠷⣶⣄⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣰⡿⠁⠀⠀⢀⣀⡀⠙⣷⡀⠀⠀⠀
⠀⠀⠀⡀⠀⠀⠀⠀⠀⢠⣿⠁⠀⠀⠀⠘⠿⠃⠀⢸⣿⣿⣿⣿
⠀⣠⡿⠛⢷⣦⡀⠀⠀⠈⣿⡄⠀⠀⠀⠀⠀⠀⠀⣸⣿⣿⣿⠟
⢰⡿⠁⠀⠀⠙⢿⣦⣤⣤⣼⣿⣄⠀⠀⠀⠀⠀⢴⡟⠛⠋⠁⠀
⣿⠇⠀⠀⠀⠀⠀⠉⠉⠉⠉⠉⠁⠀⠀⠀⠀⠀⠈⣿⡀⠀⠀⠀
⣿⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢹⡇⠀⠀⠀
⣿⡆⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣼⡇⠀⠀⠀
⠸⣷⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢠⡿⠀⠀⠀⠀
⠀⠹⣷⣤⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣀⣰⡿⠁⠀⠀⠀⠀
⠀⠀⠀⠉⠙⠛⠿⠶⣶⣶⣶⣶⣶⠶⠿⠟⠛⠉⠀⠀⠀⠀⠀⠀
EOF
sleep 2
echo "Iniciando script!"
sleep 1


URL=""
USER_AGENT=""
LIST=""
EXTENSIONS=""
declare -a FOUND_DIRS
declare -a FOUND_FILES
declare -a DIR_LISTINGS

usage() {
    echo "Usage: $0 -u &#x3C;User-Agent> -w &#x3C;wordlist> -e &#x3C;extensions> &#x3C;URL>"
    echo "Extensoes tem que estar separadas por virgula sem espaco. Exemplo: php,html,js"
    exit 1
}


while getopts "u:w:e:" opt; do
    case "${opt}" in
        u)
            USER_AGENT=${OPTARG}
            ;;
        w)
            LIST=${OPTARG}
            ;;
        e)
            EXTENSIONS=${OPTARG}
            ;;
        *)
            usage
            ;;
    esac
done


shift $((OPTIND-1))


if [ -z "${USER_AGENT}" ] || [ -z "${LIST}" ] || [ -z "${EXTENSIONS}" ] || [ -z "$1" ]; then
    usage
fi


URL=$1


if [ ! -f "${LIST}" ]; then
    echo "List file does not exist: ${LIST}"
    exit 1
fi


IFS=',' read -r -a ext_array &#x3C;&#x3C;&#x3C; "${EXTENSIONS}"


is_directory_listing() {
    local url=$1
  
    content=$(curl -s -H "User-Agent: ${USER_AGENT}" --connect-timeout 10 "${url}")
    
    if [[ "${content}" =~ (Index\ of|Parent\ Directory) ]]; then
        return 0 
    else
        return 1 
    fi
}


while IFS= read -r word; do
    dir_response=$(curl -s -o /dev/null -w "%{http_code}" -H "User-Agent: ${USER_AGENT}" --connect-timeout 10 "${URL}/${word}/")

    
    if [[ "$dir_response" == "200" ]]; then
        # Check if it's not a directory listing page
        if ! is_directory_listing "${URL}/${word}/"; then
            FOUND_DIRS+=("${URL}/${word}/")
          
            for ext in "${ext_array[@]}"; do
                file_response=$(curl -s -o /dev/null -w "%{http_code}" -H "User-Agent: ${USER_AGENT}" --connect-timeout 10 "${URL}/${word}/index.${ext}")

                if [[ "$file_response" == "200" ]]; then
                    FOUND_FILES+=("${URL}/${word}/index.${ext}")
                fi
            done
        else
            DIR_LISTINGS+=("${URL}/${word}/")
        fi
    fi
done &#x3C; "${LIST}"


echo "Scan Report for ${URL}"
echo "======================"
if [ ${#FOUND_DIRS[@]} -ne 0 ]; then
    echo "Found Directories (not listing pages):"
    printf " - %s\n" "${FOUND_DIRS[@]}"
else
    echo "No directories found."
fi

if [ ${#FOUND_FILES[@]} -ne 0 ]; then
    echo "Found Files:"
    printf " - %s\n" "${FOUND_FILES[@]}"
else
    echo "No files found."
fi

if [ ${#DIR_LISTINGS[@]} -ne 0 ]; then
    echo "Skipped Directory Listings:"
    printf " - %s\n" "${DIR_LISTINGS[@]}"
else
    echo "No directory listings found."
fi

```
</code></pre>
