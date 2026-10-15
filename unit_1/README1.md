# Información de la entrevista – Negocio de Karinto

### 1. ¿Cómo fue que se animó a armar este negocio?

Por necesidad, quería tener un ingreso propio para cubrir sus gastos.

### 2. ¿Qué producto venden en este negocio?

Venden Karinto. El precio es de aproximadamente 0,50 Bs por gramo y cada bolsita cuesta aproximadamente 2 Bs.

### 3. ¿Qué es lo que más les dificulta en el día a día?

Algunos productos e ingredientes resultan cada vez más caros y además existe dificultad para llevar correctamente el conteo de los productos y las ganancias.

### 4. Cuando el cliente paga, ¿ustedes le dan algún tipo de recibo o comprobante?

No utilizan recibos. Según la información proporcionada, si el negocio no supera una ganancia de 1.500 Bs, no necesita presentar recibo.

### Narrativa del Negocio
El negocio se dedica a la elaboración y comercialización artesanal de Karinto, un producto que se empaqueta en bolsitas con un costo aproximado de 2 Bs (calculado a un precio base de 0,50 Bs por gramo). El flujo de comercialización opera en una cadena de distribución directa e indirecta: la Dueña produce y vende el producto a distintos Vendedores (o distribuidores), quienes a su vez revenden el Producto a los Clientes finales.

Actualmente, el negocio enfrenta dos problemas principales en su operación diaria:

Falta de control financiero e inventario: No existe un registro estructurado para llevar el conteo exacto de la producción, el stock disponible y el cálculo real de ganancias frente al costo variable de los ingredientes.

Ausencia de comprobantes: Las ventas no emiten recibos debido a que el margen operativo se mantiene por debajo del umbral tributario obligatorio (1.500 Bs de ganancia); sin embargo, esto genera vacíos de información sobre qué productos se vendieron, a quién y mediante qué transacción.


### Base de datos conceptual

### Modelo relacional propuesto

El flujo de información se organiza así:

**COMPRA DE INSUMOS → DETALLE DE COMPRA → PRODUCCIÓN → DETALLE DE PRODUCCIÓN → PRODUCTO → DETALLE DE VENTA → VENTA A TIENDA**

Cada detalle registra una línea por ingrediente o producto. De esta forma, una compra puede incluir varios ingredientes, una producción puede consumir varios ingredientes y una venta puede incluir varios productos.

| Tabla | Campos principales | Propósito |
|---|---|---|
| **INGREDIENTE** | `ingrediente_id` (PK), nombre, unidad_medida | Catálogo de harina, azúcar, aceite, bolsas y otros insumos. |
| **PROVEEDOR** | `proveedor_id` (PK), nombre, contacto | Catálogo de quienes venden los insumos. |
| **COMPRA_INSUMO** | `compra_id` (PK), `proveedor_id` (FK), fecha | Encabezado de cada compra de materias primas. |
| **DETALLE_COMPRA** | `compra_id` (PK, FK), `ingrediente_id` (PK, FK), cantidad_comprada, costo_unitario | Registra la cantidad y el costo de cada ingrediente comprado. |
| **PRODUCTO** | `producto_id` (PK), nombre, gramos_por_bolsa, precio_referencia | Catálogo de productos elaborados para la venta. |
| **PRODUCCION** | `produccion_id` (PK), `producto_id` (FK), fecha, cantidad_producida | Registra cada lote y cuántas unidades de producto se elaboraron. |
| **DETALLE_PRODUCCION** | `produccion_id` (PK, FK), `ingrediente_id` (PK, FK), cantidad_utilizada | Conecta cada lote con sus ingredientes y la cantidad consumida de cada uno. |
| **TIENDA** | `tienda_id` (PK), nombre, contacto | Catálogo de tiendas o distribuidores clientes. |
| **VENTA_TIENDA** | `venta_id` (PK), `tienda_id` (FK), fecha | Encabezado de la venta o entrega a una tienda. |
| **DETALLE_VENTA** | `venta_id` (PK, FK), `producto_id` (PK, FK), cantidad_vendida, precio_unitario | Conecta una venta con cada producto, su cantidad y el precio aplicado. |

**PK** significa clave primaria y **FK** significa clave foránea. El importe por línea se obtiene multiplicando la cantidad por el precio unitario; el total de la venta se obtiene sumando sus líneas. Así se evita guardar importes calculables dos veces. El precio de referencia puede ser Bs 2,00 por bolsita, mientras que el precio unitario de cada detalle conserva el precio realmente aplicado en esa venta.

### Normalización

El modelo propuesto cumple las formas normales siguientes, siempre que cada campo almacene un solo valor y se respeten las claves indicadas:

* **Primera forma normal (1FN):** cada campo contiene un valor único y no hay listas de ingredientes o productos dentro de una fila. Los artículos se guardan como filas individuales en `DETALLE_COMPRA`, `DETALLE_PRODUCCION` y `DETALLE_VENTA`.
* **Segunda forma normal (2FN):** las tablas de detalle usan claves compuestas. Sus cantidades y precios dependen de la combinación completa de la transacción y del ingrediente o producto, no solo de una parte de la clave.
* **Tercera forma normal (3FN):** los datos descriptivos de ingredientes, productos, proveedores y tiendas se guardan en sus catálogos. Compras y ventas guardan las claves foráneas; los detalles guardan sus claves y datos propios de la transacción, evitando repetir nombres, contactos o unidades.

