# Proyecto: Matriz de Precios Unitarios y Presupuesto de Puentes (base SICT)

> **Cómo usar este archivo:** cópialo a la carpeta donde tienes los archivos (PDF, Excel, etc.)
> y renómbralo a `CLAUDE.md`. Claude Code lo lee automáticamente al abrir esa carpeta
> y sabrá qué queremos hacer, cómo organizar todo y en qué orden trabajar.

---

## 1. Objetivo

Construir una **base de datos de matrices de precios unitarios (APU) para puentes carreteros**
y un **generador de presupuestos**, usando como referencia oficial los tabuladores de la
**SICT (DGST)** y la **Normativa SICT (IMT)**, complementado con catálogos de conceptos y
análisis de licitaciones públicas.

Entregables finales:
1. `salida/matriz_puentes.xlsx`: libro de Excel con insumos, básicos, matrices (APU),
   catálogo de conceptos de puente, presupuesto y comparativo contra el tabulador SICT.
2. `salida/conceptos_sict_puentes.csv`: todos los conceptos de puentes extraídos de los
   tabuladores SICT (clave, descripción, unidad, precio, año, fuente, página).
3. `salida/reporte_fuentes.md`: qué archivo se procesó, qué se extrajo, qué falló.
4. (Opcional, fase 2) módulo de presupuesto de puentes dentro de `bitacora.html` (Bitácora GCI).

---

## 2. Estructura de carpetas esperada

```
matriz-puentes/
├── CLAUDE.md                  ← este archivo
├── fuentes/                   ← TODO lo descargado, sin modificar (solo lectura)
│   ├── sict/                  ← tabuladores DGST (PDF)
│   │   ├── TCD_Cons-2026.pdf
│   │   ├── TABULADOR_COSTO_DIRECTO_CONSTRUCCION-2025.pdf
│   │   ├── TABULADOR_COSTOS_PARAMETRICOS-2025.pdf
│   │   ├── TABULADOR_COSTO_DIRECTO_SERVICIOS-2025.pdf
│   │   └── TABULADOR_SICT_CONSTRUCCION_2024.pdf
│   ├── normas_imt/            ← N-CTR-CAR-1-01-xxx, N-CTR-CAR-1-02-xxx, etc. (PDF)
│   ├── catalogos/             ← catálogos de conceptos de licitaciones (PDF/XLS/XLSX)
│   ├── apu_ejemplos/          ← análisis de precios unitarios de terceros (PDF/XLS)
│   └── otros/                 ← tabuladores estatales, CDMX, BIMSA, etc.
├── trabajo/                   ← intermedios generados (CSV/JSON por archivo)
├── scripts/                   ← código Python de extracción y cálculo
└── salida/                    ← entregables finales
```

**Regla:** nunca modificar ni borrar nada en `fuentes/`. Todo lo generado va en `trabajo/` o `salida/`.

---

## 3. Fuentes (dónde descargar)

### SICT – Dirección General de Servicios Técnicos (prioridad 1)
- Índice: https://micrs.sct.gob.mx/index.php/infraestructura/direccion-general-de-servicios-tecnicos/tabulador
- Costo directo construcción 2026: https://micrs.sct.gob.mx/images/DireccionesGrales/DGST/Tabulador/TCD_Cons-2026.pdf
- Costo directo construcción 2025: https://micrs.sct.gob.mx/images/DireccionesGrales/DGST/Tabulador/TABULADOR_COSTO_DIRECTO_CONSTRUCCION-2025.pdf
- Costos paramétricos 2025: https://micrs.sct.gob.mx/images/DireccionesGrales/DGST/Tabulador/TABULADOR_COSTOS_PARAMETRICOS-2025.pdf
- Servicios relacionados 2025: https://micrs.sct.gob.mx/images/DireccionesGrales/DGST/Tabulador/TABULADOR_COSTO_DIRECTO_SERVICIOS-2025.pdf
- Construcción 2024: https://micrs.sct.gob.mx/images/DireccionesGrales/DGST/Tabulador/TABULADOR_SICT_CONSTRUCCI%C3%93N_2024__V1_.pdf

### Normativa SICT (IMT) (prioridad 1)
- Libro CTR Construcción: https://normas.imt.mx/libros/CTR
- Clave para puentes: Tema CAR, Parte 1.02 Estructuras (concreto hidráulico 003, acero de refuerzo 004,
  concreto reforzado 006, presforzado 007, y demás), Parte 1.01 Terracerías (excavación para estructuras).
