# Bash Tips & Tricks

### Trim functions

This seams to be such a basic tool and it would be in any other language. But when it comes to bash scripting there seams to be no such option available. Most people answering questions about this online also seams to not really understand the concept if trim. Some solutions will remove leading and tralling space but not tabulators and such. Other solutions will remove tabs and spaces from the entire string, including in between actual printable characters. Not one single solution deals with things like leading/tralling new line characters. 

Tools like `sed`, `grep` etc. is of no use. They mostly work on a per line basis and as such does not deal with new line characters. One solution that is constantly being brought up is `echo "$string" | xargs` which is absolutly terrible. This will remove ALL whitespaces and not just the ones before and/or after the actual string content. 

The only solution is to go the manual aprough by actually checking each character either starting at the beginning or the end and find a substring position to extract. Doing this we can actually create two very known and usefull functions that will handle a REAL trim operation and remove any type of whitespace and control character, without destroying the string itsef as some of the other online solutions. 

```sh
ltrim() {
    local str="$1" num
    
    for (( i=0; i < ${#str}; i++ )); do
        num="$(echo -e "${str:$i:1}" | od -t d1 | head -n 1 | awk '{print $2}')"

        if [ $num -gt 32 ] && [ ! $num -eq 127 ]; then
            echo "${str:$i:$((${#str}-$i))}"; return
        fi
    done
    
    echo ""
}
```

```sh
rtrim() {
    local str="$1" num
    
    for (( i=${#str}-1; i >= 0; i-- )); do
        num="$(echo -e "${str:$i:1}" | od -t d1 | head -n 1 | awk '{print $2}')"

        if [ $num -gt 32 ] && [ ! $num -eq 127 ]; then
            echo "${str:0:$i+1}"; return
        fi
    done
    
    echo ""
}
```
