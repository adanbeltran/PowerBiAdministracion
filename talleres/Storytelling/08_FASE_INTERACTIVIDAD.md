# Fase 8. Interactividad y navegación

[← Fase anterior: Logística](07_FASE_LOGISTICA.md) | [Inicio](00_INICIO.md) | [Siguiente: Acción y seguimiento →](09_FASE_ACCION.md)

## Objetivo

Incorporar navegación, filtros e interacciones que permitan explorar el informe sin perder la secuencia narrativa.

Al finalizar esta fase, el informe deberá permitir:

- desplazarse entre los grandes temas mediante páginas;
- cambiar entre vistas de una misma página mediante marcadores;
- conservar filtros relevantes entre páginas;
- consultar información complementaria mediante tooltips;
- profundizar en un producto mediante drillthrough;
- explorar jerarquías mediante drill down y drill up.

---

## 1. Diferencia entre páginas, marcadores y vistas

### Página

Una página es un lienzo independiente del informe. Debe utilizarse para separar temas o preguntas de negocio diferentes.

En este taller, las páginas principales son:

1. Inicio.
2. Panorama ejecutivo.
3. Rentabilidad.
4. Logística.
5. Acción y seguimiento.
6. Detalle de producto.
7. Tooltip de producto, página oculta.

### Marcador

Un marcador guarda un estado de una página o del informe. Puede conservar:

- filtros y segmentadores;
- selecciones;
- nivel de drill;
- ordenamiento;
- visibilidad de objetos;
- página activa.

Un marcador no necesita corresponder a una página diferente.

### Vista

En este taller, una vista es un estado dentro de una página. Puede construirse mediante marcadores que muestran u ocultan grupos de objetos.

### Regla de diseño

> Use páginas para separar grandes temas y marcadores para cambiar estados dentro de una misma página.

---

## 2. Arquitectura de navegación recomendada

La navegación principal del taller será:

```text
Inicio → Panorama → Rentabilidad → Logística → Acción
```

La página `Detalle producto` se abrirá mediante drillthrough.

La página `Tooltip producto` permanecerá oculta.

Dentro de algunas páginas se utilizarán marcadores locales:

```text
Rentabilidad
├── Resumen
├── Diagnóstico
└── Recomendación
```

```text
Logística
├── Resumen
└── Transportadoras
```

También se utilizarán marcadores auxiliares para:

- abrir el panel de filtros;
- cerrar el panel de filtros;
- mostrar u ocultar explicaciones.

---

# 3. Navegador de páginas

## Propósito

Permitir que el usuario se desplace entre los grandes temas del informe.

## Páginas que debe incluir

- Inicio.
- Panorama.
- Rentabilidad.
- Logística.
- Acción.

No incluya en la navegación principal:

- Detalle producto.
- Tooltip producto.

Estas páginas cumplen funciones auxiliares.

## Procedimiento

1. Seleccione **Insertar**.
2. Seleccione **Botones**.
3. Seleccione **Navegador**.
4. Elija **Navegador de páginas**.
5. Ubíquelo en la misma posición en todas las páginas.
6. Configure:
   - estado predeterminado;
   - estado al pasar el cursor;
   - estado seleccionado;
   - tipografía;
   - tamaño;
   - separación;
   - orientación.

## Criterio de calidad

El usuario debe identificar en qué página se encuentra y cuál es el siguiente paso del recorrido.

## Error que debe evitarse

Utilizar marcadores para reemplazar páginas cuando cada sección tiene una pregunta y un conjunto de visualizaciones claramente diferentes.

---

# 4. Segmentadores sincronizados

## Propósito

Mantener un contexto de análisis coherente entre páginas.

## Segmentadores que deben sincronizarse

- Región.
- Canal.
- Segmento.
- Periodo, cuando corresponda.

## Procedimiento

1. Cree el segmentador en la página `Panorama`.
2. Selecciónelo.
3. Abra **Vista → Sincronizar segmentadores**.
4. Active la sincronización en:
   - Panorama;
   - Rentabilidad;
   - Logística;
   - Acción.
5. Decida en qué páginas debe ser visible.

## Ejemplo

Cuando el usuario selecciona:

```text
Región: Norte
Canal: E-commerce
```

las páginas Rentabilidad, Logística y Acción deben conservar ese contexto.

## Precaución

No sincronice un segmentador cuando el filtro no tenga sentido en todas las páginas.

---

# 5. Marcadores narrativos dentro de la página Rentabilidad

## Propósito

Mostrar diferentes niveles de profundidad sin crear páginas adicionales.

Los marcadores serán:

1. `Rentabilidad_Resumen`.
2. `Rentabilidad_Diagnostico`.
3. `Rentabilidad_Recomendacion`.

## 5.1. Preparar el panel Selección

1. Abra **Vista → Selección**.
2. Cambie los nombres genéricos de los objetos.
3. Agrupe los objetos relacionados.

