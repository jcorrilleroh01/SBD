# Escape Room DynamoDB · Caso 07-OMEGA

**Alumno:** Jaime Corrillero Holgado
**Fecha:** 2026-11-10
**Tabla consultada:** `EscapeRoomMinecraft`

---

## Misión 1 · Los sospechosos

**¿Cuántas cuentas presentan actividad en esa franja?**

```sql
SELECT nombre FROM "EscapeRoomMinecraft"
WHERE fechaHora BETWEEN '2026-11-02T22:10:00' AND '2026-11-02T22:20:00'
```

**6 cuentas:** RedstonePro, NotchFan, ShadowPixel, AdminSol, LunaByte y CreeperKing.

**¿Qué jugadores quedan descartados al no tener ningún evento en ese intervalo?**

```sql
SELECT nombre FROM "EscapeRoomMinecraft"
WHERE nombre <> 'RedstonePro' AND nombre <> 'NotchFan' AND nombre <> 'ShadowPixel'
AND nombre <> 'AdminSol' AND nombre <> 'LunaByte' AND nombre <> 'CreeperKing'
```

Picador99, ZorroCraft, AlexBuilder, EnderMago, Dragonera y NetherFox.

**¿Estar activo en esa ventana temporal implica culpabilidad?**

No. Que una cuenta tenga actividad en esa franja solo demuestra que estaba conectada, no que robara nada. Para culpar a alguien hay que comprobar también dónde estaba y qué hizo (zona, tipo de evento y objeto).

**Entregable: cuentas activas entre las 22:10 y las 22:20**

- P003 · LunaByte
- P004 · NotchFan
- P005 · RedstonePro
- P007 · CreeperKing
- P009 · ShadowPixel
- P011 · AdminSol

Todas estuvieron **presentes** en la franja, pero eso por sí solo **no las hace culpables**: es solo una lista de sospechosos que hay que seguir investigando.

---

## Misión 2 · El castillo y la cámara

**¿Quiénes estuvieron en el castillo entre las 22:10 y las 22:20?**

```sql
SELECT * FROM "EscapeRoomMinecraft"
WHERE zona = 'Castillo' AND fechaHora BETWEEN '2026-11-02T22:10:00' AND '2026-11-02T22:20:00'
```

RedstonePro, NotchFan, AdminSol, LunaByte y CreeperKing.

**¿Quién fue el único jugador que registró una entrada a la CamaraTesoro?**

```sql
SELECT * FROM "EscapeRoomMinecraft"
WHERE zona = 'CamaraTesoro'
```

**CreeperKing (P007)**, con el evento `ENTRAR_ZONA` a las 22:14:10. AdminSol también aparece en la cámara, pero sus eventos son depositar la espada (22:09:00) y denunciar el robo (22:25:00), no una entrada.

**¿La consulta se ejecutó como Query o como Scan?**

Como **Scan**. `zona` no es clave de la tabla ni tiene índice, así que DynamoDB tiene que recorrer la tabla entera y filtrar después.

**Entregable: registros destacados en el castillo y la cámara**

- **22:09:00** · AdminSol · `DEPOSITAR_OBJETO` · CamaraTesoro · `ESPADA_OMEGA`
- **22:14:00** · AdminSol · `INCIDENCIA_RED` · Castillo · detalle `PICO_LATENCIA_SERVIDOR`
- **22:14:05** · LunaByte · `TIMEOUT_CONEXION` · Castillo · —
- **22:14:10** · CreeperKing · `ENTRAR_ZONA` · CamaraTesoro · —
- **22:14:51** · NotchFan · `ABRIR_COFRE` · Castillo · `ESPADA_ACERO`
- **22:15:02** · CreeperKing · `ABRIR_COFRE` · CamaraTesoro · `ESPADA_OMEGA`
- **22:15:18** · CreeperKing · `RETIRAR_OBJETO` · CamaraTesoro · `ESPADA_OMEGA`
- **22:16:10** · CreeperKing · `SALIR_ZONA` · Castillo · —

---

## Misión 3 · Los minutos del lag

**¿Qué registros técnicos confirman el fallo de conexión a las 22:14? ¿Quién sufrió una desconexión por timeout?**

```sql
SELECT * FROM "EscapeRoomMinecraft"
WHERE fechaHora BETWEEN '2026-11-02T22:14:00' AND '2026-11-02T22:14:59'
```

