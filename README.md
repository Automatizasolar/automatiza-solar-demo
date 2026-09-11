# Automatiza Solar — Panel de leads (demo)

Demo interactiva de un solo archivo (`index.html`, sin build ni dependencias) para enseñar en
llamadas de venta: el panel que vería un instalador solar a la mañana siguiente, con las
personas que escribieron de noche y en qué quedó cada una.

## Cómo usarlo en una llamada

- **Toca cualquier tarjeta** para leer la conversación de esa persona.
- **Andrés Zapata** está marcado como "Solo curiosidad": su conversación es interactiva y
  termina recuperándolo, lo que actualiza los indicadores en vivo.
- **Velocidad** (Normal / Rápida / Al instante) ajusta las pausas del chat. En una llamada,
  *Rápida* suele ir mejor.
- **← Atrás** deshace la última elección y rebobina la conversación al instante. Sirve para
  corregir un clic equivocado sin reiniciar, o para enseñar la otra rama en la misma llamada.
  Desaparece al confirmar la recuperación, porque eso ya modificó el panel.
- **Reiniciar demo** vuelve al estado inicial. Pide confirmación si hay una conversación a medias.
- El estado sobrevive a un F5 accidental (`sessionStorage`); "Reiniciar demo" lo borra.

## Cómo está hecha la conversación

No es una secuencia de pasos encadenados, sino una función `buildScript(lead, respuestas)`
que **reconstruye la conversación entera** a partir de las respuestas dadas. Deshacer un paso
es borrar la última respuesta y volver a dibujar: por eso cambiar de rama a mitad del flujo
no deja mensajes huérfanos de la rama anterior.

## Reglas de los datos

- **Un precio por kit**, definido una sola vez en `KITS`. Ninguna cifra se escribe a mano en
  otro sitio, así que no puede haber dos precios para el mismo kit.
- **El kit recomendado se calcula** con `recomendarKit(factura)`, no se escribe por lead.
- La estimación de consumo usa `TARIFA_COP_POR_KWH` (700–950 COP/kWh). Si cambia la tarifa,
  se toca ahí y en ningún otro lugar.

## Privacidad

Todas las personas y negocios del panel son **ficticios**. No hay formularios, ni cookies, ni
llamadas de red: nada sale del navegador salvo la fuente de Google Fonts.

## Estructura

```
index.html      el panel completo (estilos y scripts en línea)
brand/          logo, favicons e imagen para compartir enlaces
vercel.json     cabeceras de seguridad y caché
```

## Pendientes

- [ ] **Dominio propio.** Al ponerlo hay que actualizar `canonical`, `og:url` y la URL del
      `og:image`, y los enlaces a la landing del pie y la cabecera.
- [ ] **Conectar el repo a Vercel** si se quiere auto-deploy en cada `push`.

## Deploy

Sitio 100% estático — Framework Preset *Other*, sin comando de build.
