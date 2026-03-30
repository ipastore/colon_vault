Esta es una guía paso a paso para EL MÁQUINA, EL DIABLO de mi amigo, the one and only PADI. 

## 1) Pedir acceso a la VPN con formulario
https://aesso.unizar.es/

Tardan más de 24 hs en darte acceso

****
## 2) Generar Token OTP

### a) Para esto necesitas bajarte el Google Authenticator a tu movil.

### b.1) Con Anydesk o Rustdesk te conectas a https://cerbero.unizar.es/ (esto es porque necesitas conectarte a esa web con la red de UNIZAR)
### b.2) Apretar en "Inscribir un nuevo token" y seguir screenshot


![[Pasted image 20251028100953.png]]

En la app de Google authenticator the llega un código y lo introducís en Please enter a valid OTP value of the new token. Luego Verify Token.

![[Pasted image 20251028101116.png]]
![[Screenshot 2025-10-28 at 10.14.04.png]]

### b.3) Comprobar token![[Screenshot 2025-10-28 at 10.14.04 1.png]]
correctamente generado

****

## 3) Bajar Fortclient VPN y configurar VPN

### a) Bajar
https://www.fortinet.com/lat/support/product-downloads#vpn

### b) Configurar

![[Screenshot 2025-10-28 at 10.17.32.png]]

### c) Conectar

- Connect
- Introducis tus credenciales con nip y contraseña
- Te piden el token que te aparece en el Google Authenticator
- Ya te debería saltar que estás conectado
- Listo ya tenés la VPN y estas en la red de la unizar, ahora vamos a conectarnos con ssh.

****
## 4) Configurar ssh con alias para conectarte a tu máquina

> [!NOTE]
> Tener en cuenta que esto es para **Linux y Macos** no se como será para **Windows**

### a) Crear directorio .ssh si no existe
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```
Con 700 seteas permisos para solo poder modificarlo vos

### b) Generar key ssh 

```bash
ssh-keygen -t ed25519 -C "your_user@your_host" -f ~/.ssh/id_ed25519
```
-t te da una clave "moderna"
-C es un comentario: por ejemplo: padi@windows
-f path para guardar key

Te pide una passphrase que es como "una segunda contraseña" para acceder a la key. La ingresas y se genera la key con SHA256.

### c) Remember key 

```bash
eval "$(ssh-agent -s)"

ssh-add --apple-use-keychain ~/.ssh/id_ed25519 2>/dev/null || ssh-add ~/.ssh/id_ed25519

```

Podes comprobar con:

```bash
ssh-add -l        # lists loaded keys (fingerprints)
ps aux | grep ssh-agent  # confirms agent process is running
```


### d) Averiguar tus datos de la maquina del laboratorio

```bash
whoami
hostname -I
```

de hostname-I agarras la primera ip

### e) abris config

```bash
vim ~/.ssh/config
```
o nano o code, lo que prefieras


```txt
Host *
  AddKeysToAgent yes
  UseKeychain yes
  ServerAliveInterval 30
  ServerAliveCountMax 3
  ControlMaster auto
  ControlPath ~/.ssh/cm-%r@%h:%p
  ControlPersist 10m
  IdentitiesOnly yes
  StrictHostKeyChecking accept-new
  
  Host REPLACE_WITH_ALIAS
  HostName REPLACE_WITH_IP 
  User REPLACE_WITHUSERNAME
  IdentityFile ~/.ssh/id_ed25519
```

El alias es lo que vas a utilizar para tirar el comand ssh alias, así que elegí uno fácil y corto

### f) Configurar permisos
```bash

chmod 600 ~/.ssh/config
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
```

### g) Revisar si tengo ssh instalado en host, sino instalar

```bash
which sshd
```
sino

```bash
sudo apt update
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
```
Por default escucha al port 22

### h) Agregay key a host

**Opcion 1: Copiar y pegar key desdde tu maquina al host**

- Copy the key from ~/.ssh/id_ed25519.pub. Then

```bash
vim ~/.ssh/authorized_keys  # paste your public key
chmod 600 ~/.ssh/authorized_keys
```


**Opcion 2: con cat desde tu maquina**
```bash
cat ~/.ssh/id_ed25519.pub | ssh -p 22 REPLACE_WITH_ALIAS@REPLACE_WITHIP \
  'umask 077; mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```

### i) Testea conexion

```bash
ssh alias
```

****
### 5) Conectarse por ssh a VsCode

- Instalar extension Remote-SSH
- Ctrl+shift+p: Remote-SSH: Connect to Host
- Elegis tu alias

LISTO PAPAAAA


****
# Otros

- Web guía: https://sicuz.unizar.es/comunicaciones/vpn/conexi%C3%B3n-vpn-inicio
- Proceso de obtención de OTP: https://sicuz.unizar.es/2FA
- PDF guía completo: [[Servicio_VPN_Doble_Factor.pdf]]