- De cada norma interesa la cláusula **"Medición" y "Base de pago"**: define la unidad y qué incluye el precio.

### Catálogos y APU de licitaciones (prioridad 2)
- Puente Los Borregos, SLP 2023: https://sitio.sanluis.gob.mx/pagAyuntamientoCMS/storage/Licitaciones/LO-EST-245800030-46-2023/Anexos/LO-EST-245800030-46-2023_CATALOGO%20DE%20CONCEPTOS%20Puente%20Los%20Borregos.pdf
- Puente peatonal, SLP 2024: https://sitio.sanluis.gob.mx/pagAyuntamientoCMS/storage/Licitaciones/LO-EST-245800030-77-2024/Anexos/LO-EST-245800030-77-2024_CATALOGO%20DE%20CONCEPTOS.pdf
- Catálogo puente vehicular (xlsx): https://documentos.arq.com.mx/Detalles/257514.html
- Tabulador General de Precios Unitarios CDMX 2025: https://www.obras.cdmx.gob.mx/
- Licitaciones SICT: https://micrs.sct.gob.mx/index.php/direccion-general-de-desarrollo-carretero/licitaciones

### Búsquedas útiles en Google/Bing (un tipo de archivo a la vez)
```
precios unitarios puente filetype:xls
catálogo de conceptos puente filetype:xlsx
catálogo de conceptos puente site:gob.mx
análisis de precios unitarios puente site:gob.mx filetype:pdf
tarjetas de precios unitarios puente
matriz precio unitario trabe AASHTO
```

---

## 4. Plan de trabajo por fases (orden para Claude Code)

### Fase 0: Inventario
- Recorrer `fuentes/` y generar `trabajo/inventario.csv` con: ruta, tipo (pdf/xls/xlsx/doc),
  páginas u hojas, tamaño, si el PDF tiene texto o es escaneado (necesita OCR), año detectado, categoría.
- No procesar nada todavía; solo reportar qué hay.

### Fase 1: Extracción de tabuladores SICT
- Usar `pdfplumber` (tablas) y, si falla, `camelot` o `tabula`. PDFs escaneados: `ocrmypdf` + `pytesseract` (idioma `spa`).
- Filtrar los capítulos de **estructuras / puentes / obras de drenaje mayores**.
- Por cada concepto guardar: `clave, descripcion, unidad, precio_cd, anio, zona (si aplica), norma_ref, archivo, pagina`.
- Un CSV por archivo en `trabajo/sict/`, luego unir en `salida/conceptos_sict_puentes.csv`.
- **Validar:** contar conceptos por capítulo, revisar precios vacíos o no numéricos, que las unidades sean válidas
  (m, m², m³, kg, t, pza, dm³, lote).

### Fase 2: Paramétricos
- Extraer del tabulador paramétrico la tabla de puentes: tipo (concreto/acero), claro (15/25/30/40 m),
  carriles (2/4/6), costo por m² de tablero o por m lineal. Guardar en `trabajo/parametricos_puentes.csv`.

### Fase 3: Catálogos y APU de terceros
- Leer los catálogos de conceptos (PDF/XLS) y normalizarlos al mismo esquema.
- Leer APU de terceros: extraer insumos (materiales, mano de obra, equipo), cantidades y rendimientos.
- Marcar la fuente y el año; **no mezclar precios de años distintos sin actualizarlos** (usar INPC/índices INEGI).

### Fase 4: Unificación y catálogo tipo de puente
- Crear un catálogo maestro de conceptos de puente agrupado por partidas:
  1. **Preliminares:** trazo, desmonte, excavación para estructuras, relleno.
  2. **Cimentación:** pilotes colados en sitio, pilas de cimentación, zapatas, concreto, acero de refuerzo.
  3. **Subestructura:** caballetes, pilas, columnas, cabezales, topes sísmicos, bancos de apoyo.
  4. **Superestructura:** trabes presforzadas (AASHTO, Nebraska, cajón), montaje, losa, diafragmas,
     apoyos de neopreno, presfuerzo.
  5. **Complementarias:** parapetos, guarniciones, banquetas, juntas de dilatación, drenes, losa de acceso,
     carpeta, señalamiento, pruebas de carga.
- Relacionar cada concepto con: clave SICT (si existe), norma N-CTR, unidad, precio tabulador.

