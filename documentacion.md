# Documentación del proyecto: dashboard de desempeño comercial de Andes Retail Group

Proceso completo de construcción del dashboard en Power BI Desktop, desde la conexión a los datos hasta la narrativa para la dirección. El resumen y las capturas están en el [README](README.md).

## 1. Conexión y exploración
- Se importó `Andes_Retail_Group_2024_2025.xlsx` en Power BI Desktop (Obtener datos → Excel) y se abrió en Power Query para revisarlo antes de cargarlo.
- El dataset tiene 5.000 filas y 12 columnas; cada fila representa un pedido.
- Columnas clave:
  - Métricas: Ingresos, Costo, Unidades_Vendidas, Precio_Unitario.
  - Dimensiones de análisis: Fecha_Pedido, Estación, País, Región, Segmento_Cliente, Categoría_Producto.
  - Identificadores: ID_Pedido, ID_Cliente.
- Los tipos detectados automáticamente eran correctos, excepto Precio_Unitario e Ingresos, que se cargaron como número entero.

## 2. Preparación de los datos
- Fecha_Pedido: se definió como tipo Fecha con configuración regional Español (Latinoamérica).
- Tipos numéricos: Precio_Unitario e Ingresos se cambiaron de entero a decimal, según el diccionario de datos. ID_Pedido y Unidades_Vendidas se mantienen como enteros y Costo como decimal.
- Nivel_Venta: columna condicional de tipo texto. Ingresos ≥ 1000 → "Venta Alta"; de lo contrario → "Venta Baja".
- Calidad: con el perfil de columna sobre todo el conjunto de datos (5.000 filas), las 13 columnas muestran 100 % de valores válidos, 0 % errores y 0 % vacíos. No fue necesaria limpieza adicional.

## 3. Diseño y planificación del dashboard

### Vista Overview: ¿cómo ha evolucionado el ingreso total entre 2024 y 2025?

**KPIs**

- Ingresos: es la métrica central de la vista. Se acompaña de su variación % frente al año anterior, en rojo si es negativa y en verde si es positiva, para que el directivo vea de inmediato si el negocio crece o cae.
- Ganancia (Ingresos − Costo): muestra cuánto queda después de costos; permite saber si la caída de ingresos también afecta el resultado.
- Margen % (Ganancia / Ingresos): mide la rentabilidad y permite descartar o confirmar un problema de costos.
- Pedidos (conteo de ID_Pedido): mide el volumen y ayuda a distinguir si el ingreso cambia por cantidad de pedidos o por su valor.

**Gráficos**

- Gráfico de tiempo: gráfico de líneas de Ingresos por mes, con la estación en el eje y una línea por año (2024 en gris, 2025 en azul). Permite comparar el mismo mes entre años y ver en qué temporada se concentra la diferencia. El filtro de Año no afecta este gráfico, para mantener siempre visible la comparación.
- Gráfico comparación geográfica: barras horizontales de Ingresos por País, con etiquetas de datos. Con solo tres países, las barras comparan magnitudes con más precisión que un mapa.
- Gráfico comparación clientes/productos: barras horizontales de Ingresos por Segmento_Cliente, con etiquetas de datos. Muestra qué tipo de cliente sostiene el negocio.

**Jerarquía visual**

- Título: arriba a la izquierda, con el filtro de Año a la derecha.
- Fila de tarjetas KPI: debajo del título, todas del mismo tamaño; es lo primero que lee el directivo. La variación en rojo dirige la atención a la complicación.
- Gráfico de líneas: a la izquierda, ocupa cerca de dos tercios del ancho por ser el visual principal.
- Barras por País y por Segmento_Cliente: a la derecha, una sobre otra, como contexto de apoyo.
- Color: azul para el año analizado, gris para el año de comparación y rojo/verde reservados para la variación.
- Títulos y subtítulos: cada gráfico tiene un título que comunica el hallazgo y un subtítulo que señala la complicación, en lugar de describir los ejes.

**Filtros**

- Año (selección única). Además, al hacer clic en un país o segmento se filtran los demás visuales de la página.

### Vista Detalle: ¿por qué han caído los ingresos?

**Gráficos**

- Gráfico comparación estacional contra ingresos: Gráfico de columnas agrupadas - `Ingresos` vs `Estación`, con `Año` como leyenda. Muestra en qué temporada se produce la caída.
- Gráfico comparación segmento contra ingresos: Gráfico de barras agrupadas - `Segmento_Cliente` vs `Ingresos`, con `Año` como leyenda. Es el visual principal de la vista, porque identifica qué tipo de cliente explica la caída.
- Gráfico comparación categoría contra ingresos: Gráfico de columnas agrupadas - `Categoría_Producto` vs `Ingresos`, con `Año` como leyenda. Permite comprobar si la caída depende de un producto en particular.
- Tabla mostrando 6 columnas, agrupada por `País` y `Región`:
  - Conteo `ID_Pedido`
  - Conteo único `ID_Cliente`
  - Suma `Ingresos`
  - Ganancia
  - Margen %
  - Ticket promedio (Ingresos / pedidos)

