# Automatiza Solar — Panel de leads (demo)

Demo interactiva de un solo archivo (`index.html`, sin build ni dependencias) para enseñar en
llamadas de venta: el panel que vería un instalador solar a la mañana siguiente, con las
personas que escribieron de noche y en qué quedó cada una.

## Cómo usarlo en una llamada

- **La demo abre con una conversación ya puesta.** Al cargar en pantalla ancha se despliega
  sola la de Marcela Restrepo, junto al marcador: quien abre el enlace entiende en tres
  segundos de qué va esto sin tener que tocar nada.
  Se abre una **ya resuelta** a propósito. Abrir un lead frío gastaría el momento que quieres
  provocar tú en vivo — recuperarlo y ver moverse las cifras — así que los seis fríos quedan
  intactos. Se cambia con `LEAD_INICIAL` en el script.
  No se auto-abre por debajo de 75rem: ahí el cajón taparía el panel entero. Y si lo cierras,
  no vuelve solo, ni siquiera tras un F5; «Reiniciar demo» lo devuelve a la primera visita.

- **Las conversaciones son una lista, no tarjetas.** Cada persona es una fila con estado,
  nombre, qué pide y cuánto vale, alineados por columna para poder barrerlas y compararlas.
  **Tocar la fila despliega el detalle completo dentro de ella** — sistema instalado, gasto
  informado, componentes, hora — y tocar otra vez lo cierra.
- **El botón de la derecha abre la conversación**: dice *Recuperar* (verde) en quien se
  enfrió y *Ver chat* en el resto. Son dos acciones distintas, así que son dos botones y no
  uno anidado en otro. El chat entra como **cajón lateral** y, en pantalla ancha, el panel se
  aparta en vez de quedar tapado: se ve el marcador y la conversación a la vez. Eso es lo que
  hay que enseñar — el dolor y la solución en el mismo pantallazo. Por debajo de 48rem el
  cajón ocupa la pantalla entera.
- En la columna de valor, quien ya cerró muestra lo que vale y quien se enfrió muestra lo que
  está **en juego**, en rojo. La columna nunca queda vacía, que es lo que la haría inútil de
  barrer.
- **Andrés Zapata** está marcado como "Solo curiosidad": su conversación es interactiva y
  termina recuperándolo. Al confirmar, **los indicadores de arriba se mueven de verdad**:
  entra en el histórico de hoy como una venta y una visita más, y las cuatro tarjetas
  afectadas parpadean **con un chip que dice de dónde sale el movimiento** («+$8.900.000
  ahora»). Lo que ve el visitante sube por el precio exacto del kit que eligió, y las cifras
  de dinero **suben contándose** en vez de aparecer ya cambiadas: al apagar el agente se ve
  caer el dinero, que es media demostración.
- **Velocidad** (Normal / Rápida / Al instante) ajusta las pausas del chat. En una llamada,
  *Rápida* suele ir mejor.
- **← Atrás** deshace la última elección y rebobina la conversación al instante. Sirve para
  corregir un clic equivocado sin reiniciar, o para enseñar la otra rama en la misma llamada.
  Desaparece al confirmar la recuperación, porque eso ya modificó el panel.
- **Reiniciar demo** vuelve al estado inicial. Pide confirmación si hay una conversación a medias.
- El estado sobrevive a un F5 accidental (`sessionStorage`); "Reiniciar demo" lo borra.

## Doce conversaciones, no una repetida

No todo el que escribe de noche quiere lo mismo, y la demo se cae si las doce filas
cuentan la misma historia. Cada lead lleva una `intencion`:

- **`kit`** — sistema nuevo. Corre el cuestionario entero: factura, sitio, y las tres
  opciones en ficha.
- **`paneles`** y **`baterias`** — ya es cliente, tiene su kit instalado y viene a por una
  pieza concreta. La conversación es **más corta a propósito**: quien ya compró no repite el
  cuestionario. El agente confirma qué tiene montado, pregunta cuántas unidades y cotiza.

Un dueño de negocio reconoce los dos casos, y el segundo le interesa más de lo que parece:
es dinero que entra de gente que ya le compró. La panadería que quiere más respaldo para los
hornos de las 3 a.m. y la lavandería que dobló el consumo no son adornos — son las dos
ampliaciones que más se piden.

Cada quien abre con su propia frase (`apertura`), que es lo que evita que las doce suenen
al mismo guion.

**Seis de los doce están marcados como "Solo curiosidad"**: se enfriaron. La barra roja suma
lo que valen — para un sistema nuevo, el kit que le toca por su factura; para una ampliación,
lo que vino a pedir (`valorEnJuego`).

## Cómo está hecha la conversación