### Fase 5: Motor de APU (cálculo)
Fórmula (Reglamento de la LOPSRM; verificar la versión vigente):
```
CD  = Materiales + Mano de obra + Equipo + Básicos/Auxiliares
MO  = (Salario base × FASAR) / Rendimiento
EQ  = Costo horario / Rendimiento
PU  = CD × (1 + %Ind) × (1 + %Fin) × (1 + %Util) × (1 + %Cargos adicionales)
```
- Tablas: `insumos`, `mano_obra` (con FASAR), `equipo` (costo horario), `basicos`
  (concretos por f'c, cuadrillas), `matrices` (concepto → insumos + cantidades).
- Parámetros editables: % indirectos, % financiamiento, % utilidad, cargos adicionales (5 al millar, etc.), zona.

### Fase 6: Excel de salida (`salida/matriz_puentes.xlsx`)
Hojas:
1. `Parametros`: porcentajes, zona, fecha base, salario mínimo, FASAR.
2. `Insumos`: materiales, mano de obra, equipo.
3. `Basicos`: concretos, morteros, cuadrillas.
4. `Matrices`: un APU por concepto, con fórmulas vivas (no valores pegados).
5. `Catalogo_Puente`: conceptos por partida con cantidad, PU y total.
6. `Presupuesto`: resumen por partida, IVA y total.
7. `Comparativo_SICT`: PU propio contra tabulador SICT, diferencia %, alerta si pasa de ±15 %.
8. `Parametrico`: costo del puente por m² de tablero contra tabulador paramétrico SICT.
9. `Fuentes`: de dónde salió cada dato (archivo, página, año).

### Fase 7 (opcional): Integración con Bitácora GCI
- Agregar a `bitacora.html` una sección "Presupuesto de puentes" que cargue el catálogo (JSON) y
  permita capturar cantidades y generar el presupuesto.

---

## 5. Esquema de datos (columnas estándar)

`conceptos` (CSV/Excel):
| campo | ejemplo |
|---|---|
| clave | (clave del tabulador SICT o propia, ej. PTE-SUP-001) |
| partida | Superestructura |
| descripcion | Concreto hidráulico f'c=250 kg/cm² en losa, incluye… |
| unidad | m³ |
| norma_ref | N-CTR-CAR-1-02-003 |
| precio_cd | (número) |
| anio | 2026 |
| fuente | TCD_Cons-2026.pdf |
| pagina | (número) |
| notas | |

`matrices` (detalle del APU):
`clave_concepto, tipo_insumo (MAT/MO/EQ/BAS), clave_insumo, descripcion, unidad, cantidad, precio, importe`

---

## 6. Reglas para Claude Code

- Trabajar **en español**; nombres de columnas en minúsculas y sin acentos.
- Python 3 con `pdfplumber`, `pandas`, `openpyxl` (y `camelot`/`ocrmypdf` solo si hace falta).
  Crear `requirements.txt`.
- Procesar **por lotes** (son muchos archivos): primero 2 o 3 de prueba, mostrar resultado, luego todos.
- Guardar siempre la **trazabilidad**: archivo y página de cada precio.
- **No inventar precios.** Si un dato no se pudo extraer, dejarlo vacío y anotarlo en `reporte_fuentes.md`.
- No mezclar costo directo con precio unitario final: el tabulador SICT es a **costo directo**.
- Las fórmulas del Excel deben quedar vivas (que se recalculen al cambiar parámetros).
- Al terminar cada fase: resumen corto de lo hecho, conteos y problemas encontrados.

---

## 7. Prompts sugeridos (copiar y pegar en Claude Code)

1. `Lee CLAUDE.md y haz la Fase 0: inventario de todo lo que hay en fuentes/.`
2. `Haz la Fase 1 solo con TCD_Cons-2026.pdf: extrae los conceptos de estructuras/puentes y muéstrame 20 filas de ejemplo.`
3. `Se ve bien, aplica la Fase 1 a todos los tabuladores SICT y genera conceptos_sict_puentes.csv.`
4. `Haz la Fase 2 (paramétricos de puentes).`
5. `Haz las Fases 3 y 4: normaliza los catálogos y arma el catálogo maestro de puente por partidas.`
6. `Haz las Fases 5 y 6: genera matriz_puentes.xlsx con fórmulas vivas y el comparativo contra SICT.`

---

## 8. Pendientes / dudas por resolver

- [ ] ¿Qué zona o estado se usará para precios de materiales y salarios?
- [ ] ¿Porcentajes de indirectos, financiamiento y utilidad de GCI?
- [ ] ¿Tipo de puente a presupuestar primero? (claro, número de carriles, concreto o acero)
- [ ] ¿Se usará Opus/Neodata o solo Excel?
- [ ] Confirmar la versión vigente del Reglamento de la LOPSRM para la integración del PU.
