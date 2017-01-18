# Additional notes to get things to work

```
git clone
```
create do account
apply promocode heresthething
create read-write token
```
vi ~/.do-token
chmod og= ~/.do-token
```
## Change the config
REGION=sfo1
DOMAIN=int.netremedies.ca
SERVERS=3
SERVER_SIZE=512mb
IP_INT_PREFIX=10.130
```
vi config/env
```
## Install tnic, cfssl, packer
```
sudo pacman -Syyu tinc
yaourt -S cfssl --noconfirm
yaort -S packer-io --noconfirm
```
## Create the base image
ensure ssh_username is configured in base.json and Makefile
```
cd packer
make
```
## Configure domain name
create  config/env domain in do console
delegate subdomain is main domain registrar (dyn in my case to DO)
# Find image id

```
curl -X GET --silent "https://api.digitalocean.com/v2/images?per_page=999" -H "Authorization: Bearer $(<~/.do-token)" |jq '.'|less
```
# Build infrastructure
image id from above (look for id of snapshot listed in DO console)
```
cd ../tf
cp ~/.ssh/id_rsa.pub id_rsa.pub
./terraform apply -var cluster_state=new -var image=22244571
```