Estructura sugerida:

```text
Grupo_Resumen
├── Tarjetas
├── Margen por región
└── Utilidad por segmento

Grupo_Diagnostico
├── Dispersión descuento-margen
├── Matriz de diagnóstico
├── Árbol de descomposición
└── Texto del hallazgo

Grupo_Recomendacion
├── Matriz de acciones
├── Indicadores de seguimiento
└── Texto de recomendación
```

## 5.2. Marcador `Rentabilidad_Resumen`

Deje visible:

- Grupo_Resumen.

Oculte:

- Grupo_Diagnostico.
- Grupo_Recomendacion.

Configuración recomendada:

| Opción | Estado |
|---|---:|
| Datos | No |
| Mostrar | Sí |
| Página actual | Sí |
| Visuales seleccionados | Sí |

### Justificación

El marcador debe cambiar la visualización sin borrar los filtros seleccionados por el usuario.

## 5.3. Marcador `Rentabilidad_Diagnostico`

Deje visible:

- Grupo_Diagnostico.

Puede conservar parcialmente:

- algunas tarjetas de Grupo_Resumen.

Oculte:

- Grupo_Recomendacion.

Configuración recomendada:

| Opción | Estado |
|---|---:|
| Datos | No |
| Mostrar | Sí |
| Página actual | Sí |
| Visuales seleccionados | Sí |

## 5.4. Marcador `Rentabilidad_Recomendacion`

Deje visible:

- Grupo_Recomendacion.

Oculte o reduzca:

- gráficos secundarios;
- objetos que ya no aporten a la conclusión.

Configuración recomendada:

| Opción | Estado |
|---|---:|
| Datos | No |
| Mostrar | Sí |
| Página actual | Sí |
| Visuales seleccionados | Sí |

## 5.5. Actualizar los marcadores

Después de modificar la visibilidad:

1. seleccione el marcador;
2. abra los tres puntos;
3. seleccione **Actualizar**;
4. pruebe los demás marcadores;
5. vuelva al marcador y verifique el resultado.

---

# 6. Navegador de marcadores

## Propósito

Permitir el cambio entre las vistas internas de la página Rentabilidad.

## Procedimiento

1. Agrupe los tres marcadores narrativos.
2. Seleccione **Insertar → Botones → Navegador**.
3. Elija **Navegador de marcadores**.
4. Configure el navegador para mostrar únicamente el grupo de Rentabilidad.
5. Cambie las etiquetas visibles a:

```text
Resumen | Diagnóstico | Recomendación
```

## Diferencia frente al navegador de páginas

| Navegador de páginas | Navegador de marcadores |
|---|---|
| Cambia de lienzo | Cambia de estado |
| Separa grandes temas | Cambia vistas internas |
| Usa páginas del informe | Usa marcadores |
| Forma el recorrido principal | Complementa una página |

---

# 7. Panel lateral de filtros

## Propósito

Evitar que numerosos segmentadores ocupen el lienzo principal.

## Objetos del panel

- Región.
- Canal.
- Segmento.
- Categoría.
- Botón `Cerrar filtros`.

## Marcadores necesarios

- `Panel_Filtros_Abierto`.
- `Panel_Filtros_Cerrado`.

## Configuración correcta

| Opción | Abierto | Cerrado |
|---|---:|---:|
| Datos | No | No |
| Mostrar | Sí | Sí |
| Página actual | No | No |
| Visuales seleccionados | Sí | Sí |

### Justificación

Los marcadores deben mostrar u ocultar el panel sin cambiar las selecciones del usuario.

## Procedimiento

1. Cree un rectángulo lateral.
2. Coloque los segmentadores sobre el rectángulo.
3. Agrupe todos los objetos.
4. Cree un botón para abrir.
5. Cree un botón para cerrar.
6. Asigne a cada botón el marcador correspondiente.
7. Pruebe que los filtros se conserven al abrir y cerrar.

---

# 8. Tooltip de página

## Propósito

Mostrar información complementaria sin saturar las visualizaciones principales.

## Página

Cree una página denominada:

```text
Tooltip producto
```

## Configuración

1. Active **Información de página → Tooltip**.
2. Configure el tamaño como **Tooltip**.
3. Agregue:
   - Producto.
   - Ventas.
   - Utilidad.
   - Margen.
   - Tasa de descuento.
   - OTIF.
   - Minitendencia.
4. Oculte la página.

## Asignación

1. Seleccione la visualización de productos.
2. Abra **Formato → Información sobre herramientas**.
3. Seleccione:
   - Tipo: Página del informe.
   - Página: Tooltip producto.

## Criterio de calidad

El tooltip debe ampliar el contexto, pero no contener la conclusión principal del informe.

---

# 9. Drillthrough hacia el detalle del producto

## Propósito

