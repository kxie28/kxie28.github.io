Basic IP Block Script
=====================

Put together a very basic IP block script. It accepts a URL and curls the contents and grabs the IPs listed: ::

        x=1
        while [ $x -le 1 ]
        do
                echo "Enter URL: "
                read urlvar
                regex='(https?|ftp|file)://[-A-Za-z0-9\+&@#/%?=~_|!:,.;]*[-A-Za-z0-9\+&@#/%=~_|]'
        if [[ $urlvar =~ $regex ]]
        then
                curl $urlvar >> scriptip.lst
                ((x++))
        else
                echo "Link not valid"
        fi
        done
        
        grep -Ev '^#|^$' /home/kevin.xie/scriptip.lst |
        awk -v url="$urlvar" '{
        regex = /[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}/
        for (x=1;x<=NF;x++)
        where = match($0,regex)
        #ip = substr($0,RSTART,RLENGTH)
        if (where)
                print $ips,", " url
        }' > scriptip.lst.sort

* Curl (tool to transfer data from or to a server) each URL and grab that data and output it into a file
* Grep -Ev ...
        - E is for extended regex so we can search fro more conditions and v is for inverted search (instead of searching for "x" we search for everything **but** "x"
        - \^# and \^$ means grep is searching for everything but the lines that start with "#" and new lines (\$ = blank lines)
        - The awk command is searching for matching numbers from 0-9 separated by a "." delimiter
        - All the IPs are printed into a new file "ipblock.lst.sort"


