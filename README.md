# Opsæt og konfigurer en webserver

Når du har oprettet en Droplet på DigitalOcean skal du tilgå Droplet Console.

## Node.js

Følg de tre trin i dokumentationen for [NodeSource](https://github.com/nodesource/distributions#nodejs) for at installere Node.js:

1) Download og importer Nodesource GPG nøgle

```
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```

2) Opret et deb repository

Skift Node.js udgave til 16 eller 18 i stedet for 20 hvis nødvendigt.

```
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```

3) Lav opdatering og kør installation

```
sudo apt-get update
sudo apt-get install nodejs -y
```

## Nginx

Nginx er en del af Ubuntus pakker.

```
sudo apt update 
sudo apt install nginx
```

```
sudo nginx -v
systemctl status nginx
```

Opsætning af firewall.

```
sudo ufw app list
sudo ufw allow 'Nginx HTTP’
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status
```

SSH Fra Command Line Interface og skriv password.

```
ssh root@ip-adresse
```

Forskellige kommandoer for Nginx.

```
sudo systemctl stop nginx
sudo systemctl start nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
sudo systemctl disable nginx
sudo systemctl enable nginx
```

Konfiguration af Nginx.

For at åbne konfigurationen:

```
sudo nano /etc/nginx/sites-available/default
```

Find delen for server { } og location / { }

Her kan vi pege vores endpoint / til en localhost proces som kører på port 3000.

```
server { 
... 
	location / { 
		proxy_pass http://localhost:3000; 
		proxy_http_version 1.1; 
		proxy_set_header Upgrade $http_upgrade; 
		proxy_set_header Connection 'upgrade'; 
		proxy_set_header Host $host; 
		proxy_cache_bypass $http_upgrade; 
	} 
... 
}
```

Tryk CTRL+X for at afslutte og tryk Y for ja til at gemme ændringerne.

## GitHub Personal Access Token

For at flytte vores projektkode fra GitHub til vores Droplet skal vi bruge en Personal Access Token hvis vores repository på GitHub er privat. Hvis det er public er det ligemeget.

Gå til 'Settings' og vælg 'Developer Settings' nederst til venstre i menuen.

Vælg Tokens (classic) og 'Generate new token (classic)'.

Her kan du give dit token et navn og give den tilladelse til repo.

Når din token er oprettet skal du kopiere og gemme den, da du ikke vil kunne se den igen på GitHub.

Denne token kan du bruge til 'git clone' og 'git pull' fra GitHub.

```
git clone https://ghp_yjxvPiI8mqEMTmy@github.com/miwnKEA/joe.git
cd joe/src
npm install
node server.js
```

Brug git pull til at hente opdateringer i projektkoden fra GitHub.

```
git pull https://ghp_yjxvPiI8mqEMTmy@github.com/miwnKEA/joe.git
```

Erstat med din egen token som følgende.

```
git clone https://token_her@github.com/brugernavn/reponavn.git

git pull https://token_her@github.com/brugernavn/reponavn.git
```

## PM2

For at undgå at vi skal starte vores applikation med 'node server.js' kan vi installere en process manager.

```
sudo npm install pm2@latest -g
```

Derefter kan vi starte en process. Hold øje med i Terminalen om pm2 giver de samme instruktioner som følgende. Hvis de er anderledes kan det godt virke alligevel. Dette er en kopi fra min Terminal. Følg selv med i Terminalen og se hvad pm2 skriver.

```
pm2 start server.js
pm2 startup systemd
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u root --hp /home/root
pm2 save
sudo systemctl start pm2-root
systemctl status pm2-root
```

Husk at restarte pm2 efter du har hentet opdateringer med 'git pull'.

```
pm2 restart all
```

Her er en liste med forskellige kommandoer for pm2.

```
pm2 list
pm2 start node_file.js
pm2 stop app_name_or_id
pm2 restart app_name_or_id
pm2 delete app_name_or_id
pm2 info app_name
pm2 save
```

## Links til dokumentation, mv.

[DigitalOcean: How To Set Up a Node.js Application for Production on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-20-04)

[DigitalOcean: How To Install Node.js on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04)

[DigitalOcean: How To Install Nginx on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04)

[NGINX: Beginner's Guide](https://nginx.org/en/docs/beginners_guide.html)

[PM2: Quick Start](https://pm2.keymetrics.io/docs/usage/quick-start/)

[npm: PM2](https://www.npmjs.com/package/pm2)
