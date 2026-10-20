Juan David Diaz y Juan Sebastian Pedraza

# Proyecto HAProxy: Balanceo de carga con Docker

## Descripción

Este proyecto implementa una arquitectura web basada en contenedores Docker, utilizando HAProxy como balanceador de carga para distribuir las solicitudes entre dos servidores backend desarrollados con FastAPI.

El objetivo es demostrar el funcionamiento del balanceo de carga, la comunicación entre contenedores, la configuración de HAProxy y la evaluación del rendimiento mediante Apache JMeter.

## Tecnologías utilizadas

- **Docker:** ejecución de los servicios en contenedores.
- **Docker Compose:** administración de los servicios y la red.
- **HAProxy 2.8:** balanceo de carga y estadísticas.
- **Python 3.11:** desarrollo del backend.
- **FastAPI y Uvicorn:** creación y ejecución de la API.
- **Node.js 20 y Vite:** construcción y ejecución del frontend.
- **Apache JMeter:** pruebas de rendimiento y generación de informes.

## Arquitectura

La aplicación está compuesta por cuatro servicios:

1. **Frontend:** interfaz web de la aplicación.
2. **Backend 1:** primera instancia de la API.
3. **Backend 2:** segunda instancia de la API.
4. **HAProxy:** recibe las solicitudes y las distribuye entre los servicios correspondientes.

Los contenedores se comunican mediante la red Docker `app-net`.

### Flujo de las solicitudes

- Las solicitudes web se dirigen al frontend.
- Las solicitudes que comienzan por `/api/` se envían al backend.
- HAProxy utiliza el algoritmo `roundrobin` para distribuir las solicitudes entre las dos instancias del backend.
- La ruta `/stats` permite consultar las estadísticas de HAProxy.

## Estructura del proyecto

```text
HAproxy-Proyecto/
├── backend/
│   ├── dockerfile
│   └── main.py
├── frontend/
│   ├── dockerfile
│   ├── index.html
│   ├── package.json
│   └── vite.config.js
├── haproxy/
│   └── haproxy.cfg
├── docs/
│   └── architecture.png
├── informe_haproxy_definitivo/
├── informe_jmeter/
├── informe_jmeter_final/
├── docker-compose.yml
├── prueba_haproxy.jmx
├── prueba_haproxy_backup.jmx
├── prueba_haproxy_limpia.jmx
├── jmeter.log
└── README.md
```

## Requisitos

Antes de ejecutar el proyecto, se necesita:

- Docker Desktop o Docker Engine con Docker Compose.
- Git para clonar el repositorio.
- Un navegador web.
- Apache JMeter, si se desean ejecutar nuevamente las pruebas de rendimiento.

## Instalación y ejecución

### 1. Clonar el repositorio

```bash
git clone https://github.com/juandiazm-a/HAproxy-Proyecto.git
cd HAproxy-Proyecto
```

### 2. Construir las imágenes e iniciar los servicios

```bash
docker compose up --build -d
```

Este comando construye las imágenes necesarias e inicia los cuatro servicios en segundo plano.

### 3. Verificar los contenedores

```bash
docker compose ps
```

Los servicios esperados son `frontend`, `backend1`, `backend2` y `haproxy`.

### 4. Consultar la aplicación

Con los servicios iniciados, abre las siguientes direcciones:

| Servicio | URL |
|---|---|
| Aplicación web | http://localhost:8080 |
| API | http://localhost:8080/api/hello |
| Estadísticas HAProxy | http://localhost:8080/stats |

La disponibilidad de cada ruta depende de que los servicios estén funcionando correctamente.

## Configuración del balanceo de carga

HAProxy utiliza el modo HTTP para recibir las solicitudes y seleccionar el backend correspondiente.

La configuración incluye:

- **Frontend HTTP:** escucha en el puerto 80 del contenedor.
- **Backend web:** envía las solicitudes web al servicio `frontend`, en el puerto 3000.
- **Backend API:** distribuye las solicitudes entre `backend1` y `backend2`, en el puerto 8000.
- **Algoritmo `roundrobin`:** alterna la distribución de solicitudes entre las instancias disponibles.
- **Health checks:** verifican la disponibilidad de los servidores configurados.
- **Estadísticas:** permite observar el estado de los servidores desde `/stats`.

El puerto 8080 del equipo anfitrión se conecta con el puerto 80 del contenedor HAProxy.

## Pruebas de funcionamiento

### Verificar la API

Ejecuta:

```bash
curl http://localhost:8080/api/hello
```

Respuesta esperada:

```json
{"message":"Hello from FastAPI!"}
```

### Verificar el estado de HAProxy

Abre:

http://localhost:8080/stats

Comprueba que `backend1` y `backend2` aparezcan disponibles (`UP`).

### Consultar los registros

```bash
docker compose logs -f
```

Para detener la visualización de registros, utiliza `Ctrl+C`.

## Pruebas de rendimiento con Apache JMeter

El proyecto incluye planes de prueba `.jmx` y tres carpetas de informes generados a partir de las pruebas.

El informe definitivo registrado durante las pruebas presentó los siguientes resultados:

| Métrica | Resultado |
|---|---:|
| Solicitudes ejecutadas | 200 |
| Solicitudes fallidas | 0 |
| Tasa de error | 0 % |
| Tiempo promedio de respuesta | 2,22 ms |
| Tiempo mínimo de respuesta | 1 ms |
| Tiempo máximo de respuesta | 25 ms |
| Mediana | 2 ms |
| Percentil 90 | 3 ms |
| Percentil 95 | 3,95 ms |
| Percentil 99 | 6 ms |
| Rendimiento registrado | 21,22 transacciones/s |

Estos resultados corresponden a la ejecución registrada y no garantizan el mismo rendimiento en otros equipos o condiciones de prueba.

### Consultar los informes

Los informes HTML se encuentran en:

- `informe_haproxy_definitivo/`
- `informe_jmeter/`
- `informe_jmeter_final/`

Para revisarlos, abre el archivo `index.html` de la carpeta correspondiente.

## Comandos útiles

Detener los servicios:

```bash
docker compose stop
```

Iniciar nuevamente los servicios:

```bash
docker compose start
```

Detener y eliminar los contenedores y la red del proyecto:

```bash
docker compose down
```

Reconstruir y reiniciar los servicios:

```bash
docker compose up --build -d
```

<img width="1892" height="942" alt="image" src="https://github.com/user-attachments/assets/3e5c1791-7ce9-4ee0-82b1-3103a87d7c22" />


<img width="1909" height="639" alt="evidencia 2" src="https://github.com/user-attachments/assets/30c53386-2a67-45ff-8cea-c365968ef5ad" />


<img width="1918" height="515" alt="image" src="https://github.com/user-attachments/assets/6db06925-f484-4320-afa5-567e2908aed7" />