El fallo es real: a las **22:14:00** hay un evento `INCIDENCIA_RED` en el Castillo con el detalle `PICO_LATENCIA_SERVIDOR` (un pico de lag en el servidor). Quien sufrió la desconexión por timeout fue **LunaByte (P003)**, con el evento `TIMEOUT_CONEXION` a las **22:14:05**.

**¿Qué jugador aprovechó los segundos de confusión del lag para entrar en la Cámara del Tesoro?**

**CreeperKing (P007)**, que entró en la CamaraTesoro a las 22:14:10, justo durante el lag.

**¿A qué segundo exacto se abrió el cofre y a qué segundo se retiró la Espada Omega?**

```sql
SELECT * FROM "EscapeRoomMinecraft"
WHERE jugadorId = 'P007' AND fechaHora BETWEEN '2026-11-02T22:14:00' AND '2026-11-02T22:16:15'
```

El cofre se abrió a las **22:15:02** y la espada se retiró a las **22:15:18**.

**Entregable: secuencia de 22:14 a 22:16**

1. **22:14:00** · Pico de lag en el servidor (`INCIDENCIA_RED`).
2. **22:14:05** · LunaByte (P003) se desconecta por timeout.
3. **22:14:10** · Aprovechando la confusión, CreeperKing (P007) entra en la CamaraTesoro.
4. **22:15:02** · CreeperKing abre el cofre de la Espada Omega.
5. **22:15:18** · CreeperKing retira la Espada Omega.
6. **22:16:10** · CreeperKing sale de la cámara hacia el Castillo.

Causa y efecto: el fallo del servidor generó confusión y CreeperKing la aprovechó para robar la espada en poco más de un minuto.

---

## Misión 4 · El rastro de la Espada Omega

```sql
SELECT * FROM "EscapeRoomMinecraft"."PorObjeto"
WHERE objeto = 'ESPADA_OMEGA'
```

**¿Cuántos eventos en total hacen referencia a ESPADA_OMEGA?**

**7 eventos.**

**¿Quién la depositó inicialmente en el cofre y a qué hora?**

**AdminSol (P011)**, a las **22:09:00**, en la CamaraTesoro.

**¿A quién se la transfirió el ladrón y en qué zona fue el intercambio?**

CreeperKing (P007) se la pasó a **ShadowPixel (P009)** en el **BosqueOscuro**, a las 22:17:40.

**¿Dónde fue ocultada finalmente la espada y por quién?**

En la **MinaAbandonada**, por **ShadowPixel (P009)**, a las 22:20:10.

**Entregable: cronología completa de la espada**

- **22:09:00** · AdminSol (P011) deposita la espada · CamaraTesoro
- **22:15:02** · CreeperKing (P007) abre el cofre · CamaraTesoro
- **22:15:18** · CreeperKing (P007) retira la espada · CamaraTesoro
- **22:17:40** · CreeperKing (P007) transfiere la espada · BosqueOscuro · a ShadowPixel (P009)
- **22:17:44** · ShadowPixel (P009) recibe la espada · BosqueOscuro · de CreeperKing (P007)
- **22:20:10** · ShadowPixel (P009) oculta la espada · MinaAbandonada
- **22:25:00** · AdminSol (P011) denuncia el robo · CamaraTesoro

---

## Misión 5 · El engaño de la IP y la conspiración

**¿Qué dos cuentas comparten la IP 198.51.100.77?**

```sql
SELECT nombre, jugadorId FROM "EscapeRoomMinecraft"
WHERE ip = '198.51.100.77'
```

**CreeperKing (P007)** y **ShadowPixel (P009)**.

**¿Por qué compartir una IP pública no demuestra que sean la misma persona?**

Una misma IP no significa una misma persona. Es como la dirección de un edificio: muchas personas pueden conectarse a internet con la misma IP si están en la misma casa, en el mismo instituto o usan el mismo servicio (una VPN o la red de su compañía de internet). Solo demuestra que las dos cuentas usaron la misma conexión, no que las maneje el mismo jugador.

**¿Qué eventos prueban que ambas cuentas actuaron de forma coordinada?**

El traspaso de la espada en el BosqueOscuro. A las **22:17:40** CreeperKing la transfiere con destinatario **P009**, y a las **22:17:44** ShadowPixel la recibe con remitente **P007**. El sistema registra las dos partes del intercambio, así que la colaboración está demostrada.

