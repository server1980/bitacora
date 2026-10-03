# Conocimiento y experiencias GCI: memoria de trabajo con Claude

> Este archivo guarda lo aprendido y decidido en las sesiones con Claude, para continuar en cualquier
> PC o sesión nueva. **Al iniciar una sesión nueva, pídele a Claude que lea este archivo.**
> Actualízalo al final de cada sesión con lo nuevo (decisiones, datos, pendientes).

Última actualización: 2026-10-03

---

## 1. Quién y qué

- Empresa: **GCI** (logos en `LOGO_GCI.png` / `LOGO_GCI_W.png`).
- Repo: `server1980/bitacora`. Contiene `bitacora.html` (Bitácora Informativa GCI).
- Trabajo principal: obra civil y mantenimiento en instalaciones industriales con cargo **SSPA**
  (seguridad, salud y protección ambiental, tipo PEMEX), presupuestos y precios unitarios (APU)
  en **Opus/Neodata**, y control de avance de pendientes en campo.
- Idioma de trabajo: español.

---

## 1.1 Cómo trabaja el usuario (preferencias)

- Escribe rápido y coloquial, a veces con faltas. Hay que interpretar la intención, no corregirlo.
- Manda **capturas de pantalla de Opus** y **fotos de notas manuscritas de campo**. Hay que transcribirlas
  con cuidado y **marcar las lecturas dudosas** en lugar de adivinar.
- Prefiere entregables en **Excel** (resúmenes, controles de avance, matrices).
- Quiere respuestas con números concretos y tablas listas para capturar en Opus.
- **No subir ni hacer commit de nada sin verificar.** Si pide parar, se para y se espera. Si pide descartar,
  se borra.
- Le interesa aprender: explicar el porqué de cada ajuste (rendimiento, equipo, unidad) con referencias.

---

## 1.2 Uso de Opus (software de precios unitarios)

Opus (Ecosoft) es donde vive el presupuesto real. Claude **no opera Opus directamente**. El flujo es:
1. El usuario manda una captura de la matriz o del catálogo, o exporta el reporte de Opus a Excel o PDF.
2. Claude analiza (rendimientos, equipo, unidades, costos horarios, consistencia) y compara contra
   referencias (tabuladores SICT o CDMX, análisis de terceros, rendimientos típicos).
3. Claude devuelve **una tabla lista para capturar en Opus**: clave, cantidad, rendimiento y costo, con el
   importe esperado para que el usuario verifique que cuadra.

Lo que se ve en las capturas de Opus del usuario:
- **Pantalla del concepto:** Tipo, Clave (ej. 1.20), Descripción, Unidad, Cantidad, Precio unitario, Total.
- **Pestañas de la matriz con su subtotal:** Todos (= costo directo), Materiales, Mano de obra, Herramientas,
  Equipos, Auxiliares, Matrices, Fletes, Trabajos.
- **Columnas del detalle:** C, Clave, Descripción, Unidad, Cantidad, Rendimiento, Costo unitario, Total,
  Define rendimiento.
  - Con **"Define rendimiento"** marcado, la cantidad = 1 / rendimiento. Se captura el rendimiento y Opus
    calcula la cantidad.
  - En el **cargo SSPA** viene desmarcado: la cantidad se captura directa (jornadas-hombre).
  - La columna **C** muestra "H" en equipos y "+" en el cargo SSPA. Su significado exacto está
    **por confirmar con el usuario**.
- **Herramienta menor:** unidad "(%)mo", cantidad 0.03 (3%). Su costo unitario es la suma de la mano de obra
  (incluye el SSPA).
- **Equipos:** se cobran por hora; las horas van ligadas a la cuadrilla (6.7857 hr efectivas por jornada).
- **Catálogo de costos horarios:** cada equipo tiene dos registros: costo horario ("Hora") y costo de
  adquisición ("-VA", "pieza"). Ej.: EQP140 generador 5 kW = $67.48/hr y EQP140-VA = $38,000.
  Bases de datos que aparecen: "GCI MAQUINARIA", "OBRA MECÁNICA", "OBRA INDUSTRIAL".
- **Prefijos de clave GCI:** GCI-MAT (materiales), GCI-MO (mano de obra), GCI-HER (herramienta),
  GCI-EQ (equipo), EQP### (maquinaria del catálogo de costos horarios).
- **Salarios con FASAR** (ya integrados en el costo unitario): peón $1,050.25/jor, albañil $1,284.87/jor,
  cabo de oficiales $2,227.34/jor. Cargo SSPA $180.89 por jornada-hombre (vigía contraincendio 0.05,
  supervisor de seguridad 0.04, etc.).