El chat imita WhatsApp lo justo para que se reconozca sin fingir que lo es: burbujas con
cola, marca de día, hora y doble check azul, indicador de tecleo y una cabecera que pasa a
«escribiendo…» mientras el agente responde. El papel tapiz **no son los garabatos de
WhatsApp** sino una retícula propia de celdas solares. La franja bajo la cabecera lleva el
estado del lead, y cambia a *Recuperado* en el mismo momento que la fila de la lista.

Las tres opciones de kit **no son un párrafo**, sino una ficha con precio y una marca en el
que corresponde al consumo del lead. Un muro de texto en un chat no lo lee nadie, y menos en
una llamada de venta: se ve el precio, se ve cuál recomienda el agente y se decide.

No es una secuencia de pasos encadenados, sino una función `buildScript(lead, respuestas)`
que **reconstruye la conversación entera** a partir de las respuestas dadas. Deshacer un paso
es borrar la última respuesta y volver a dibujar: por eso cambiar de rama a mitad del flujo
no deja mensajes huérfanos de la rama anterior.

## Cómo está organizado

Arriba, fijo en las tres vistas, **el marcador**: el panel de control y los cinco
indicadores. No es una vista más — es lo que tiene que verse moverse mientras pasa
cualquier otra cosa. Debajo, **tres pestañas** que se turnan la pantalla:

- **Conversaciones** (la de entrada) — los doce chats de anoche. Encima, una barra roja
  dice cuántos se enfriaron y cuánto valen; la pestaña lleva ese mismo número en rojo.
  Cuando no queda ninguno, barra y número cambian de tono.
- **Gráficas** — la gráfica y, en tarjeta aparte, el desglose de la pérdida.
- **Visitas** — el calendario, con el total en la propia pestaña.

Una pestaña oculta mide cero, así que la gráfica **no se dibuja hasta que se muestra**
(`chartPendiente`): si no, saldrían trazados sin sentido sobre un ancho de cero.

Se turnan la pantalla en vez de apilarse por una razón concreta: en una llamada de tres
minutos nadie rueda la rueda buscando la sección. Se toca una pestaña y está.

Dos reglas visuales sostienen el conjunto. **Oscuro es lo que manejas tú**: el periodo, la
calculadora y el interruptor viven juntos sobre el panel de control navy, separados de las
superficies blancas, que sólo informan. Y **el radio codifica la escala** — sección, tarjeta,
elemento, control — para que no todo pese lo mismo.

La tarjeta de pérdida ocupa el ancho entero por debajo de los otros cuatro indicadores: es el
asunto de la pantalla y no una métrica más en la fila.

## El panel

En el panel de control, un **filtro de periodo** (Hoy · 7 · 30 · 90 días, o un día concreto)
que manda sobre los cuatro indicadores y sobre la gráfica a la vez, para que las cifras de la
pantalla nunca se contradigan entre sí.

**La gráfica** son dos paneles apilados que comparten el eje X: clientes que escribieron y dinero
generado. No es un gráfico de doble eje a propósito — alinear dos escalas distintas en un mismo
plot inventa una correlación que no está en los datos. Una sola cruz recorre los dos paneles y
un único tooltip lee ambas medidas del mismo día. Hasta 31 días se pinta día a día; por encima,
por semanas. "Ver datos" abre la misma información en tabla.

Está dibujada en **SVG a mano**: la CSP del sitio no permite CDN, así que no hay librería de
gráficos, en línea con el resto del proyecto.

La tarjeta **"Ventas recuperadas"** cuenta ventas, no personas: son las que no habrían
entrado sin contestar a tiempo. Se llamaba "Leads recuperados", que invitaba a dividirla entre
el total de gente que escribió y leía como una cifra floja — cuando cada una vale entre
$8.900.000 y $21.800.000.

**El interruptor «Apagar el agente»** recalcula el panel entero con el escenario contrario:
respuesta a 11 horas, cero recuperados y la facturación desplomada. Es la pieza que hace ver el
problema en vez de contarlo.

**La calculadora** (mensajes al día y venta media) regenera todo con los números del visitante,
para que deje de mirar una demo ajena y se mire a sí mismo.

**El calendario** muestra las visitas agendadas por día, con la intensidad del verde marcando
cuántas hay. Se navega por meses y al tocar un día se ve quién viene, a qué hora y con qué kit.

## Reglas de los datos

- **Un precio por kit**, definido una sola vez en `KITS`, y **un precio por pieza de
  ampliación** en `AMPLIA` (panel de 730W y batería de 51.2V/100Ah). Ninguna cifra se escribe
  a mano en otro sitio, así que no puede haber dos precios para el mismo panel.
  **Son aproximados**: están en el rango real para que el panel resulte creíble, pero no son
  la lista oficial de precios. No cotices sobre ellos.
- **El kit recomendado se calcula** con `recomendarKit(factura)`, no se escribe por lead.
- La estimación de consumo usa `TARIFA_COP_POR_KWH` (700–950 COP/kWh). Si cambia la tarifa,
  se toca ahí y en ningún otro lugar.
