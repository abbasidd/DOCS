# sending the balances to the account which are use used
> seth send --value 53269984665640564029457580928849087252755 
# for deploying pip-deploy option should be median

# Changing the .source-gofer-wrapped
to this
>#!/usr/bin/env bash
set -eo pipefail
if [[ -n $OMNIA_DEBUG ]]; then set -x; fi

# cd "$(cd "${0%/*/*}" && pwd)/lib"
cd /home/usman/Videos/omnia/lib

. ./log.sh

base=$(echo $1 | cut -d "/" -f 1)
PRICE=$(curl -sk https://goerli.gsu.io/Umbraco/Api/Rates/GSU/?symbol=$base | /usr/bin/jq --raw-output '.price')
_price=`bc <<< "scale=4; $PRICE/10^18"`
if _price=`bc <<< "scale=4; $PRICE/10^18"`
then
	jq -c --arg price "$_price" --arg _base "$base" '{asset:($_base+"/"+"USD"), "median":$price}' <<<"$_price"
else
	error "could not get price" "$(jq -r '.error' <<<"$_price")"
	exit 1
fi




## for checking
> seth --from 0xa1AE7dE8a093eD5C8b28847ae7716279E07aa693 call $MEDIAN "read()(uint)
> seth --from 0xa1AE7dE8a093eD5C8b28847ae7716279E07aa693 call $OSM "src()"
> seth --from 0xa1AE7dE8a093eD5C8b28847ae7716279E07aa693 call $OSM "read()(uint)"
> seth --from 0xa1AE7dE8a093eD5C8b28847ae7716279E07aa693 call 0xF9FBdd95649b33642fa1486518a55b28842F5D48 "peek()(uint)"



### if it is expires or staled:


