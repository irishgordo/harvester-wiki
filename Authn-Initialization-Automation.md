Here's an example to initialize the admin password and access the API of a fresh new Harvester.

```shell
#!/bin/bash

 export HARVESTER_URL='https://172.16.0.1:30443'
 export NEW_PASSWORD=admin123456789 # New password to be set
 export WAIT_TIME=5

 # check jq install

 jq -V >> /dev/null 2>1
 if [ $(echo $?) != "0" ]; then
     echo 'jq is not installed.'
 fi

 # It takes time for Harvester to be ready, check and retry in the loop.
 export i=100

 while (( i > 10 ))
 do
     export CHECK_FIRST_LOGIN=$( curl -k -s  -H Expect:"" ${HARVESTER_URL}/v3/settings/first-login | jq -r .value )

     if [ "${CHECK_FIRST_LOGIN}" = 'true' ] || [ "${CHECK_FIRST_LOGIN}" = 'false' ]; then
         export i=1
     fi
     echo "Harvester is not ready. Wait ${WAIT_TIME} seconds."
     sleep ${WAIT_TIME}
 done

 if [ "${CHECK_FIRST_LOGIN}" = 'true' ]; then

 login_request_body()
 {
 cat <<EOF
 {
     "description": null,
     "password": "admin",
     "responseType": "json",
     "ttl": 3600,
     "username": "admin"
 }
EOF
 }

 # token ttl is set to 3600 seconds so the token will be valid for the next hour.
 API_TOKEN=$( curl -LSs -k -X POST \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/json' \
     -d "$(login_request_body)" \
    "${HARVESTER_URL}/v3-public/localProviders/local?action=login" | jq -r .token )

 # change-password

 change_password_request_body()
 {
 cat <<EOF
 {
     "currentPassword": "admin",
     "newPassword": "${NEW_PASSWORD}"
 }
EOF
 }

 curl -k -X POST \
     -H "Authorization: Bearer ${API_TOKEN}" \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/json' \
     -d "$(change_password_request_body)" \
     "${HARVESTER_URL}/v3/users?action=changepassword"

# And we can call other APIs using the API_TOKEN
curl -k \
     -H "Authorization: Bearer ${API_TOKEN}" \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/json' \
     "${HARVESTER_URL}/apis"

fi
```