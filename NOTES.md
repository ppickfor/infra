# Additional notes to get things to work

```
git clone
```
create DO account
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
## Install tinc, cfssl, packer
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
create  config/env domain in DO console
delegate subdomain in main domain registrar (dyn in my case to DO)
## Find image id

```
curl -X GET --silent "https://api.digitalocean.com/v2/images?per_page=999" -H "Authorization: Bearer $(<~/.do-token)" |jq '.'|less
```
## Build infrastructure
image id from above (look for id of snapshot listed in DO console)
```
cd ../tf
cp ~/.ssh/id_rsa.pub id_rsa.pub
./terraform apply -var cluster_state=new -var image=$(curl -X GET --silent "https://api.digitalocean.com/v2/images?per_page=999" -H "Authorization: Bearer $(<~/.do-token)" |jq '.images[] | select(.name|test("default-master-")).id')


```
## Do stuff

ssh
```
cssh -l root $(curl -X GET -H "Content-Type: application/json" -H "Authorization: Bearer $(< ~/.do-token) " "https://api.digitalocean.com/v2/droplets"|jq -r '.droplets[].networks.v4[] | select( .type == "public").ip_address')
```
tinc fails to bring up vpn overlay network

## Destroy infrastructure
```
/terraform destroy -var cluster_state=new -var image=$(curl -X GET --silent "https://api.digitalocean.com/v2/images?per_page=999" -H "Authorization: Bearer $(<~/.do-token)" |jq '.images[] | select(.name|test("default-master-")).id')
```
delete the image too in DO console
