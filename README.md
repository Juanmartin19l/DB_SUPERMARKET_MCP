# Base de Datos Supermercado

Este proyecto contiene la configuración de Docker para una base de datos PostgreSQL y un backup del esquema del supermercado.

## Estructura del proyecto

- `docker-compose.yml`: Configuración de Docker Compose para levantar PostgreSQL
- `supermarket.backup`: Archivo de backup de la base de datos del supermercado

## Instrucciones para restaurar la base de datos

### Paso 1: Levantar el contenedor de PostgreSQL

```bash
docker-compose up -d
```

### Paso 2: Crear el rol postgres (REQUERIDO antes del restore)

Antes de restaurar el backup, es necesario crear el rol postgres. Ejecuta el siguiente comando en la base de datos:

```sql
CREATE ROLE postgres;
```

Puedes ejecutar este comando:

- Desde pgAdmin en el Query Tool
- Desde la línea de comandos con sql
- Desde cualquier cliente de PostgreSQL

### Paso 3: Restaurar el backup

El archivo `supermarket.backup` debe ser restaurado usando **pgAdmin**:

1. Abre pgAdmin
2. Conecta a tu servidor PostgreSQL
3. Haz clic derecho en la base de datos de destino
4. Selecciona **Restore...**
5. En el diálogo de restore:
   - **Format**: Custom or tar
   - **Options**: Selecciona update y arrastra el archivo `supermarket.backup`
   - **Filename**: Selecciona el archivo `supermarket.backup`
6. Haz clic en **Restore**

## Notas importantes

- ⚠️ **Es crítico ejecutar `CREATE ROLE postgres;` antes de hacer el restore del backup**
- El backup fue creado desde pgAdmin y debe ser restaurado usando pgAdmin
- Asegúrate de que el contenedor de PostgreSQL esté ejecutándose antes de intentar la restauración

## Troubleshooting

Si encuentras errores durante el restore:

- Verifica que el rol postgres existe: `SELECT * FROM pg_roles WHERE rolname = 'postgres';`
- Asegúrate de tener permisos suficientes en la base de datos de destino
- Revisa los logs de pgAdmin para más detalles sobre el error
