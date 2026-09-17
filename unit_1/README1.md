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

COMPRA DE INSUMOS → PRODUCCIÓN (PRODUCTO) → VENTA A TIENDA (INGRESO)

#### Entidades:

* **GASTO_INGREDIENTES:** Registra las compras de materias primas e insumos (harina, azúcar, aceite, bolsas) para medir el costo de producción actual frente al aumento de precios.
* **PRODUCCION:** Registra el volumen de bolsitas elaboradas por lote para determinar el costo unitario real de fabricación.
* **VENTA_TIENDA:** Registra las entregas de producto y el dinero cobrado a la tienda cliente.

#### Atributos:

* **Gasto_Ingredientes:** fecha, detalle de materia prima y monto gastado.
* **Produccion:** fecha y cantidad de bolsitas hechas.
* **Venta_Tienda:** fecha, cantidad de bolsitas vendidas, precio unitario (2.00 Bs) y monto cobrado.

















