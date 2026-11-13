# Laboratorio de Ansible con Docker

**Estudiante:** Oskarling Moises Mendez Mercedes  
**Matrícula:** 2025-1058

## Objetivo

Crear un laboratorio con dos servidores Linux utilizando Docker y administrarlos con Ansible. En esta práctica se comprueba la conexión con los contenedores, se identifica su sistema operativo y se automatiza la creación de archivos y carpetas mediante un playbook.

## Archivos

- `docker-compose.yml`: define los dos servidores Ubuntu 24.04 (`server1` y `server2`).
- `inventory.ini`: indica a Ansible los servidores que va a administrar.
- `playbook.yml`: contiene las tareas que se ejecutan en ambos servidores.
- `README.md`: documenta los pasos realizados y los resultados de la práctica.
- `captura-ejecucion.png`: muestra la ejecución exitosa del playbook.

## 1. Iniciar los contenedores

Primero, abrí PowerShell en la carpeta del proyecto y ejecuté los siguientes comandos para iniciar los dos servidores y comprobar que estuvieran funcionando.

```bash
docker compose up -d
docker ps
```

Los contenedores `server1` y `server2` se iniciaron correctamente utilizando Ubuntu 24.04.

## 2. Instalar Python en ambos servidores

Luego instalé Python 3 en cada contenedor, ya que Ansible lo necesita para ejecutar sus módulos.

```bash
docker exec server1 bash -c "apt-get update && apt-get install -y python3"
docker exec server2 bash -c "apt-get update && apt-get install -y python3"
```

La instalación se completó correctamente en ambos servidores.

## 3. Comprobar la conexión y ejecutar el playbook

Para administrar los contenedores con Ansible, se utiliza el inventario `inventory.ini`.

Desde una terminal Linux que tenga Ansible, Docker CLI y la colección `community.docker` instalados, los comandos son:

```bash
ansible-galaxy collection install community.docker
ansible docker -i inventory.ini -m ping
ansible-playbook -i inventory.ini playbook.yml
```

En mi caso, realicé la práctica desde Windows utilizando Docker Desktop y PowerShell. Para ejecutar Ansible sin instalarlo directamente en Windows, utilicé un contenedor temporal con Alpine Linux.

```powershell
docker run --rm -v "${PWD}:/work" -v /var/run/docker.sock:/var/run/docker.sock -w /work alpine:3.21 sh -c "apk add --no-cache ansible docker-cli && ansible-galaxy collection install community.docker && ansible docker -i inventory.ini -m ping && ansible-playbook -i inventory.ini playbook.yml"
```

La conexión se comprobó correctamente y ambos servidores respondieron con `SUCCESS` y `ping: pong`.

El inventario utiliza la conexión `community.docker.docker`, que permite a Ansible administrar los contenedores directamente mediante Docker.

## 4. Comprobar los resultados

Al ejecutar el playbook, se realizaron las siguientes tareas en ambos servidores:

- Mostrar el nombre del servidor.
- Identificar el sistema operativo Ubuntu 24.04.
- Crear el directorio `/tmp/ansible-demo`.
- Crear el archivo `info.txt` con el nombre del servidor y su sistema operativo.
- Crear los directorios `logs`, `backup` y `config` utilizando un loop.
- Mostrar un mensaje de confirmación cuando el sistema operativo es Ubuntu.

Para comprobar los archivos creados, se pueden utilizar estos comandos:

```bash
docker exec server1 cat /tmp/ansible-demo/info.txt
docker exec server2 cat /tmp/ansible-demo/info.txt
docker exec server1 ls -la /tmp/ansible-demo
```

### Resultado de la ejecución

El playbook se ejecutó correctamente en los dos servidores. El resumen final fue:

```text
PLAY RECAP ************************************************************
server1 : ok=7 changed=3 unreachable=0 failed=0 skipped=0
server2 : ok=7 changed=3 unreachable=0 failed=0 skipped=0
```

Los resultados muestran que las tareas se completaron sin errores y que ninguno de los servidores presentó problemas de conexión.

## 5. Captura de ejecución

A continuación, se muestra la captura de pantalla tomada desde PowerShell después de ejecutar el playbook de Ansible.

![Ejecución exitosa del playbook](captura-ejecucion.png)

## Conclusión

Con esta práctica pude crear dos servidores Ubuntu utilizando Docker y administrarlos mediante Ansible. También comprobé cómo utilizar un inventario y un playbook para ejecutar varias tareas automáticamente en diferentes servidores.

La ejecución fue exitosa en ambos contenedores, sin errores de conexión ni tareas fallidas. Esto me permitió comprender mejor cómo Ansible facilita la administración y automatización de servidores Linux.
