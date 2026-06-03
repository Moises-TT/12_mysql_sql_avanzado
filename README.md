# 🗄️ MySQL con Docker — SQL Avanzado: Constraints, Foreign Keys y Views

> Creación de base de datos MySQL con Docker, definición de tablas con llaves foráneas, índices y generación de vistas.

---

## 📋 Descripción

Laboratorio de SQL avanzado usando **MySQL 8.0** en un contenedor Docker.  
Se practicó la creación de tablas con constraints completos (PRIMARY KEY, FOREIGN KEY, UNIQUE, INDEX), inserción de datos con Mockaroo, resolución de errores de integridad y creación de **vistas (VIEWS)** para enmascaramiento de datos.

---

## 🛠️ Stack y Herramientas

| Tecnología | Uso |
|-----------|-----|
| Docker | Contenedor MySQL 8.0 |
| MySQL 8.0 | Motor de base de datos |
| HeidiSQL / DBeaver | Cliente GUI |
| Mockaroo | Generación de datos de prueba |
| SQL | Lenguaje de definición y manipulación |

---

## 🔧 Levantar MySQL con Docker

```bash
# Crear contenedor MySQL con imagen estándar de Docker Hub
docker run -d   --name mysql-container   -e MYSQL_ROOT_PASSWORD=mi_clave_segura   -e MYSQL_DATABASE=magallanes   -p 3306:3306   mysql:8.0

# Verificar estado
docker ps
```

---

## 📁 Estructura de la Base de Datos

```
Base de datos: magallanes
├── clientes    (tabla principal)
└── compra      (tabla relacionada via FK)
```

---

## 🔨 Creación de Tablas

### Tabla `clientes`

```sql
CREATE TABLE clientes (
    id          INT AUTO_INCREMENT PRIMARY KEY,
    nombre      VARCHAR(100)    NOT NULL,
    apellido    VARCHAR(100)    NOT NULL,
    email       VARCHAR(150)    UNIQUE,
    telefono    VARCHAR(20),
    fecha_reg   DATE,
    INDEX idx_apellido (apellido)   -- Índice para búsquedas rápidas
);
```

> **INDEX**: Acelera búsquedas por apellido  
> **UNIQUE**: El email no puede repetirse entre registros

### Tabla `compra`

```sql
CREATE TABLE compra (
    id              INT AUTO_INCREMENT PRIMARY KEY,
    id_cliente      INT             NOT NULL,
    producto        VARCHAR(200)    NOT NULL,
    cantidad        INT             NOT NULL,
    precio          DECIMAL(10,2)   NOT NULL,
    fecha           DATE,
    FOREIGN KEY (id_cliente) REFERENCES clientes(id)
    -- FK relaciona compra con clientes
);
```

---

## 📥 Inserción de Datos

```sql
-- Insertar clientes manualmente
INSERT INTO clientes (nombre, apellido, email, telefono, fecha_reg)
VALUES
  ('Ana', 'García', 'ana@email.com', '300000001', '2024-01-15'),
  ('Luis', 'Martínez', 'luis@email.com', '300000002', '2024-02-20');

-- Insertar compra relacionada
INSERT INTO compra (id_cliente, producto, cantidad, precio, fecha)
VALUES (1, 'Laptop ASUS', 1, 2500000.00, '2024-03-10');
```

---

## 🛠️ Resolución de Errores

### Error con UNIQUE en campo `fecha`
Al importar datos de Mockaroo para la tabla `compra`, el campo fecha tenía INDEX UNIQUE pero Mockaroo generó fechas duplicadas.

```sql
-- Solución: eliminar el índice UNIQUE del campo fecha
ALTER TABLE compra DROP INDEX fecha;

-- Reimportar todos los datos exitosamente
```

---

## 👁️ Vistas (VIEWS) para Enmascaramiento

```sql
-- Crear vista con datos parcialmente enmascarados
CREATE VIEW vista_clientes AS
SELECT
    id,
    nombre,
    CONCAT(LEFT(apellido, 1), '*****') AS apellido_mascarado,
    CONCAT('****@', SUBSTRING_INDEX(email, '@', -1)) AS email_mascarado,
    fecha_reg
FROM clientes;

-- Usar la vista
SELECT * FROM vista_clientes;
```

---

## 📊 Datos Cargados con Mockaroo

| Tabla | Registros |
|-------|-----------|
| clientes | 100+ |
| compra | 100+ |

---

## 🧠 Aprendizajes

- Diseño de esquemas relacionales con constraints completos
- Diferencia entre PRIMARY KEY, FOREIGN KEY, UNIQUE e INDEX
- Diagnóstico y resolución de errores de integridad referencial
- Uso de vistas (VIEWS) para enmascaramiento y seguridad de datos
- Generación masiva de datos de prueba con Mockaroo
