# Shutter-count(reflex)

## Testeado en:

- Raspberry Pi Model B Plus Rev 1.2
- Raspbian GNU/Linux 11 (bullseye)

## Step by step:

---

### Configuracion del sistema (IP y Asignaciones de IP)

<aside>
⚠️ Para que estos cambios tomen efecto se debe reiniciar el sistema `sudo reboot`

</aside>

### Asignar ip a pc conectada directamente

> Instalamos dnsmasq (encargado de asignar ip a las pc’s conectadas a la raspberry, asi evitamos asignar una ip estatica a la pc con la que necesitamos chequear).
> 
> 
> ```yaml
> sudo apt install dnsmasq
> ```
> 
> Modificamos el archivo del server DHCP `sudo nano /etc/dnsmasq.conf`  agregamos esto al final del archivo
> 
> ```yaml
> interface=eth0
> bind-dynamic
> domain-needed
> bogus-priv
> dhcp-range=192.168.0.100,192.168.0.200,255.255.255.0,12h
> ```
> 

### IP estatica y fallback:

> Modificamos el archivo de configuracion de ip con el comando
`sudo nano /etc/dhcpcd.conf` 
Al final del archivo necesitamos esta configuracion, se encarga de general un fallback a ip estatica cuando la raspberry esta conectada directamente a una pc (sin una red), aunque no es recomendable conectar la raspberry a una red con un servidor DHCP(router), es una buena solucion por si necesitamos actualizar la raspberry
> 
> 
> ```yaml
> # It is possible to fall back to a static IP if DHCP fails:
> # define static profile
> profile static_eth0
> static ip_address=192.168.0.211/24
> static routers=192.168.0.211
> static domain_name_servers=192.168.1.211
> 
> ```
> 

---

### Instalacion de gphoto2 (comuncacion con camara)

- Actualizamos el sistema

```bash
sudo apt update && sudo apt upgrade -y
```

- Instalamos gphoto2

```bash
sudo apt install gphoto2
```

- Chequeamos que gphoto2 este instalado correctamente

```bash
gphoto2 --auto-detect
```

![Respuesta de `gphoto2 --auto-detect`  si hay una camara conectada aparece el modelo despues de la linea de guiones](./images/shuttercount.jpg)
Respuesta de `gphoto2 --auto-detect`  si hay una camara conectada aparece el modelo despues de la linea de guiones

---

### Chequear numeros de disparos

```bash
gphoto2 --get-config /main/status/shuttercounter
```

Podemos tener dos tipos de salidas:

la camara no fue encontrada

`**** Error: No camera found. ****`

El valor de “CURRENT” es el numero de disparos

`**Label: Shutter Counter
Readonly: 0
Type: TEXT
CURRENT: 20245
END**`