La cantidad de ingredientes corresponde a **DETALLE_PRODUCCION**, porque representa lo consumido para elaborar un lote. La cantidad de productos vendidos corresponde a **DETALLE_VENTA**. El inventario disponible puede calcularse a partir de la producción menos las ventas, sin mezclar ambos tipos de cantidad en una misma tabla.









# 🗄️ Implementación Física y Consultas de Control: Base de Datos Karinto

## 📖 Introducción Técnica: ¿Qué es SQL y por qué se utiliza?

**SQL (Structured Query Language)** es el lenguaje estándar para la gestión y manipulación de bases de datos relacionales. En este proyecto, la implementación física en SQL cumple las siguientes funciones fundamentales:

* **Garantizar la Integridad de los Datos (DDL):** Permite definir la estructura relacional en **Tercera Forma Normal (3FN)**, aplicando restricciones de clave primaria (`PRIMARY KEY`), clave foránea (`FOREIGN KEY`) e integridad referencial (`ON DELETE RESTRICT/CASCADE`). Esto evita redundancias y previene el registro de datos desvinculados o inconsistentes.
* **Simular la Operativa Real (DML):** Facilita la carga de datos de prueba para registrar las transacciones reales del taller artesanal: adquisición de insumos, procesos de transformación/producción y ventas a distribuidores.
* **Soportar la Toma de Decisiones (DQL):** Proporciona la capacidad de ejecutar consultas analíticas complejas que calculan costos de producción, márgenes de ganancia e impacto de la inflación de materia prima en tiempo real.

---

## 📌 Documentación de la Estructura de la Base de Datos

El script se divide en tres secciones ejecutables:

1. **Estructura DDL (Data Definition Language):** Creación de la base de datos `karinto_db` y sus 10 tablas relacionales.
2. **Población DML (Data Manipulation Language):** Inserción de datos iniciales para catálogos, registros de compra, lotes de producción y ventas a clientes.
3. **Consultas DQL (Data Query Language):** Consultas de verificación y extracción de información clave.

```sql
-- ============================================================
-- BASE DE DATOS: KARINTO (SISTEMA DE CONTROL Y RENTABILIDAD)
-- ============================================================

CREATE DATABASE IF NOT EXISTS karinto_db;
USE karinto_db;

-- ============================================================
-- 1. ESTRUCTURA DE TABLAS (DDL)
-- ============================================================

-- Catálogos (Entidades Fuertes)
CREATE TABLE INGREDIENTE (
    ingrediente_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE,
    unidad_medida VARCHAR(20) NOT NULL
);

CREATE TABLE PROVEEDOR (
    proveedor_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    contacto VARCHAR(100)
);

CREATE TABLE PRODUCTO (
    producto_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    gramos_por_bolsa DECIMAL(10,2) NOT NULL,
    precio_referencia DECIMAL(10,2) NOT NULL DEFAULT 2.00
);

CREATE TABLE TIENDA (
    tienda_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    contacto VARCHAR(100)
);

-- Transacciones e Intersecciones
CREATE TABLE COMPRA_INSUMO (
    compra_id INT AUTO_INCREMENT PRIMARY KEY,
    proveedor_id INT NOT NULL,
    fecha DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL,
    FOREIGN KEY (proveedor_id) REFERENCES PROVEEDOR(proveedor_id) ON DELETE RESTRICT
);

CREATE TABLE DETALLE_COMPRA (
    compra_id INT NOT NULL,
    ingrediente_id INT NOT NULL,
    cantidad_comprada DECIMAL(10,2) NOT NULL,
    costo_unitario DECIMAL(10,2) NOT NULL,
    PRIMARY KEY (compra_id, ingrediente_id),
    FOREIGN KEY (compra_id) REFERENCES COMPRA_INSUMO(compra_id) ON DELETE CASCADE,
    FOREIGN KEY (ingrediente_id) REFERENCES INGREDIENTE(ingrediente_id) ON DELETE RESTRICT
);

CREATE TABLE PRODUCCION (
    produccion_id INT AUTO_INCREMENT PRIMARY KEY,
    producto_id INT NOT NULL,
    fecha DATE NOT NULL,
    cantidad_producida INT NOT NULL,
    FOREIGN KEY (producto_id) REFERENCES PRODUCTO(producto_id) ON DELETE RESTRICT
);

CREATE TABLE DETALLE_PRODUCCION (
    produccion_id INT NOT NULL,
    ingrediente_id INT NOT NULL,
    cantidad_utilizada DECIMAL(10,2) NOT NULL,
    PRIMARY KEY (produccion_id, ingrediente_id),
    FOREIGN KEY (produccion_id) REFERENCES PRODUCCION(produccion_id) ON DELETE CASCADE,
    FOREIGN KEY (ingrediente_id) REFERENCES INGREDIENTE(ingrediente_id) ON DELETE RESTRICT
);

CREATE TABLE VENTA_TIENDA (
    venta_id INT AUTO_INCREMENT PRIMARY KEY,
    tienda_id INT NOT NULL,
    fecha DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL,
    FOREIGN KEY (tienda_id) REFERENCES TIENDA(tienda_id) ON DELETE RESTRICT
);

CREATE TABLE DETALLE_VENTA (
    venta_id INT NOT NULL,
    producto_id INT NOT NULL,
    cantidad_vendida INT NOT NULL,
    precio_unitario DECIMAL(10,2) DEFAULT 2.00 NOT NULL,
    PRIMARY KEY (venta_id, producto_id),
    FOREIGN KEY (venta_id) REFERENCES VENTA_TIENDA(venta_id) ON DELETE CASCADE,
    FOREIGN KEY (producto_id) REFERENCES PRODUCTO(producto_id) ON DELETE RESTRICT
);

-- ============================================================
-- 2. DATOS DE PRUEBA (DML)
-- ============================================================

INSERT INTO INGREDIENTE (nombre, unidad_medida) VALUES 
('Harina de Trigo', 'kg'),
('Azúcar', 'kg'),
('Aceite Vegetal', 'L'),
('Bolsas Plásticas', 'Unidades');

INSERT INTO PROVEEDOR (nombre, contacto) VALUES 
('Distribuidora Los Andes', '70012345'),
('Empaques S.R.L.', NULL);

INSERT INTO PRODUCTO (nombre, gramos_por_bolsa, precio_referencia) VALUES 
('Bolsita Karinto Tradicional', 20.00, 2.00);

INSERT INTO TIENDA (nombre, contacto) VALUES 
('Donaji', '71000001'),
('Abarrotes Don Pedro', '72000002');

INSERT INTO COMPRA_INSUMO (proveedor_id, fecha) VALUES (1, '2026-10-10 09:00:00');

INSERT INTO DETALLE_COMPRA (compra_id, ingrediente_id, cantidad_comprada, costo_unitario) VALUES 
(1, 1, 10.00, 5.00),
(1, 2, 30.00, 6.50),
(1, 3, 2.00, 12.00);

INSERT INTO PRODUCCION (producto_id, fecha, cantidad_producida) VALUES (1, '2026-10-12', 100);

INSERT INTO DETALLE_PRODUCCION (produccion_id, ingrediente_id, cantidad_utilizada) VALUES 
(1, 1, 2.00),
(1, 2, 1.50),
(1, 3, 0.50);

INSERT INTO VENTA_TIENDA (tienda_id, fecha) VALUES (1, '2026-10-14 15:30:00');

INSERT INTO DETALLE_VENTA (venta_id, producto_id, cantidad_vendida, precio_unitario) VALUES 
(1, 1, 50, 2.00);
```

