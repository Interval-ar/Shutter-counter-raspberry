# Shutter-count(reflex)

## Testeado en:

- Raspberry Pi Model B Plus Rev 1.2
- Raspbian GNU/Linux 11 (bullseye)

## Step by step:

<details>
<summary>Uso rapido con raspberry ya configurada</summary>
 
- Conectamos la raspberry a un USB de la pc para darle energia
- Conectamos el cable de red directamente desde la raspberry a la pc
- Conectamos la camara por usb a la raspberry
- Encendemos la camara
- ejecutamos el siguiente comando remplazando usuario por el usuario de la raspberry e ip por la ip o hostname de la raspberry, si esta configurada como esta guia, el usuario `pi` y la ip `192.168.0.211`
```console
ssh usuario@ip 'gphoto2 --get-config /main/status/shuttercounter'
```
- Si devuelve un error: `**** Error: No camera found. ****`, podes probar apagar la camara y volverla a prender, darle un segundo para que conecte y ejecutar el comando, aveces las camaras depues de un rato dejan de responder a las peticiones de USB

</details>

<details>
<summary>terminal/consola/cmd/powershell</summary>
 
>Cualquiera sea tu sistema operativo, vas a necesitar una terminal
> <details>
>
> <summary>Windows</summary>
>
> Tocamos la `tecla de windows + R` e ingresamos `CMD` apretamos enter
>
></details>
>
><details>
><summary>MAC OS</summary> 
>
>1. Abrimos la carpeta `Applications/Utilities/` y buscamos la aplicacion `Terminal` 
>2. Abrimos el launchpad (atajo de teclado F4) y escribimos `Terminal` apretamos enter
></details>
><details>
><summary>Linux</summary> 
>
> Hay varias formas de abrir una terminal en linux segun el Desktop Enviroment, en gnome:
>- Abrimos el menu de aplicaciones via `Activities`(arriba a la derecha) y buscamos la aplicacion `Terminal` 
>
></details>





</details>


<details>
<summary>Conexion a la raspberry</summary>
 
### Conexion via SSH

Para conectar via SSH vamos a necesitar el nombre de usuario de la raspberry, el usuario por defecto es `pi` pero puede ser cambiado en la instalacion, tambien vamos a necesitar la direccion de la raspberry en la red y tenemos dos opciones para esto:
1. __Via ip__: Si todavia no se configuro una ip estatica, vamos a necesitar un programa como `angry ip` y vamos a chequear por un dispositivo con el puerto `22` abierto(en `angry ip` podes configurar la busqueda de puertos especificos en `preferencias->puertos->seleccion de puertos` agregamos `,22` a la lista.)
2. __via nombre de dominio__: el nombre de dominio por defecto es "raspberry" pero se puede cambiar en la instalacion, vamos a usar `nombrededominio.local` (por defecto seria `raspberry.local`)

>estos serian los comandos a ejecutar si los valores estan por defecto
>| | via nombre de dominio  | via ip |
>|---| ------------- | ------------- |
>|comando|` ssh pi@raspberry.local  `|` ssh pi@192.168.0.211  `|
>
>Nos va a pedir la contraseña del usuario de la raspberry
>una vez la conexion este establecida vamos a tener esta linea a la izquierda del input de la consola `pi@raspberrypi:~ $ `
</details>

<details>
<summary>Actualizacion del sistema</summary>
 
Para instalar las aplicaciones necesarias sin romper el sistema vamos a necesitar actualizar el sistema
>Actualizamos el sistema
>
>```console
>sudo apt update && sudo apt upgrade -y
>```

</details>

<details>

<summary> Configuracion del sistema (IP y Asignaciones de IP)</summary>


## ⚠️ Para que estos cambios tomen efecto se debe reiniciar el sistema `sudo reboot`

### Asignar ip a pc conectada directamente

> Instalamos dnsmasq (encargado de asignar ip a las pc’s conectadas a la raspberry, asi evitamos asignar una ip estatica a la pc con la que necesitamos chequear).
> 
> 
> ```console
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
> 
>Al final del archivo necesitamos esta configuracion, se encarga de general un fallback a ip estatica cuando la raspberry esta conectada directamente a una pc (sin una red).
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
>Aunque no es recomendable conectar la raspberry configurada como servidor DHCP a una red con otro servidor DHCP(router), es una buena solucion por si necesitamos actualizar la raspberry
>

</details>

<details>

 <summary>Instalacion de gphoto2 (comuncacion con camara)</summary> 
  
>
>- Instalamos gphoto2
>
>```console
>sudo apt install gphoto2
>```
>
>- Chequeamos que gphoto2 este instalado correctamente
>
>```console
>gphoto2 --auto-detect
>```
>
>![Respuesta de `gphoto2 --auto-detect`  si hay una camara conectada aparece el modelo despues de la linea de guiones](./images/shuttercount.png)
>
>Respuesta de `gphoto2 --auto-detect`  si hay una camara conectada aparece el modelo despues de la linea de guiones

---
</details><details>

<summary>Chequear numeros de disparos</summary> 

>
>```console
>gphoto2 --get-config /main/status/shuttercounter
>```
>
>Podemos tener dos tipos de salidas:
>
>la camara no fue encontrada
>
>```yaml
>**** Error: No camera found. ****
>```
>
>El valor de “CURRENT” es el numero de disparos
>
>``` yaml
>Label: Shutter Counter
>Readonly: 0
>Type: TEXT
>CURRENT: 20245
>END
>```
>

</details>