**Entregable: matriz comparativa**

- **IP compartida (198.51.100.77)**
  - Lo que sugiere: que podrían ser la misma persona.
  - Límite técnico: varias personas pueden compartir IP (misma casa, Wi-Fi, VPN).
  - Evidencia que lo confirma: ninguna por sí sola. Es una señal débil.
- **Robo durante el lag**
  - Lo que sugiere: que CreeperKing aprovechó el fallo del servidor.
  - Límite técnico: no demuestra que tuviera ayuda.
  - Evidencia que lo confirma: `ENTRAR_ZONA` (22:14:10), `ABRIR_COFRE` (22:15:02) y `RETIRAR_OBJETO` (22:15:18).
- **Traspaso en el BosqueOscuro**
  - Lo que sugiere: que P007 y P009 colaboraron.
  - Límite técnico: ninguno, porque lo registra el propio sistema.
  - Evidencia que lo confirma: `TRANSFERIR_OBJETO` (22:17:40, destinatario P009) y `RECIBIR_OBJETO` (22:17:44, remitente P007).
- **Ocultación en la MinaAbandonada**
  - Lo que sugiere: que ShadowPixel participó activamente.
  - Límite técnico: ninguno.
  - Evidencia que lo confirma: `OCULTAR_OBJETO` a las 22:20:10 por P009.

**Conclusión:** la IP no basta para expulsarlos como si fueran el mismo jugador. Lo que sí prueba que actuaron juntos es el traspaso de la espada.

---

## Misión 6 · Caso cerrado y dictamen forense

**¿Quién es el autor material del robo y en qué segundo exacto lo cometió?**

**CreeperKing (P007)**. Retiró la Espada Omega a las **22:15:18**, después de abrir el cofre a las 22:15:02, aprovechando el lag.

**¿Quién actuó como cómplice y cuál fue su papel?**

**ShadowPixel (P009)**. Recibió la espada de CreeperKing en el BosqueOscuro (22:17:44) y la escondió en la MinaAbandonada (22:20:10).

**¿Cuál es la última ubicación comprobada de la Espada Omega?**

La **MinaAbandonada**, donde la ocultó ShadowPixel a las 22:20:10.

**¿Qué afirmaciones del expediente inicial quedaron desmentidas por los registros?**

- **Que el lag era una coartada inventada:** es falso. El pico de lag y el timeout de LunaByte están registrados.
- **Que P007 y P009 son el mismo jugador por compartir IP:** la IP no lo demuestra. Lo que está probado es que colaboraron.
- **Que todos los avisados eran culpables:** estar activo en la franja no implicaba culpa. LunaByte, por ejemplo, solo sufrió una desconexión.

---

# Informe Pericial de Investigación Forense · Caso 07-OMEGA

- **Estudiante / Analista:** Jaime Corrillero Holgado
- **Fecha de emisión:** 2026-11-10
- **Caso:** Sustracción de la Espada Omega en ÁgoraCraft
- **Fuente de auditoría:** Tabla `EscapeRoomMinecraft` (Amazon DynamoDB, región `us-east-1`)

---

## 1. Dictamen pericial

El robo de la Espada Omega lo cometió **CreeperKing (P007)**. A las 22:14:00 hubo un pico de lag en el servidor que, a las 22:14:05, desconectó a LunaByte (P003). CreeperKing aprovechó esa confusión para entrar en la CamaraTesoro a las 22:14:10, abrió el cofre a las 22:15:02 y se llevó la espada a las 22:15:18. A las 22:17:40 se la entregó en el BosqueOscuro a su cómplice **ShadowPixel (P009)**, que la escondió en la MinaAbandonada a las 22:20:10. La denuncia oficial de AdminSol (P011) no llegó hasta las 22:25:00, diez minutos después del robo.

---

## 2. Cronología forense probada