**Filtros**

- `Fecha_pedido` (rango de fechas)
- `País`
- `Segmento_Cliente`

## 4. Construcción de las vistas

**Construcción de las vistas**

- Vista 1 (Overview): 4 KPIs con la variación anual de ingresos, un gráfico de líneas mensual 2024 vs. 2025 y dos gráficos de barras (país y segmento).
- Vista 2 (Detalle): tres gráficos comparativos por año (segmento, estación y categoría) y una tabla por país y región.

**Medidas creadas (DAX)**

- Ingresos Totales, Costo Total y Ganancia (Ingresos − Costo).
- Margen % (Ganancia / Ingresos).
- Pedidos (conteo de ID_Pedido) y Clientes Únicos (conteo único de ID_Cliente).
- Ticket Promedio (Ingresos / Pedidos).
- Ingresos Año Anterior y Var % Ingresos, para comparar cada año con el anterior.

**Coherencia visual**

- Misma regla de color en ambas vistas: azul para 2025, gris para 2024 y rojo/verde solo para la variación.
- Títulos que comunican el hallazgo y subtítulos que señalan la complicación.
- Columnas de apoyo creadas en Power Query: Año, Mes y Nombre del mes (ordenado por número de mes).

## 5. Narrativa del dashboard (modelo SCQA)

Historia del dashboard con el modelo SCQA (Situación, Complicación, Pregunta y Respuesta).

### Vista General (Overview)

**S (Situación):**
Andes Retail Group opera en Perú, Chile y Colombia. En 2025 vendió 2,67 millones con un margen de 35,1 %. Perú y Chile concentran cerca del 75 % de los ingresos, y los segmentos Estándar y Premium, alrededor del 90 %.

**C (Complicación):**
Los ingresos cayeron 6,8 % frente a 2024 (de 2,86 a 2,67 millones) y la ganancia bajó de 1,01 millones a 936 mil. La caída no es pareja durante el año: se concentra en enero, febrero y diciembre, los meses de verano y de mayor venta. El resto del año se mantiene en niveles similares.

**Q (Pregunta):**
¿Qué explica la caída en la temporada alta: los costos, un país, un tipo de cliente o una categoría de producto?

**A (Respuesta):**
No es un problema de rentabilidad: el margen se mantiene en 35,1 % en ambos años. Es un problema de ventas: se registran 100 pedidos menos (de 2.550 a 2.450) y la pérdida ocurre justo en los meses de mayor demanda. La causa se identifica en la vista Detalle.

---

### Vista Detalle

**S (Situación):**
Los ingresos de 2025 cayeron 0,19 millones, concentrados en verano. El negocio se distribuye en tres segmentos de cliente, cuatro categorías de producto y nueve regiones.

**C (Complicación):**
La caída no es general. Premium es el único segmento que retrocede: pasa de 1,40 a 1,20 millones (−14 %), mientras Estándar se mantiene y Económico crece levemente. Es el segmento más valioso: representa el 30 % de los pedidos y el 47 % de los ingresos.

**Q (Pregunta):**
¿Por qué cae Premium: por un producto, por una región o por un cambio en su comportamiento de compra?

**A (Respuesta):**
Es un cambio en el comportamiento de compra durante la temporada alta. Premium vendió 0,17 millones menos en verano (de 0,61 a 0,44 millones), con caídas en las cuatro categorías y un margen estable cercano al 35 % en todas las regiones, lo que descarta un problema de producto o de rentabilidad. En el año, Premium hizo 71 pedidos menos (de 795 a 724) y su ticket promedio bajó 5,6 % (de 1.757 a 1.659).

Recomendación: recuperar a los clientes Premium con una campaña dirigida antes y durante el verano (diciembre a febrero) y crear un programa de fidelización, ya que casi ningún cliente repite compra (3.821 clientes en 5.000 pedidos).

---

## 6. Mensaje para el equipo (Slack)

📊 **Actualización desempeño comercial**

Equipo, les comparto el resumen del desempeño comercial 2024–2025.

En 2025 vendimos 2,67 millones con un margen estable de 35,1 %. Sin embargo, los ingresos cayeron 6,8 % frente a 2024, unos 0,19 millones menos.

La caída no es general. Casi toda se explica por el segmento Premium, que vendió 0,20 millones menos (−14 %), y ocurre sobre todo en verano (diciembre a febrero), nuestra temporada más fuerte. No es un problema de producto ni de costos: Premium baja en las cuatro categorías y el margen se mantiene en todas las regiones. Este segmento hizo 71 pedidos menos y su ticket promedio bajó 5,6 %.

Acciones recomendadas:
- Lanzar una campaña dirigida a clientes Premium antes del próximo verano.
- Crear un programa de fidelización, porque hoy casi ningún cliente repite compra.
- Priorizar Perú, donde se concentra la mayor parte de la caída (de 1,14 a 1,02 millones).
