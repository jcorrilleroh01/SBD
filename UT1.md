# Actividad: Red de sensores de calidad del aire

## Contexto

El ayuntamiento quiere utilizar **24 sensores** para controlar la calidad del aire, generar alertas y planificar medidas de movilidad.

La red registra contaminantes, temperatura, humedad, ubicación y hora. Durante una prueba de **48 horas** se han detectado problemas de cobertura y de calidad de los datos.

## Condiciones

- **Duración:** 2 horas.
- **Modalidad:** trabajo individual o por parejas.
- No hay que programar, ejecutar scripts ni modificar datasets.
- El análisis se realizará con el **dossier de resultados**.

---

## Actividad

### 1. Comprender el problema *(20 minutos)*

Responde brevemente:

1. ¿Quién utilizará estos datos?

    **Respuesta: Los ingenieros de datos que seran quienes los interpreten y sepan sacar conclusiones de estos datos captados**

2. ¿Qué decisiones se pueden tomar con ellos?
        **Respuesta: Se puede llegar a la deduccion de problemas a largo plazo como algun contaminante en el  aire que pueda llegar a ser peligroso o probolemas de accion inmediata como incendios**

3. ¿Qué diferencia hay entre una **alerta inmediata** y un **informe histórico**?
**Un informe historico recauda informacion de la que en su conjunto puedes llegar a detectar patrones o situaciones que se repitan que pueden afectar de alguna manera y las alertas inmediatas te muestran el problema de forma directa y requiere de una solucion inmediata**

### 2. Analizar cobertura y calidad *(35 minutos)*

Utilizando el dossier:

1. Identifica **dos problemas de calidad** y explica sus consecuencias.
    **Respuesta: Se puede llegar a la deduccion de problemas a largo plazo como algun contaminante en el  aire que pueda llegar a ser peligroso o probolemas de accion inmediata como incendios**
2. Indica qué **distrito** necesita mayor atención y justifica tu respuesta.
3. Elige una **anomalía** y explica si la corregirías, la marcarías como dudosa o la excluirías.

> **Nota:** No es necesario recalcular los datos. Debes interpretar las tablas.

### 3. Comparar arquitecturas *(25 minutos)*

Completa esta tabla:

| Criterio                        | Batch | Streaming |
|---------------------------------|-------|-----------|
| Rapidez para generar alertas    |       |           |
| Coste y complejidad             |       |           |
| Informes históricos             |       |           |
| Picos de datos                  |       |           |

Indica qué alternativa usarías para las **alertas** y cuál para los **informes históricos**.

### 4. Elaborar una recomendación *(30 minutos)*

Redacta una recomendación para el ayuntamiento que incluya:

- [ ] El riesgo más urgente.
- [ ] La actuación que propones.
- [ ] Dos razones basadas en el dossier.
- [ ] Un problema que seguiría pendiente.
- [ ] Una medida de privacidad.