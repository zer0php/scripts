function _wget() {
    local url="$1"
    local method="$2"
    local additionalHeaders="$3"

    if [ -z "${url}" ]; then
        printf 'Usage: %s URL [METHOD] [ADDITIONAL HEADERS] [e.g.: %s https://postman-echo.com/post POST "FirstKey: FirstValue%sSecondKey: SecondValue"]\r\n' \
            "${FUNCNAME[0]}" "${FUNCNAME[0]}" '\r\n'

        return 1;
    fi

    if [ -z "${method}" ]; then
        method="GET"
    fi

    read proto server path <<<$(echo ${url//// })
    host=${server//:*}
    port=${server//*:}
    requestUri=/${path// //}

    if [ x"${host}" == x"${port}" ]; then
        [[ "$proto" == "http:" ]] && port=80 || port=443
    fi

    requestHeaders=$(printf '%s\r\n' \
      "${method} ${requestUri} HTTP/1.1" \
      "Host: ${host}" \
      "Connection: close" \
      "User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0" \
      "$(echo -en "${additionalHeaders}")" \
      ""
    )

    echo "$requestHeaders"

    if [ "$proto" == "http:" ]; then
        exec 3<>/dev/tcp/${host}/${port}
        echo "${requestHeaders}" >&3
        cat <&3
    else
        echo "${requestHeaders}" | openssl s_client -quiet -connect "${host}:${port}" 2>/dev/null | cat
    fi
    echo
}
