# Opsæt og konfigurer en webserver

Når du har oprettet en Droplet på DigitalOcean skal du tilgå Droplet Console.

Følg de tre trin i dokumentationen for [NodeSource](https://github.com/nodesource/distributions#nodejs) for at installere Node.js:

1) Download og importer Nodesource GPG nøgle

```
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```

2) Opret et deb repository

Skift Node.js udgave til 16 eller 18 i stedet for 20.

```
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```

3) Lav opdatering og kør installation

```
sudo apt-get update
sudo apt-get install nodejs -y
```