---

## 💡 Utilidad Operativa de las Consultas (Toma de Decisiones)

> **Nota para la presentación:** Estas consultas (`DQL`) no son solo filtros de inspección, sino herramientas analíticas clave para la gestión del negocio. Permiten detectar variaciones en los costos al comparar el precio histórico de compra de los ingredientes contra el precio de venta fijo del producto final ($2.00\text{ Bs}$). De esta manera, el sistema facilita la localización de desviaciones financieras y ayuda a tomar decisiones estratégicas en tiempo real, como renegociar con proveedores, optimizar la receta o evaluar un incremento en el precio de venta frente a la inflación.

```sql
-- ============================================================
-- 3. CONSULTAS DE CONTROL Y VERIFICACIÓN (DQL)
-- ============================================================

-- 1. Todos los registros
SELECT * FROM INGREDIENTE;

-- 2. Columnas específicas
SELECT ingrediente_id, nombre, unidad_medida FROM INGREDIENTE;

-- 3. Con alias
SELECT ingrediente_id AS "Código Insumo", CONCAT(nombre, ' (', unidad_medida, ')') AS "Descripción Completa"
FROM INGREDIENTE;

-- 4. Con filtro simple
SELECT * FROM PRODUCCION WHERE producto_id = 1;

-- 5. Con múltiples condiciones
SELECT * FROM DETALLE_COMPRA
WHERE cantidad_comprada >= 10 AND costo_unitario <= 6.50;

-- 6. LIKE - búsqueda por patrón
SELECT * FROM INGREDIENTE WHERE nombre LIKE 'Har%';
SELECT * FROM INGREDIENTE WHERE nombre LIKE '%a%';

-- 7. BETWEEN - rango de valores
SELECT * FROM DETALLE_VENTA WHERE precio_unitario BETWEEN 1.50 AND 2.00;

-- 8. IN - lista de valores
SELECT * FROM DETALLE_COMPRA WHERE ingrediente_id IN (1, 2, 3);

-- 9. IS NULL / IS NOT NULL
SELECT * FROM PROVEEDOR WHERE contacto IS NULL;

-- 10. ORDER BY
SELECT * FROM INGREDIENTE ORDER BY nombre ASC;

-- 11. LIMIT
SELECT * FROM DETALLE_VENTA ORDER BY cantidad_vendida DESC LIMIT 5;
```





