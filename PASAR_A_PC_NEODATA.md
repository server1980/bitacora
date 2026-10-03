# Pasar la conversación a la PC con Neodata

> Para continuar en local la sesión del 2026-10-03. Abre Claude Code en la carpeta del repo en la PC
> donde está instalado Neodata y pega el **prompt inicial** de la sección 3.

## 1. Qué se habló

- El usuario quiere que Claude trabaje con **Neodata** como se hace con Opus (ver `CONOCIMIENTO_GCI.md`,
  sección 1.2), e intentar **conexión directa por SQL**.
- La sesión en la nube **no puede** hacerlo: corre en un contenedor Linux, sin acceso a la PC ni a la red
  local del usuario. Por eso se pasa la tarea a Claude Code **local** en la PC con Neodata.
- Neodata es de Windows. No se sabe todavía qué motor de base de datos usa la versión instalada:
  **hay que verificarlo en la PC, no asumirlo.**

## 2. Pasos en la PC

1. Instalar Claude Code, luego:
   ```
   git clone https://github.com/server1980/bitacora.git   (o git pull si ya existe)
   cd bitacora
   git checkout claude/inspiring-brown-q6711m
   ```
2. Abrir Claude Code en esa carpeta. `CLAUDE.md` le indica leer `CONOCIMIENTO_GCI.md`.
3. **Respaldar las obras de Neodata** antes de cualquier prueba.
4. Pegar el prompt de la sección 3.

## 3. Prompt inicial (copiar y pegar)

```
Lee CONOCIMIENTO_GCI.md y PASAR_A_PC_NEODATA.md. Quiero trabajar con Neodata instalado en esta PC igual
que lo hacemos con Opus.

Fase 1, SOLO LECTURA, no modifiques nada:
1. Encuentra la carpeta de instalación de Neodata, la versión, y dónde guarda las obras/catálogos.
2. Lista los servicios y motores de base de datos activos (SQL Server, Firebird, Access/.mdb, etc.) y qué
   archivos o bases usa Neodata.
3. Dime qué rutas de acceso existen (ODBC, sqlcmd, pyodbc, archivos exportables) y cuál recomiendas.
4. Si hay una base accesible, conéctate en modo solo lectura y lista tablas y columnas relacionadas con
   conceptos, matrices de precios unitarios e insumos. Muéstrame el esquema antes de consultar datos.
5. Prueba con una consulta de un solo concepto y compárala con lo que se ve en pantalla en Neodata.

Reglas: español; no inventar precios; citar la fuente de cada dato; marcar lecturas dudosas; no hacer
commit ni push sin que yo lo verifique; no escribir en la base de Neodata sin que yo lo autorice.
```

## 4. Rutas posibles según lo que se encuentre

| Caso | Qué hacer |
|---|---|
| Motor accesible (ej. SQL Server) | Conexión **solo lectura**; consultar conceptos, matrices e insumos |
| Archivos propietarios | Exportar de Neodata a Excel o PDF y que Claude lea los reportes |
| Escritura de datos | No escribir directo a la base. Claude devuelve la **tabla lista para capturar** (clave, cantidad, rendimiento, costo e importe esperado) |

## 5. Al terminar la sesión local

Pedir: *"actualiza CONOCIMIENTO_GCI.md con lo de hoy (versión de Neodata, motor de base de datos, ruta
de acceso que funcionó) y haz commit y push"*.

## 6. Pendientes abiertos de otras líneas

- APU 1.20: revisar costo horario del rotomartillo, cambiar HM1800 por cincelador SDS-Max, cobrar por m².
- Obra: verificar "CVT", "Sello en pisos" (60% u 80%) y "anillo de cimentación".
- Puentes: plantilla de Excel descartada, pendiente de retomar.
