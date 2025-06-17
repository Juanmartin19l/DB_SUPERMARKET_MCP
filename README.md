# Sistema de Base de Datos - Supermercado 🛒

Un proyecto de base de datos PostgreSQL completamente dockerizada que implementa el esquema de datos de un supermercado con capacidades de integración MCP para Claude Desktop.

## 📋 Tabla de Contenidos

- [Descripción del Proyecto](#-descripción-del-proyecto)
- [Prerrequisitos](#-prerrequisitos)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Instalación y Configuración](#-instalación-y-configuración)
- [Conexión a pgAdmin](#-conexión-a-pgadmin)
- [Restauración de la Base de Datos](#-restauración-de-la-base-de-datos)
- [Integración con Claude Desktop (MCP)](#-integración-con-claude-desktop-mcp)
- [Solución de Problemas](#-solución-de-problemas)

## 🎯 Descripción del Proyecto

Este proyecto proporciona una solución completa para el manejo de datos de un supermercado utilizando PostgreSQL en contenedores Docker. Incluye:

- **Base de datos PostgreSQL**: Esquema completo del supermercado
- **Backup de datos**: Archivo de respaldo con estructura y datos de prueba
- **Integración MCP**: Configuración para uso con Claude Desktop
- **Docker Compose**: Configuración de infraestructura automatizada

## ⚙️ Prerrequisitos

Antes de comenzar, asegúrate de tener instalado:

- [Docker](https://www.docker.com/get-started) v20.10 o superior
- [Docker Compose](https://docs.docker.com/compose/install/) v2.0 o superior
- [pgAdmin](https://www.pgadmin.org/download/) (para restauración del backup)
- [Claude Desktop](https://claude.ai/desktop) (opcional, para integración MCP)

## 📁 Estructura del Proyecto

```
DB_SUPERMARKET/
├── docker-compose.yml      # Configuración de Docker Compose
├── supermarket.backup      # Backup de la base de datos
└── README.md              # Documentación del proyecto
```

## 🚀 Instalación y Configuración

### 1. Iniciar los Contenedores

```bash
docker-compose up -d
```

Este comando iniciará dos servicios:

**PostgreSQL** (puerto 5432):

- **Usuario**: admin
- **Contraseña**: admin
- **Base de datos**: admin_db

**pgAdmin** (puerto 8080):

- **Email**: admin@admin.com
- **Contraseña**: admin
- **URL**: http://localhost:8080

### 2. Verificar que los Contenedores estén Ejecutándose

```bash
docker-compose ps
```

Deberías ver dos contenedores corriendo:

- `postgres_db` (PostgreSQL)
- `pgadmin_web` (pgAdmin)

## 🔗 Conexión a pgAdmin

pgAdmin se incluye como parte del stack de Docker y se ejecuta automáticamente. No necesitas instalarlo por separado.

### Acceso a pgAdmin Web

1. **Abrir navegador** y ir a: http://localhost:8080

2. **Iniciar sesión** con las credenciales:
   - **Email**: `admin@admin.com`
   - **Contraseña**: `admin`

### Configurar Conexión al Servidor PostgreSQL

Una vez dentro de pgAdmin:

1. **Agregar nuevo servidor**:

   - Clic derecho en "Servers" → "Register" → "Server..."

2. **Configurar pestaña "General"**:

   - **Name**: `Supermercado DB` (o cualquier nombre descriptivo)

3. **Configurar pestaña "Connection"**:

   - **Host name/address**: `postgres` (nombre del servicio en Docker)
   - **Port**: `5432`
   - **Username**: `admin`
   - **Password**: `admin`

4. **Guardar la conexión**:
   - Marcar "Save password" (recomendado)
   - Clic en "Save"

### ✅ Verificar la Conexión

Una vez configurado correctamente, deberías ver:

- El servidor "Supermercado DB" en el panel izquierdo
- La base de datos `admin_db` expandible
- Posibilidad de explorar esquemas y tablas

### 🚀 Acceso Rápido al Query Tool

Para ejecutar consultas SQL:

1. **Expandir** el servidor conectado
2. **Expandir** `Databases` → `admin_db`
3. **Clic derecho** en `admin_db` → "Query Tool"
4. ¡Listo para ejecutar comandos SQL!

### 🔧 Troubleshooting de pgAdmin

**Si no puedes acceder a pgAdmin:**

- Verificar que ambos contenedores estén ejecutándose: `docker-compose ps`
- Comprobar que el puerto 8080 no esté ocupado
- Revisar logs: `docker-compose logs pgadmin`

**Si no puedes conectarte al servidor PostgreSQL desde pgAdmin:**

- Usar `postgres` como host (no `localhost` ni `127.0.0.1`)
- Verificar que las credenciales sean exactamente: `admin` / `admin`
- Revisar logs de PostgreSQL: `docker-compose logs postgres`

## 🔄 Restauración de la Base de Datos

### Paso 1: Crear el Rol PostgreSQL (CRÍTICO)

⚠️ **IMPORTANTE**: Antes de restaurar el backup, ejecuta este comando SQL:

```sql
CREATE ROLE postgres;
```

**Formas de ejecutar este comando:**

- pgAdmin → Query Tool
- Cliente de línea de comandos SQL
- Cualquier cliente PostgreSQL (DBeaver, DataGrip, etc.)

### Paso 2: Restaurar el Backup en pgAdmin

1. **Abrir pgAdmin** y conectarse al servidor PostgreSQL
2. **Clic derecho** en la base de datos de destino
3. **Seleccionar** "Restore..."
4. **Configurar la restauración**:
   - **Format**: Custom or tar
   - **Filename**: Seleccionar `supermarket.backup`
   - **Options**: Marcar las opciones necesarias
5. **Ejecutar** la restauración

### Paso 3: Verificar la Restauración

Ejecuta una consulta de prueba para confirmar que los datos se restauraron correctamente:

```sql
SELECT table_name
FROM information_schema.tables
WHERE table_schema = 'public';
```

## 🔌 Integración con Claude Desktop (MCP)

La integración con Model Context Protocol (MCP) permite que Claude Desktop interactúe directamente con la base de datos para análisis y consultas inteligentes.

### Configuración Paso a Paso

#### 1. Verificar que la Base de Datos esté Activa

```bash
docker-compose up -d
docker-compose ps
```

#### 2. Configurar Claude Desktop

Edita el archivo de configuración de Claude Desktop en tu sistema:

**📍 Ubicaciones del archivo de configuración:**

- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Linux**: `~/.config/Claude/claude_desktop_config.json`

**Configuración a agregar:**

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://admin:admin@localhost:5432/admin_db"
      ]
    }
  }
}
```

#### 3. Reiniciar Claude Desktop

Cierra completamente Claude Desktop y ábrelo nuevamente para que la configuración tome efecto.

### ✅ Verificación de la Integración

Una vez configurado correctamente, Claude Desktop podrá:

- 🔍 **Analizar el esquema** de la base de datos automáticamente
- 📊 **Ejecutar consultas SQL** en tiempo real
- 💡 **Generar insights** sobre los datos del supermercado
- 🔗 **Responder preguntas** sobre estructura y contenido de las tablas

### 📝 Notas Importantes sobre MCP

- La URL de conexión debe coincidir exactamente con tu configuración de `docker-compose.yml`
- Si modificas las credenciales de la base de datos, actualiza también la configuración MCP
- El servidor MCP se descarga automáticamente en la primera ejecución
- Asegúrate de que no hay conflictos de puertos (5432 debe estar libre)

## 🛠️ Solución de Problemas

### Errores Comunes en la Restauración

| Problema                         | Causa                                 | Solución                                  |
| -------------------------------- | ------------------------------------- | ----------------------------------------- |
| `role "postgres" does not exist` | No se ejecutó `CREATE ROLE postgres;` | Ejecutar el comando SQL antes del restore |
| Conexión rechazada               | Docker no está ejecutándose           | Verificar con `docker-compose ps`         |
| Permisos insuficientes           | Usuario sin privilegios               | Conectarse como administrador             |

### Comandos de Diagnóstico

**Verificar rol postgres:**

```sql
SELECT * FROM pg_roles WHERE rolname = 'postgres';
```

**Verificar conexión a la base de datos:**

```bash
docker-compose exec postgres psql -U admin -d admin_db -c "\l"
```

**Ver logs del contenedor:**

```bash
docker-compose logs postgres
```

### Problemas de Integración MCP

**Claude Desktop no detecta la base de datos:**

1. Verificar que el archivo de configuración esté en la ubicación correcta
2. Validar la sintaxis JSON del archivo de configuración
3. Comprobar que la base de datos esté accesible desde localhost:5432
4. Reiniciar completamente Claude Desktop

**Error de conexión MCP:**

1. Verificar que las credenciales en la URL coincidan con docker-compose.yml
2. Comprobar que no hay firewall bloqueando el puerto 5432
3. Intentar la conexión manual con psql o pgAdmin

---

**Desarrollado con ❤️ para el curso de Base de Datos**