- **El histórico es ficticio y se genera con semilla fija** (`generarHistoria`), derivada de la
  fecha del día: la misma pantalla en cada recarga y en cada llamada de venta. Calibrado al
  cliente objetivo — unas 18 personas al día con un 4% de cierre.
- **Cada peso perdido tiene una causa nombrada.** El bloque "Por qué se va ese dinero"
  reparte la pérdida entre tres motivos concretos — entró de noche entre semana, entró en fin
  de semana, o entró en horario pero estabas ocupado — con su importe, su porcentaje y cuántos
  mensajes hay detrás. El tooltip dice lo mismo por día ("Era sábado: 12 mensajes esperando al
  lunes"), y la gráfica sombrea los sábados y domingos, que es donde la línea gris se despega.
  **Las tres causas suman exactamente la tarjeta roja**: la pérdida se calcula una vez por día,
  redondeada, y todo lo demás se deriva de ella.
- **El escenario sin agente** sale de `SIN_AGENTE`: un 58% de los mensajes entran fuera de
  horario y de esos solo un 22% sigue comprando cuando contestas por la mañana. Las ventas se
  acumulan arrastrando la fracción de un día al siguiente — redondear cada día por separado
  hundía el escenario sin agente y **exageraba la pérdida** a bajo volumen.
  Los ingresos sin agente **se derivan del alcance** (`ingresos × alcance`), no de tiradas de
  venta aparte: cierras al mismo ritmo sobre la gente que sí atiendes. Así la proporción se
  mantiene en el 55-57% tanto a 15 mensajes al día como a 80, y en cualquier periodo.
- **Las visitas sin agente arrastran igual que las ventas** (`SIN_AGENTE.visitas`, 30%). Con
  aproximadamente una visita al día, redondear cada día por separado dejaba casi todas a cero:
  de 29 visitas salían 2 en vez de 9, y el panel contaba dos historias distintas — el dinero
  caía un 55% y las visitas un 93%. Se arrastra la fracción al día siguiente y vuelven a decir
  lo mismo.
- **Un lead recuperado en vivo entra en el histórico de hoy**, no en un contador aparte: así
  los indicadores, la gráfica, el calendario y el desglose de pérdida no pueden discrepar.
  No suma `personas` — esa persona ya había escrito — y no toca `ingresosSin`, porque sin el
  agente no habría comprado: por eso la tarjeta roja sube con él. La causa que lo explica es
  la hora a la que escribió, para que **las tres causas sigan sumando la tarjeta roja**.
  Se guardan aparte de `historia` (`recuperadosEnVivo`) y se reaplican cuando el histórico se
  regenera: al mover la calculadora y tras un F5. "Reiniciar demo" los descarta.
- Los colores de las series (`#0C5FA8` y `#138363`) están comprobados para daltonismo y
  contraste. El navy de marca `#062A50` **no** sirve como color de dato: es demasiado oscuro y
  con tan poca saturación que lee como gris. Se queda para texto.

## Privacidad

Todas las personas y negocios del panel son **ficticios**. No hay formularios, ni cookies, ni
llamadas de red: nada sale del navegador salvo la fuente de Google Fonts.

## Las fotos

Son de banco de imágenes (Unsplash), descargadas ya en `webp` y al tamaño exacto, y servidas
desde el propio dominio: la CSP es `img-src 'self' data:` y no admite ningún CDN en caliente.
El detalle de cada una, con su origen y cómo sustituirla, está en `brand/CREDITOS.md`.

- `brand/fotos/` — cabecera, banda de cierre y una miniatura por kit dentro del chat.
- `brand/avatares/` — los doce retratos de la lista y de la cabecera del chat.

Dos decisiones que no son evidentes al leer el código:

- **La foto de cabecera va como fondo CSS y no como `<img>`.** En móvil se oculta, y un `<img>`
  oculto **se descarga igual** — comprobado, y `loading="lazy"` tampoco lo evita. Declarando el
  fondo dentro del media query, el móvil se ahorra 131 KB que no iba a ver.
- **La inicial sigue debajo de cada avatar.** Si la foto no carga, se ve la inicial en vez de un
  círculo vacío.

Si un día tienes fotos de instalaciones tuyas, sustitúyelas: convencen más que un banco de
imágenes. Basta con respetar el nombre y las dimensiones.

## Estructura

```
index.html      el panel completo (estilos y scripts en línea, una sola fuente: Archivo)
brand/          logo, favicons e imagen para compartir enlaces
vercel.json     cabeceras de seguridad y caché
```

## Pendientes

- [ ] **Dominio propio.** Al ponerlo hay que actualizar `canonical`, `og:url` y la URL del
      `og:image`, y los enlaces a la landing del pie y la cabecera.
- [ ] **Conectar el repo a Vercel** si se quiere auto-deploy en cada `push`.

## Deploy

Sitio 100% estático — Framework Preset *Other*, sin comando de build.