Checklist para revisar cualquier matriz de Opus:
1. ¿La **unidad** es la correcta para cómo se mide en obra? (ej. escarificado superficial: m², no m³).
2. ¿El **rendimiento** está dentro del rango de referencia para las condiciones reales (acceso,
   profundidad, altura, permisos SSPA)?
3. ¿El **equipo** es del tamaño adecuado? (caso real: una planta de soldar de 85 hp para un rotomartillo
   de 115 V).
4. ¿Las **horas de equipo** son consistentes con las jornadas de la cuadrilla?
5. ¿El **cargo SSPA** = jornadas-hombre totales de la cuadrilla?
6. ¿Los **costos horarios** son coherentes entre equipos de valor de adquisición parecido?
7. ¿Hay insumos que no aplican o que faltan (andamio, agua, acarreos, limpieza)?
8. Comparar el PU contra una referencia oficial y explicar la diferencia.

---

## 2. Proyecto: matriz de precios unitarios de puentes (base SICT)

Plan completo en `PROYECTO_MATRIZ_PUENTES.md` (fases, estructura de carpetas, esquema de datos y prompts).
Cópialo como `CLAUDE.md` a la carpeta local donde estén los PDF y Excel de fuentes.

Fuentes clave:
- Tabuladores SICT (DGST): costo directo de construcción 2025 y 2026 (`TCD_Cons-2026.pdf`), paramétricos 2025
  (puentes con claros de 15, 25, 30 y 40 m; concreto o acero; 2, 4 o 6 carriles) y servicios 2025.
  Índice: https://micrs.sct.gob.mx/index.php/infraestructura/direccion-general-de-servicios-tecnicos/tabulador
- Normativa SICT/IMT: https://normas.imt.mx/libros/CTR. Parte 1.02 Estructuras: 003 concreto hidráulico,
  004 acero de refuerzo, 006 concreto reforzado, 007 presforzado. Usar la cláusula de medición y base de pago.
- SIPUMEX (inventario de puentes federales): no está en datos abiertos; pedirlo por transparencia.
- Catálogos reales: Puente Los Borregos SLP 2023, puente peatonal SLP 2024, Tabulador CDMX 2025.
- **Los tabuladores SICT son a costo directo**: compararlos contra el costo directo, no contra el PU final.

Estado:
- La plantilla de Excel de la matriz de puentes **se descartó por decisión del usuario**. Queda pendiente
  para cuando se retome.
- El entorno de Claude en la nube no puede descargar de `*.gob.mx` ni de `normas.imt.mx` (bloqueo de red).
  En una PC local con Claude Code sí se puede, o el usuario los descarga y los pone en la carpeta.

Búsquedas que funcionan (un tipo de archivo a la vez, sin comillas):
```
precios unitarios puente filetype:xls
catálogo de conceptos puente filetype:xlsx
catálogo de conceptos puente site:gob.mx
análisis de precios unitarios puente site:gob.mx filetype:pdf
```

---

## 3. APU revisado: escarificado manual en zapatas y losas de cimentación

Concepto 1.20 del presupuesto (total del presupuesto $531,940,216.36): *"Escarificado por medios manuales en
zapatas y losas de cimentación de concreto reforzado"*, unidad **m³**, cantidad **11.17**.

Datos de campo confirmados por el usuario:
- Profundidad del escarificado: **10 mm**, así que **1 m³ = 100 m²** de superficie.
- Zapatas de **4 m de profundidad**: el **andamio sí se justifica**.
- La planta de soldar diésel de 85 hp y 2,500 A (GCI-EQ-0041, $431.66/hr) **era excesiva**. Se cambia por
  **EQP140, generador 5 kW Powerarc 5000, $67.48/hr** (valor de adquisición $38,000, clave EQP140-VA).

Hallazgos:
- El rendimiento original de 0.476 m³/jor equivale a 47.6 m²/jor. Es **optimista**: la referencia es de
  18 a 45 m²/jor con rotomartillo y de 8 a 12 m²/jor con cincel, y aquí las condiciones son más difíciles
  (4 m de profundidad, andamio, caras verticales, SSPA).
- **Rendimiento recomendado: 35 m²/jor (0.35 m³/jor)**. Conservador: 30 m²/jor.
- Las horas de equipo van ligadas a la cuadrilla: 6.7857 hr efectivas por jornada (cantidad = 6.7857 / rendimiento).
- SSPA = 2.1 jornadas-hombre por jornada de cuadrilla.
- Factor de sobrecosto usado en el APU: 1.0268 (PU / CD).

