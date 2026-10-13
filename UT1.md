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
    **Respuesta: Huecos temporales,esto hace que la medicion no sea todo lo precisa que necesitamos, al haber tiempo sin captar datos puede afectar a los datos y conclusiones sacadas al final ya que puedn llegar a ser erroneas por la falta de datos durante una franja diaria,al ser 30 minutos no afecta demasiado pero aun asi hay perdida de datos**
    **Duplicados,esto hace engordar los registros y se puede asignar mas cuidados a una zona que no los precisa unicamente porque manda registros dobles**
2. Indica qué **distrito** necesita mayor atención y justifica tu respuesta.
    **D6 Parque Natural,ya que no tiene control ninguno, a pesar de ser una zona de vegetacion que precisa de cuidados mas intensivos que una calle cualquiera o una plaza, aunque tenga pocos habitantes dicha zona necesita un control ya que se trata de una reserva natural repleta de vegetacion que requiere muchos cuidados**
3. Elige una **anomalía** y explica si la corregirías, la marcarías como dudosa o la excluirías.
**Unidades incorrectas,no es una anomalia que afecte demasiado al sistema ya que unicamente se soluciona con un cambio de unidad,aunque si seria un gran problema si cada segundo entran muchas mediciones en farenheit en las cuales haya que hacer cambio de unidad,entonces entorpeceria el flujo demasiado innecesariamente**

> **Nota:** No es necesario recalcular los datos. Debes interpretar las tablas.
11
### 3. Comparar arquitecturas *(25 minutos)*

Completa esta tabla:

| Criterio                        | Batch | Streaming |
|---------------------------------|-------|-----------|
| Rapidez para generar alertas    |     Minutos u horas  |    Segundos o pocos minutos       |
| Coste y complejidad             |   Menores    |   Mayores        |
| Informes históricos             |  Muy adecuado     |       Adecuado, con más complejidad    |
| Picos de datos                  |   Se tratan en el siguiente lote    |       Requieren ventanas y marcas de agua    |

Indica qué alternativa usarías para las **alertas** y cuál para los **informes históricos**.

### 4. Elaborar una recomendación *(30 minutos)*

Redacta una recomendación para el ayuntamiento que incluya:

- [ ] El riesgo más urgente.
- [ ] La actuación que propones.
- [ ] Dos razones basadas en el dossier.
- [ ] Un problema que seguiría pendiente.
- [ ] Una medida de privacidad.

