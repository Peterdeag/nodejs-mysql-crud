# nodejs-mysql-crud

Este repositorio contiene la configuración necesaria para ejecutar una aplicación Node.js con base de datos MySQL utilizando Docker y Docker Compose dentro de una máquina virtual Linux. Además, se configura Nginx como reverse proxy para permitir el acceso a la aplicación desde el puerto 80.

El objetivo del proyecto es containerizar la aplicación y la base de datos para que puedan ejecutarse de forma aislada y reproducible en una VM limpia. También se configura Nginx para que funcione como intermediario entre el usuario y la aplicación, evitando exponer directamente el puerto interno de Node.js.

## Tecnologías utilizadas
- Ubuntu Server / Ubuntu Desktop  
- Docker
- Docker Compose
- Node.js
- MySQL
- Nginx


### Implementacion en Docker desde Linux

## 1. Clonar el repositorio desde github

```bash
git clone https://github.com/Peterdeag/nodejs-mysql-crud
```

Entrar a la carpeta del proyecto:

```bash
cd nodejs-mysql-crud
```

## 2. Instalar Docker

Actualizar paquetes del sistema e Instalar Docker:

```bash
sudo apt update
sudo apt install docker.io -y
```

Instalar Docker Compose:

```bash
sudo apt install docker-compose-v2 -y
```

Verificar instalaciones:

```bash
docker --version
docker compose version
```

## 3. Configurar variables de entorno

Crear un archivo `.env` en la raíz del proyecto:

```bash
nano .env
```

Agregar las variables necesarias:

```
DB_HOST=paystand-mysql
DB_USER=root
DB_PASSWORD=******
DB_NAME=nodejs2

MYSQL_ROOT_PASSWORD=******
MYSQL_DATABASE=nodejs2
```

## 4 Configurar Inicializacion de base de datos
El servicio oficial de MySql en docker, utiliza por defecto el volumen /docker-entrypoint-initdb.d como predetermina para inicializar las bases de datos
Los archivos .sql en este direcctorio se ejecutan automaticamente al inicializar
En el proyecto se guardan estos archivos en la carpeta /database
En la configuracion de compose se  agrega la carpeta /database al volumen de inicializacion de MySql

volumes:
  - ./database:/docker-entrypoint-initdb.d

La configuracion de la base de datos se puede hacer desde el archivo /database/db.sql
Para el proyecto se crea la tabla customer


## 5. Instalar y configurar Nginx como Reverse Proxy

El objetivo de Nginx es actuar como intermediario entre el usuario y la aplicación Node.js.  
De esta manera, los usuarios acceden mediante el puerto 80, mientras que la aplicación continúa ejecutándose internamente en el puerto 3000.

### Instalar Nginx

```bash
sudo apt update
sudo apt install nginx -y
```

Verificar estado del servicio:

```bash
sudo systemctl status nginx
```

## 6. Crear configuración del sitio

Crear un archivo de configuración para la aplicación:

```bash
sudo cp nginx/nodeapp.conf /etc/nginx/sites-available/nodeapp
```

## 7. Habilitar la configuración

Crear un enlace simbólico hacia `sites-enabled`:

```bash
sudo ln -sf /etc/nginx/sites-available/nodeapp /etc/nginx/sites-enabled/nodeapp
```

Eliminar enlace predeterminado 
```bash
sudo rm -f /etc/nginx/sites-enabled/default
```

## 8. Verificar configuración de Nginx

```bash
sudo nginx -t
```

Si la configuración es correcta, aparecerá un mensaje asi:

```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

## 9. Reiniciar Nginx

```bash
sudo systemctl restart nginx
```

## 10. Levantar la aplicación con Docker Compose

Ejecutar el siguiente comando desde la raíz del proyecto:

```bash
sudo docker compose up --build
```

Este comando construye la imagen de la aplicación Node.js y levanta automáticamente los servicios definidos en `docker-compose.yml`, incluyendo la aplicación y la base de datos MySQL.

La aplicación queda disponible en a traves de la redireccion de Nginx desde el puerto 80:


```text
http://localhost
```

Si se ejecuta desde una máquina virtual, se puede acceder usando la IP de la VM:

```text
http://[VM_IP]
```

Nginx redirige automáticamente las solicitudes hacia la aplicación Node.js que se ejecuta internamente en el puerto 3000.
