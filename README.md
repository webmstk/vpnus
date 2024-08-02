## About
Ansible script for installing wireguard on ubuntu

## Prerequisites
- Ansible https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html (macos: brew install ansible)
- SSH
- Remote server with public ip
- Wireguard client for you device https://www.wireguard.com/install/
- Beer

## Usage

### Step 1. Create remote user.
Log to remote server and create user:
```
adduser <user>
usermod -aG sudo <user>
```
Copy your local ~/.ssh/id_rsa.pub to remote ~/.ssh/known_hosts

### Step 2. Configure ansible.
```
git clone <this repo>
cp inventory.yml.example inventory.yml
```
Modify content of inventory.yml:
- Set ansible_host to the public ip of the remote server.
- Set remote_user to the remote server user (created in Step 1).

### Step 3. Install wireguard on the remote server.
Check vars.yml

Install and set up wireguard:
```
ansible-playbook -i inventory.yml wirequard.yml -e @vars.yml -K
```

### Step 4. Generate peer config for client (and feed it to the wireguard server).

Add peer per each device:
```
# modify add_peer.yml#vars, example:
peer_name: phone
peer_address: 10.8.0.2

# then
ansible-playbook -i inventory.yml add_peer.yml -e @vars.yml -K
```

### Step 5. Get peer config.
ssh to remote server, and cd to peers configs folder. Example for `peer_name: laptop`:
```
cd ~/wg/laptop

# print to console
cat laptop.conf

# or show qr code
qrencode -t ansiutf8 -l L <laptop.conf
```

## Connection status
ssh to remote server
```
sudo wg show
```

## Troubleshooting
Restart wireguard client if vpn does not work for some reason.

## Extra

### Remove peer
```
# modify remove_peer.yml#vars, example:
peer_name: phone

# then
ansible-playbook -i inventory.yml remove_peer.yml -K
```

### PresharedKey
Comment PRESHARED KEY sections in `add_peer.yml` if you don't need cryptographic protection.

## More
https://www.digitalocean.com/community/tutorials/how-to-set-up-wireguard-on-ubuntu-20-04
https://www.digitalocean.com/community/tutorials/how-to-set-up-wireguard-on-ubuntu-22-04

Bash script (not mine):
```
curl -O https://raw.githubusercontent.com/angristan/wireguard-install/master/wireguard-install.sh
chmod +x wireguard-install.sh
./wireguard-install.sh
```
