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

















