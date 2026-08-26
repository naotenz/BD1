# Base de Datos 1

# 🗄️ Curso de Base de Datos I - Ciclo [2026/2]

Bienvenido al repositorio central de mis prácticas, proyectos y recursos del curso de **Base de Datos I**. Este repositorio documenta mi viaje desde entender qué es un dato hasta la implementación, normalización y consulta de una base de datos relacional completa.

> Estudiante: **[NAOTO YONEKURA]**
> Institución: **[UAGRM]**

---

## 🎯 Objetivos del Repositorio

1.  **Centralizar** todos los scripts SQL y diagramas generados durante el curso.
2.  **Documentar** el proceso de diseño, desde el requerimiento hasta el despliegue.
3.  **Servir de referencia** rápida para sintaxis SQL y buenas prácticas de modelado.

---

## 🛠️ Stack Tecnológico y Herramientas

Para este curso, estamos utilizando un conjunto de herramientas estándar en la industria para el diseño y gestión de bases de datos relacionales:

| Herramienta | Uso | Descripción |
| :--- | :--- | :--- |
| <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/mysql/mysql-original-wordmark.svg" alt="mysql" width="40" height="40"/> | **Motor de BD** | **MySQL (v8.0+)**. Sistema de gestión de base de datos relacional (RDBMS) de código abierto más popular. |
| <img src="https://visualstudio.microsoft.com/wp-content/uploads/2019/09/vs-code-2019.svg" alt="vscode" width="40" height="40"/> | **Editor de Código** | **VS Code**. Usado para escribir scripts `.sql`, Markdown y gestionar el repositorio Git. Extensiones recomendadas: *SQLTools*, *MySQL*, *Markdown All in One*. |
| <img src="https://www.mysql.com/common/logos/logo-mysql-workbench-product.svg" alt="workbench" width="50"/> | **Diseño y GUI** | **MySQL Workbench**. Herramienta visual unificada para arquitectos, desarrolladores y administradores de bases de datos. Permite generar diagramas *Entidad-Relación* y exportarlos a SQL (Forward Engineering). |

---

## 🏗️ Hoja de Ruta del Aprendizaje

Este curso se divide en fases lógicas que quedan reflejadas en la estructura de carpetas de este repositorio:

### Fase 1: Modelado Conceptual y Lógico (DER)
*   Análisis de requerimientos y reglas de negocio.
*   Identificación de Entidades, Atributos y Relaciones.
*   Determinación de Cardinalidades y Claves (PK/FK).
*   Diseño visual en MySQL Workbench con notación *Crow's Foot*.

### Fase 2: Normalización
*   Aplicación de las Formas Normales para evitar redundancia y anomalías.
*   **1FN:** Eliminación de grupos repetitivos.
*   **2FN:** Eliminación de dependencias parciales.
*   **3FN:** Eliminación de dependencias transitivas.

### Fase 3: Implementación DDL (Data Definition Language)
*   Creación de scripts SQL para generar la estructura.
*   Comandos: `CREATE DATABASE`, `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE`.
*   Definición de tipos de datos y restricciones (NOT NULL, UNIQUE, DEFAULT).

### Fase 4: Manipulación y Consultas DML (Data Manipulation Language)
*   Carga de datos de prueba (`INSERT`).
*   Actualización y borrado de registros (`UPDATE`, `DELETE`).
*   Consultas complejas: `SELECT` con `WHERE`, `ORDER BY`, `GROUP BY`, `HAVING`.
*   Uso extensivo de `JOINs` (INNER, LEFT, RIGHT) para conectar tablas.

---

## 📁 Estructura del Repositorio

La organización de archivos sigue el progreso del curso:

```text
base-de-datos-1/
├── 01_diagramas/           # Archivos .mwb (Workbench) y exportaciones .png/.pdf
│   ├── conceptual/         # Primeros borradores de DER
│   └── logico/             # Modelo Relacional final
├── 02_scripts_ddl/         # Scripts de creación de estructura
│   ├── 01_create_db.sql
│   └── 02_create_tables.sql
├── 03_scripts_dml/         # Scripts de manipulación de datos
│   ├── 01_inserts_pruebas.sql
│   └── 02_consultas_basicas.sql
├── 04_ejercicios/          # Prácticas sueltas y tareas de clase
└── proyecto_final/         # Todo lo relacionado al proyecto integrador
    ├── README.md           # Documentación específica del proyecto
    ├── scripts_completos.sql
    └── backup_db.sql


    -- Crear Base de Datos con codificación correcta
CREATE DATABASE IF NOT EXISTS mi_base_datos
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

USE mi_base_datos;

-- Crear una tabla ejemplo (Padre)
CREATE TABLE roles (
    id_rol INT AUTO_INCREMENT,
    nombre_rol VARCHAR(50) NOT NULL,
    CONSTRAINT pk_roles PRIMARY KEY (id_rol)
);

-- Crear tabla con FK (Hija)
CREATE TABLE usuarios (
    id_usuario INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(30) NOT NULL UNIQUE,
    fk_rol INT,
    FOREIGN KEY (fk_rol) REFERENCES roles(id_rol)
        ON UPDATE CASCADE
        ON DELETE SET NULL
);


-- Insertar datos
INSERT INTO roles (nombre_rol) VALUES ('Administrador'), ('Cliente');

-- Consulta con INNER JOIN y alias
SELECT
    u.id_usuario,
    u.username,
    r.nombre_rol AS rol
FROM usuarios AS u
INNER JOIN roles AS r ON u.fk_rol = r.id_rol;