| Hora exacta (ISO) | ID Cuenta | Alias | Acción / Evento | Zona | Objeto y detalles clave |
|---|---|---|---|---|---|
| `2026-11-02T22:09:00` | `P011` | AdminSol | `DEPOSITAR_OBJETO` | `CamaraTesoro` | `ESPADA_OMEGA` (custodia inicial) |
| `2026-11-02T22:14:00` | `P011` | AdminSol | `INCIDENCIA_RED` | `Castillo` | Detalle `PICO_LATENCIA_SERVIDOR` (fallo del servidor) |
| `2026-11-02T22:14:05` | `P003` | LunaByte | `TIMEOUT_CONEXION` | `Castillo` | Desconexión por timeout |
| `2026-11-02T22:14:10` | `P007` | CreeperKing | `ENTRAR_ZONA` | `CamaraTesoro` | Entra durante el lag del servidor |
| `2026-11-02T22:15:02` | `P007` | CreeperKing | `ABRIR_COFRE` | `CamaraTesoro` | `ESPADA_OMEGA` |
| `2026-11-02T22:15:18` | `P007` | CreeperKing | `RETIRAR_OBJETO` | `CamaraTesoro` | `ESPADA_OMEGA` (robo físico) |
| `2026-11-02T22:17:40` | `P007` | CreeperKing | `TRANSFERIR_OBJETO` | `BosqueOscuro` | `ESPADA_OMEGA`, destinatario `P009` |
| `2026-11-02T22:17:44` | `P009` | ShadowPixel | `RECIBIR_OBJETO` | `BosqueOscuro` | `ESPADA_OMEGA`, remitente `P007` |
| `2026-11-02T22:20:10` | `P009` | ShadowPixel | `OCULTAR_OBJETO` | `MinaAbandonada` | `ESPADA_OMEGA` (escondite final) |
| `2026-11-02T22:25:00` | `P011` | AdminSol | `DENUNCIAR_ROBO` | `CamaraTesoro` | `ESPADA_OMEGA` (denuncia oficial) |

---

## 3. Identificación de responsabilidades

- **Autor material del robo:** CreeperKing (`P007`)
- **Cómplice y receptador:** ShadowPixel (`P009`)
- **Momento exacto del robo físico:** 22:15:18
- **Momento oficial de denuncia y alarma:** 22:25:00
- **Última ubicación conocida del objeto:** MinaAbandonada

---

## 4. Distinción forense: Hechos observados vs. Inferencias

- **Hecho probado (con evidencia en registros):**
  A las 22:17:40 CreeperKing (P007) registra que transfiere la Espada Omega a P009, y a las 22:17:44 ShadowPixel (P009) registra que la recibe de P007, los dos en el BosqueOscuro. El sistema guarda las dos partes del intercambio, así que la colaboración está demostrada.

- **Inferencia lógica razonable:**
  CreeperKing aprovechó a propósito el fallo del servidor, porque entró en la cámara justo durante el lag. Los registros demuestran que las dos cosas coincidieron en el tiempo, pero no recogen su intención.

- **Limitación técnica del dato:**
  Que P007 y P009 compartan la IP `198.51.100.77` no demuestra que sean la misma persona. Muchas personas pueden conectarse con la misma IP si comparten conexión (la misma casa, un instituto, una VPN o la red de su compañía de internet). Solo prueba que usaron la misma red.

---

## 5. Análisis de eficiencia y Big Data en DynamoDB

1. **Operación de tipo Scan:**
   - **Consulta:** la búsqueda de eventos en la Cámara del Tesoro (Misión 2).
   - *Consulta PartiQL:* `SELECT * FROM "EscapeRoomMinecraft" WHERE zona = 'CamaraTesoro'`
   - **Explicación:** `zona` no es clave de la tabla ni tiene índice, así que DynamoDB no puede ir directo a esos datos: lee la tabla entera y después filtra. Con 500 millones de registros la consulta sería muy lenta, gastaría muchísima capacidad de lectura (y por tanto dinero) y podría frenar al resto de consultas. Lo correcto sería crear un índice por `zona`.

2. **Operación sobre Global Secondary Index (GSI):**
   - **Consulta:** el rastreo de la Espada Omega (Misión 4).
   - *Consulta PartiQL:* `SELECT * FROM "EscapeRoomMinecraft"."PorObjeto" WHERE objeto = 'ESPADA_OMEGA'`
   - **Explicación:** el índice permite ir directamente a los 7 eventos de la espada sin recorrer la tabla. Con la proyección `ALL` el índice guarda una copia de todos los datos de cada registro (zona, jugador, `destinatario`, `remitente`…), así que la consulta devuelve toda la información de una vez, sin tener que volver a leer la tabla original.
