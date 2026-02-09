# Cronograma SENA - Lineamientos para Edicion Automatizada

## Proposito
Este proyecto contiene cronogramas mensuales de actividades formativas del SENA en formato HTML.
La IA debe ser capaz de generar/actualizar estos cronogramas manteniendo los estilos institucionales.

## Archivos del Proyecto
- `1_adso_2025.html` — Cronograma mensual de ADSO (Analisis y Desarrollo de Software), ficha 2977396
- `1_tps_2025.html` — Cronograma mensual de TPS, ficha correspondiente
- `.claude/catalogo_adso.txt` — Catalogo de competencias, resultados y evidencias de ADSO
- `.claude/input_cronograma.txt` — Plantilla de entrada que el usuario llena cada mes
- `.claude/ejemplo_entrada.txt` — Ejemplo real de como llenar la plantilla

## Flujo de Trabajo
1. El usuario llena `.claude/input_cronograma.txt` con los datos del nuevo mes
2. Opcionalmente crea un archivo de evidencias separado
3. Le pide a Claude: "Actualiza el cronograma con los datos de input_cronograma.txt"
4. Claude lee el input, genera el HTML y actualiza el archivo correspondiente

---

## REGLAS DE ESTILO INSTITUCIONAL (NO MODIFICAR)

### Colores SENA
- Azul oscuro (headers, col-week): `#04324D`
- Verde SENA (logo, h2): `#39A900`
- Fondo pagina: `#f4f4f4`
- Fondo container: `#ffffff`

### Tipografia
- Font principal: `'Poppins', 'Lato', Arial, sans-serif`
- Google Fonts: `https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap`

### Logo
- El logo es un SVG inline del SENA (NO modificar, copiar tal cual del archivo existente)
- Clase `.logo` con `width: 100px`

---

## ESTRUCTURA HTML DEL CRONOGRAMA

### Encabezado
```html
<div class="header">
    <div class="logo"><!-- SVG del SENA --></div>
    <div class="titles">
        <h1>Cronograma de Actividades - {Mes} {Anio}</h1>
        <h2>{Nombre del Programa}</h2>
    </div>
</div>
```

### Columnas de la Tabla (11 columnas)
| # | Columna | Clase | Ancho |
|---|---------|-------|-------|
| 1 | Semana (vertical) | `col-week` | 2% |
| 2 | Ficha | `col-narrow` | 5% |
| 3 | Fase | `col-narrow` | 5% |
| 4 | Actividad de proyecto | `col-wide` | 17% |
| 5 | Competencia | `col-wide` | 17% |
| 6 | Resultado | `col-wide` | 17% |
| 7 | Evidencia | `col-wide` | 17% |
| 8 | Horas | `col-narrow` | 5% |
| 9 | Fecha inicial | `date-column` | 120px |
| 10 | Fecha final | `date-column` | 120px |
| 11 | Instructor | `col-medium` | 10% |

### Ciclo de Colores por Semana (se repite cada 4 semanas)
| Semana | Border class | Vstripe class |
|--------|-------------|---------------|
| 1 | `border-green` | `vs-green` |
| 2 | `border-blue` | `vs-blue` |
| 3 | `border-cyan` | `vs-cyan` |
| 4 | `border-purple` | `vs-purple` |

### Highlight de Evidencias por Tipo
| Tipo de evidencia | Clase highlight |
|-------------------|----------------|
| Conocimiento | `highlight-duplicate-purple` |
| Desempeno | `highlight-duplicate-yellow` |
| Producto | `highlight-duplicate-green` |

Colores adicionales disponibles: `highlight-duplicate-blue`, `highlight-duplicate-cyan`, `highlight-duplicate-magenta`, `highlight-duplicate-orange`

---

## REGLAS DE GENERACION HTML

### Estructura de una Semana
Cada semana es un grupo de filas con:
1. Primera fila: `<td class="col-week border-{color}" rowspan="{n}">Semana {N}</td>` + datos
2. Filas adicionales: solo datos (sin celda de semana)
3. Despues de cada semana: `<tr class="week-divider"><td colspan="11"></td></tr>`

### Plantilla de Fila
```html
<tr>
    <!-- Solo en primera fila de la semana: -->
    <td class="col-week border-{color}" rowspan="{num_evidencias}">Semana {N}</td>
    <td class="text-center">{ficha}</td>
    <td class="text-center">{fase}</td>
    <td>{actividad}</td>
    <td>{competencia}</td>
    <td>{resultado}</td>
    <td class="vstripe vs-{color} highlight-duplicate-{tipo}">{texto_evidencia}</td>
    <td class="text-center">{horas}</td>
    <td class="text-center vs-purple">{fecha_inicio}</td>
    <td class="text-center">{fecha_fin}</td>
    <td class="text-center">{instructor}</td>
</tr>
```

### Formato de Fechas
- Fecha inicio: `Lunes, DD de {mes} de {anio}` (siempre el lunes de la semana)
- Fecha fin: `Domingo, DD de {mes} de {anio}` (siempre el domingo de la semana)
- La celda de fecha inicio lleva clase adicional `vs-purple`

### Reglas de Horas
- Las horas se distribuyen entre las evidencias de cada semana
- Normalmente: primera evidencia 24h, segunda evidencia 20h (total 44h/semana)
- Si hay 3 evidencias: distribuir proporcionalmente

### Indentacion
- Usar 4 espacios (NO tabs) para indentacion HTML
- Mantener la estructura de indentacion del archivo existente

### El CSS completo y el SVG del logo NO se modifican nunca
- Copiar tal cual del archivo existente
- Solo se modifica el contenido del `<tbody>` y el titulo `<h1>`

---

## INSTRUCCIONES PARA CLAUDE

Cuando el usuario pida actualizar un cronograma:

1. **Leer** el archivo `input_cronograma.txt` (o el texto que proporcione el usuario)
2. **Leer** el archivo HTML actual para conservar el CSS y SVG intactos
3. **Calcular** las fechas de cada semana (lunes a domingo) del mes indicado
4. **Generar** las filas del `<tbody>` siguiendo las reglas de estilo
5. **Actualizar** el titulo `<h1>` con el mes y anio correctos
6. **Escribir** el archivo HTML actualizado

### Validaciones
- Verificar que las fechas sean correctas (lunes=inicio, domingo=fin)
- Verificar el ciclo de colores (green -> blue -> cyan -> purple)
- Verificar que el rowspan coincida con el numero de evidencias por semana
- No dejar semanas sin linea divisoria entre ellas
- La ULTIMA semana NO lleva linea divisoria al final