Pasar desde una visualización resumida al detalle de un producto seleccionado.

## Página

Cree:

```text
Detalle producto
```

## Campo de drillthrough

Agregue:

```text
Datos[Producto]
```

## Visualizaciones sugeridas

- Ventas.
- Utilidad.
- Margen.
- Tendencia mensual.
- Pedidos.
- Devoluciones.
- Regiones.
- Canales.
- Tabla de detalle.

## Botón de retorno

Inserte un botón con acción:

```text
Atrás
```

## Verificación

Desde una barra o punto asociado con un producto:

1. haga clic derecho;
2. seleccione **Obtener detalles**;
3. seleccione `Detalle producto`;
4. compruebe que el filtro del producto se conserve;
5. utilice el botón Atrás.

## Error que debe evitarse

Crear una página de drillthrough que repita exactamente la misma información del resumen.

---

# 10. Drill down y drill up

## Propósito

Explorar niveles jerárquicos en una misma visualización.

## Jerarquías sugeridas

### Tiempo

```text
Año → Trimestre → Mes
```

### Producto

```text
Categoría → Producto
```

## Uso

- **Drill down:** baja al siguiente nivel.
- **Drill up:** regresa al nivel superior.
- **Expandir todo:** muestra simultáneamente niveles relacionados.

## Criterio de calidad

La jerarquía debe representar una relación natural entre niveles.

## Error que debe evitarse

Crear jerarquías arbitrarias, por ejemplo:

```text
Región → Producto → Transportadora → Vendedor
```

---

# 11. Editar interacciones

## Propósito

Controlar cómo una visualización afecta a las demás.

## Procedimiento

1. Seleccione una visualización.
2. Active **Formato → Editar interacciones**.
3. Para cada objeto, defina:
   - Filtrar.
   - Resaltar.
   - Ninguna.

## Ejemplo correcto

Al seleccionar Región Norte:

- se actualizan las tarjetas;
- se filtran los productos;
- cambia la tendencia;
- se mantiene visible la meta.

## Ejemplo incorrecto

Algunas visualizaciones quedan filtradas y otras permanecen en el total general sin indicarlo.

---

# 12. Q&A como exploración opcional

## Propósito

Permitir preguntas en lenguaje natural sobre el modelo.

## Página opcional

Cree:

```text
Exploración libre
```

## Preguntas sugeridas

- Ventas por región.
- Utilidad por producto.
- Margen por canal.
- Pedidos tardíos por mes.
- Satisfacción por transportadora.

## Precaución

Q&A complementa la narrativa, pero no reemplaza las páginas diseñadas para responder la pregunta administrativa.

---

# 13. Configuración resumida de marcadores

| Marcador | Datos | Mostrar | Página actual | Función |
|---|---:|---:|---:|---|
| Rentabilidad_Resumen | No | Sí | Sí | Mostrar vista resumen |
| Rentabilidad_Diagnostico | No | Sí | Sí | Mostrar análisis |
| Rentabilidad_Recomendacion | No | Sí | Sí | Mostrar cierre |
| Panel_Filtros_Abierto | No | Sí | No | Mostrar panel |
| Panel_Filtros_Cerrado | No | Sí | No | Ocultar panel |
| Limpiar_Filtros | Sí | No | No | Restablecer filtros |

## Nota

El marcador `Limpiar_Filtros` debe guardar el estado predeterminado de los segmentadores. Se utiliza exclusivamente para restablecerlos.

---

# 14. Lista de verificación

- [ ] El navegador principal cambia entre páginas.
- [ ] El navegador de marcadores cambia vistas dentro de Rentabilidad.
- [ ] Los marcadores narrativos no eliminan los filtros del usuario.
- [ ] El panel lateral abre y cierra sin modificar selecciones.
- [ ] Los segmentadores están sincronizados correctamente.
- [ ] El tooltip muestra información complementaria.
- [ ] El drillthrough conserva el producto seleccionado.
- [ ] El botón Atrás funciona.
- [ ] Las jerarquías permiten drill down y drill up.
- [ ] Las interacciones entre visualizaciones son coherentes.
- [ ] Las páginas auxiliares están ocultas.
- [ ] Todas las interacciones ayudan a responder una pregunta.

---

## Resultado de la fase

La arquitectura final debe quedar así:

```text
PÁGINAS
Inicio
├── Panorama
├── Rentabilidad
│   ├── Marcador Resumen
│   ├── Marcador Diagnóstico
│   └── Marcador Recomendación
├── Logística
│   ├── Marcador Resumen
│   └── Marcador Transportadoras
├── Acción
├── Detalle producto [drillthrough]
└── Tooltip producto [oculta]
```

## Error principal que debe evitarse

> Usar páginas y marcadores como si fueran equivalentes.

Las páginas organizan los temas principales. Los marcadores guardan estados o vistas dentro de esos temas.
