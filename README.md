# Análisis del desempeño financiero de Adventure Works con SQL
**Herramientas:** SQL | Excel

**Tipo de proyecto:** Análisis dinanciero | Business Inteligence

**Contexto:** Proyecto académico - TripleTen Data Analytics Bootcamp

## Contexto del negocio
El *director financiero de Adventure Works* necesita tomar decisiones de inversión en marketing para 2018. Para ello, solicita respuesta a dos preguntas clave:

1. ¿Cuánto se está generando por país?
2. ¿Qué tan rentable es cada mercado considerando los gastos de campaña?

Como analista jr., el objetivo fue implementar las estapas del análisis de datos desde las tablas fuente hasta los indicadores financieros clave por país: ingresos, costos, beneficio bruto, margen y ROI.

## Diagrama Entidad Relación
El análisis utilizó un subconjunto del dataset AdventureWorks compuesto por 6 tablas:
```mermaid
erDiagram
  ventas_2017 {
    string numero_pedido
    date fecha_pedido
    int clave_producto FK
    int clave_cliente FK
    int clave_territorio FK
    int cantidad_pedido
  }
  productos {
    int clave_producto PK
    int clave_subcategoria FK
    string sku_producto
    string nombre_producto
    float costo_producto
    float precio_producto
  }
  productos_categorias {
    int clave_subcategoria PK
    string subcategoria_descripcion
    int clave_categoria
    string categoria_descripcion
  }
  clientes {
    int clave_cliente PK
    string nombre
    string apellido
    date fecha_nacimiento
    string estado_civil
    int ingreso_anual
    string nivel_educativo
    string ocupacion
  }
  territorios {
    int clave_territorio PK
    string pais
    string continente
  }
  campanas {
    int clave_territorio FK
    float costo_campana


  }
  ventas_2017 }o--|| productos : "clave_producto"
  ventas_2017 }o--|| clientes : "clave_cliente"
  ventas_2017 }o--|| territorios : "clave_territorio"
  productos }o--|| productos_categorias : "clave_subcategoria"
  territorios ||--o{ campanas : "clave_territorio"
```

## Etapas del análisis
1. **Exploración del esquema** (exploración_datos.sql)

   Se realizó una inspección inicial a las 6 tablas para comprender la estructura de columnas, tipos de datos e identificar las claves de unión entre las tablas.

   **Técnicas utilizadas:** SELECT, FROM, LIMIT

2. **Extracción y limpieza de datos** (exploracion_limpieza_datos.sql)

   Por medio de JOINs de 4 tablas, construí un dataset con fines analíticos, reemplazando valores nulos y calculando métricas por línea de pedido.

   **Técnicas utilizadas:** JOIN, LEFT JOIN, COALESCE

   **Columnas generadas:**
   | Columna | Fórmula |
   | --- | --- |
   |ingreso_total | precio_producto * cantidad_pedido |
   | costo_total | costo_producto * cantidad_pedido |

3. **Indicadores financieros** (indicadores_financieros.sql)

   Se agrupó el dataset por país para calcular los indicadores solicitador por el director financiero

   **Técnicas utilizadas:** SUM, GROUP BY, ORDER BY, NULLIF, casting con ::INTEGER
   **Columnas agregadas**
   | Columna | Fórmula |
   | --- | --- |
   | Beneficio bruto | ingresos - costos |
   | margen_pct | (ingresos - costos) * 100 / ingresos |
   | roi_pct | (ingresos - costos) * 100 / costo_campaña |

4. **Validación de calidad** (validación_resultados)

   Se realizó una comprobación para garantizar que el dataset está integro antes de presentar los resultados

   | Verificación | Resultados |
   | --- | --- |
   | NULLs en campos clave (numero_pedido, clave_producto, clave_territorio) | 0 Registros nulos |
   | Cantidades de pedido inválidas (cantidad_pedido <= 0) | 0 Registros inválidos |
   | Precios de productos negativos (precio_producto < 0) | 0 Registros inválidos |

## Resultados
| País | Ingresos | Costos | Costo de campaña | Beneficio bruto | Margen | ROI |
| --- | --- | --- | --- | --- | --- | --- |
| United States | $3,353,940 | $1,899,471 | $1,920,000 | $1,454,469 | 43.4% | 75.8% |
| Australia | $2,532,003 | $1,474,958 | $2,150,400 | $1,057,045 | 41.7% | 49.2% |
| United Kingdom | $1,189,637 | $681,509 | $2,304,000 | $508,128 | 42.7% | 22.1% |
| Germany | $1,071,460 | $611,295 | $2,265,600 | $460,165 | 43.0% | 20.3% |
| France | $924,317 | $527,797 | $2,208,000 | $396,520 | 42.9% | 18.0% |
| Canada | $710,205 | $392,326 | $1,824,000 | $317,879 | 44.8% | 17.4% |

## Análisis ejecutivo (C-F-I)
### Contexto
Se analizó el dataset de ventas 2017 cruzando transacciones con territorios, productos y campañas de marketing para obtener ingresos, costos directos, costos de campaña, beneficio bruto, margen y ROI por país.
### Hallazgos
1. Estados Unidos lidera con el 34% de los ingresos totales y el segundo costo de campaña más bajo ($1,920,000), lo que explica su ROI superior (75.8%).
2. Reino Unido tiene el costo de campaña más alto ($2,304,000) con ingresos moderados ($1,189,637), resultando en el tercer ROI más bajo (22.1%) - señal de baja eficiencia del gasto en marketing.
3. Canadá registra el ROI más bajo (17.4%) porque su beneficio bruto ($317,879) es bajo en relación a su gasto de campaña ($1,824,000).
### Implicaciones
- **EE.UU.:** Reducir 10% el presupuesto de campaña para optimizar el ROI en 2018, dado que el mercado ya opera con alta eficiencia.
- **Reino Unido:** Implementar un incremento gradual del 10% en inventario combinado con una reducción del 5% en presupuesto de campañas, con objetivo de alcanzar 45% de ROI en 2018.

## Habilidades aplicadas 
- Navegación de esquemas relacionales e identificación de claves de unión
- Escritura de JOINs para combinar múltiples tablas
- Extracción, filtrado y limpieza de datos (COALESCE, NULLIF, CASE WHEN, casting de tipos)
- Cálculo de indicadores financieros: ingresos, costos, beneficio bruto, margen y ROI
- Control de calidad con verificaciones de integridad de datos
- Comunicación de hallazgos con el método Contexto → Hallazgo → Implicación

## Autor
Roberto Barrera García - Analista de Datos Jr. en formación
[LinkedIn]([https://tu-enlace.com](https://www.linkedin.com/in/robertobarreragarcia/?lipi=urn%3Ali%3Apage%3Ad_flagship3_profile_view_base_contact_details%3Bfd1G%2Bg6JS7S3%2FVuHX5WGPA%3D%3D)) | [GitHub](https://github.com/RobertoBG1111)