Matriz final recomendada (35 m²/jor):

| Clave | Insumo | Unidad | Cantidad | Rendimiento | Costo unitario | Importe |
|---|---|---|---|---|---|---|
| GCI-MAT-AGU-0001 | Agua | m³ | 0.300000 | 3.333333 | 256.05 | 76.82 |
| GCI-MO-0025 | Peón | Jor | 2.857143 | 0.350000 | 1,050.25 | 3,000.71 |
| GCI-MO-0026 | Albañil | Jor | 2.857143 | 0.350000 | 1,284.87 | 3,671.06 |
| GCI-MO-0037 | Cabo de oficiales | Jor | 0.285714 | 3.500000 | 2,227.34 | 636.38 |
| GCI-HER-0009 | Herramienta menor | (%)mo | 0.03 | | 8,393.49 | 251.80 |
| EQP140 | Generador 5 kW Powerarc 5000 | hr | 19.387755 | 0.051579 | 67.48 | 1,308.29 |
| GCI-EQ-0044 | Rotomartillo Makita HM1800 | hr | 19.387755 | 0.051579 | 135.51 | 2,627.23 |
| GCI-EQ-0021 | Andamio de 6 m | hr | 19.387755 | 0.051579 | 2.47 | 47.89 |
| GCI-MO-0148 | Cargo SSPA de campo | Jor | 6.000000 | 0.166667 | 180.89 | 1,085.34 |
| | **Costo directo** | | | | | **$12,705.53/m³** |
| | **Precio unitario** | | | | | **$13,045.42/m³ (≈ $130.45/m²)** |

Comparativo: original $14,937.71/m³ ($166,854.22 en total); recomendado $13,045.42/m³ ($145,717.34), 12.7% menos.

Pendientes de este APU:
- [ ] Revisar el costo horario del rotomartillo ($135.51/hr). Es el doble que el generador, aunque cuestan
  algo parecido al comprarlos.
- [ ] La HM1800 es un demoledor pesado (~30 kg). Para 10 mm en caras verticales, un cincelador SDS-Max de
  5 a 10 kg es más adecuado.
- [ ] Recomendación: cobrar por **m²** en lugar de m³. Si las bases fijan m³, poner "espesor promedio 10 mm".

Referencias: demolición manual de concreto armado de 0.50 a 0.65 m³/jor. Tabulador CDMX marzo 2024:
demolición manual de cimentación de concreto armado $1,184.54/m³.

---

## 4. Control de pendientes de obra

Archivo: `obra/Pendientes_por_area.xlsx`. Hojas: Resumen y Pendientes. Solo se captura "% Falta";
el avance es 1 − falta.

Corte del 2026-10-02, tomado de las notas manuscritas:

| Área | Actividades | Avance |
|---|---|---|
| Trampas | 3 | 0% |
| Esferas 301 A y B | 9 | 14% |
| TU-45 | 12 | 40% |
| **Total** | **24** | **26%** |

Lecturas por verificar: sigla "CVT" (tapa), "Sello en pisos" 60% (¿u 80%?), "anillo de cimentación".
Nota de campo: en Esferas, según Diego Juárez la plantilla no lleva, pero hay que considerarla para colocar
bien los dados. Los 4 dados adicionales son para Luis Guerrero.

---

## 5. Herramientas y conexiones

- **Gmail y Google Drive** están conectados a la cuenta de Claude. Solo se usan cuando el usuario lo pide.
- **Hotmail/Outlook personal:** no hay conector útil. El de Microsoft 365 es para cuentas de empresa y
  solo lee. Alternativa: reenviar a Gmail.
- El entorno de Claude en la nube no tiene LibreOffice funcional: los Excel se verifican con Python y
  se marcan para recalcular al abrir.

---

## 6. Cómo continuar en otra PC

1. En la otra PC: `git clone https://github.com/server1980/bitacora.git` (o `git pull` si ya existe) y
   `git checkout claude/inspiring-brown-q6711m`.
2. Abrir Claude Code en esa carpeta. `CLAUDE.md` le indica leer este archivo.
3. Al terminar cada sesión, pedir: *"actualiza CONOCIMIENTO_GCI.md con lo de hoy y haz commit y push"*.
4. Antes de empezar en cualquier PC: `git pull` para traer lo último.
5. Solo se comparte **conocimiento** (este archivo y los planes). Los archivos pesados (PDF, bases de Opus)
   se quedan en cada PC.
6. En la PC con Opus: exportar los reportes de Opus (matrices, catálogo, insumos) a Excel en una carpeta
   local y pedirle a Claude Code que los lea desde ahí.
