---
name: explicar-paso-a-paso
description: Arma con QuickFigen una explicación paso a paso de un problema de geometría —figura que se construye momento a momento, narración, datos, lo que se pide y lo que va quedando establecido— y la muestra navegable en el chat. Usar cuando la persona pide explicar o resolver paso a paso un problema de geometría para una clase, un repaso o para compartir con estudiantes.
---

# Explicaciones paso a paso con QuickFigen

`qf_explain` compila una explicación (.qfe v1) y la muestra en el chat como un
reproductor de clase: un cuadro por momento, la narración, la lista de pasos y,
si la hay, una exploración con deslizador. Solo geometría.

## Flujo

1. **Resuelve el problema primero.** Si hay un resultado numérico, compruébalo
   con `qf_measure` sobre el script de la figura antes de narrarlo.
2. **Lee la referencia** con `qf_dsl_reference` y `domain: "geometry"`.
3. **Arma el .qfe**:
   - `given`: la figura de partida (líneas del DSL).
   - `steps`: cada paso con sus `moments`. Cada momento agrega líneas
     (`dsl`), narra (`narration`, con `$…$` y **negrita**) y puede resaltar
     (`emphasize`), ocultar (`hide`), dar estilo (`style`), acercar la cámara
     (`focus`) o recorrer un punto (`sweep`).
   - `role` por momento: `statement`, `construction`, `reasoning`,
     `exploration`, `prediction` (una pregunta: la vista espera «Mostrar
     respuesta») o `conclusion`.
   - `data` (los datos, como fichas), `ask` (lo que se pide) y `fact` (lo que
     un momento deja establecido), con las fórmulas entre `$…$`.
   - `statement`, el enunciado: separa la actividad, la pregunta y cada
     alternativa en párrafos o en una lista; no lo escribas todo en un
     párrafo.
4. **Nombres**: `emphasize`, `hide`, `style`, `move`, `sweep` y `focus` usan
   nombres escritos en el DSL (`central = angle(A, O, B)`), no los ids
   automáticos. `move` y `sweep` solo valen para puntos de `on_circle`,
   `on_segment` u `on_side`.
5. **Llama a `qf_explain`.** Si la respuesta es un error, trae el paso, el
   momento, la línea (`stepLine`), el tipo (`kind`) y cómo arreglarlo (`hint`):
   corrige eso y vuelve a llamar.
6. **Revisa `diagnostics`**: `label_overlap` (un rótulo que choca con otro
   objeto) y `focus_out_of_frame` (algo nuevo quedó fuera del acercamiento).
   Para ver un cuadro, pide `previewFrames: ["2.1"]` (paso.momento, hasta
   tres): te devuelve su PNG, que no se muestra en el chat. Con `focus`, la
   figura sale entera y lo que se ve queda en un recuadro punteado.
7. **Entrega**: la explicación ya se ve en el chat. `editUrl` la abre en el
   editor de QF Explain para mejorarla, y el `.html` descargable funciona sin
   conexión.

## Compartir

Pide `viewLink: true` solo si la persona quiere compartir la explicación.
`viewUrl` es un link de solo lectura que cualquier persona que lo tenga puede
abrir sin cuenta; vence según la cuenta y se puede revocar en Configuración de
QuickFigen. Al darle el link, dale también el aviso de `viewUrlNotice`.

## Ejemplo

El ángulo inscrito mide la mitad del central: primero el central de 100°,
después P en el arco mayor con su ángulo de 50° resaltado, una pregunta y una
conclusión que recorre P por el arco.

```json qf_explain
{
  "qfe": 1,
  "domain": "geometry",
  "title": "El ángulo inscrito es la mitad del central",
  "statement": "En la circunferencia de centro $O$, el ángulo central $\\angle AOB$ mide $100^\\circ$ y $P$ es un punto del arco mayor.\n\n¿Cuánto mide $\\angle APB$?",
  "data": [
    "$\\angle AOB = 100^\\circ$"
  ],
  "ask": "$x = \\angle APB$",
  "given": [
    "O = point(0, 0)",
    "c = circle(O, 3)",
    "A = on_circle(c, 220)",
    "B = on_circle(c, 320)"
  ],
  "steps": [
    {
      "title": "El ángulo central",
      "moments": [
        {
          "narration": "Unimos $A$ y $B$ con el centro: el ángulo central mide $100^\\circ$.",
          "role": "construction",
          "dsl": [
            "sOA = segment(O, A)",
            "sOB = segment(O, B)",
            "central = angle(A, O, B)"
          ]
        }
      ]
    },
    {
      "title": "El ángulo inscrito",
      "moments": [
        {
          "narration": "Tomamos $P$ en el arco mayor y lo unimos con $A$ y $B$.",
          "role": "construction",
          "dsl": [
            "P = on_circle(c, 90)",
            "sPA = segment(P, A)",
            "sPB = segment(P, B)",
            "inscrito = angle(A, P, B)"
          ],
          "emphasize": [
            "inscrito"
          ]
        },
        {
          "narration": "¿Cambia $\\angle APB$ si $P$ recorre el arco mayor?",
          "role": "prediction"
        },
        {
          "narration": "No cambia: mide la mitad del central, $50^\\circ$, esté donde esté $P$.",
          "role": "conclusion",
          "sweep": {
            "point": "P",
            "from": 30,
            "to": 150,
            "frames": 8
          },
          "fact": "$\\angle APB = 50^\\circ$"
        }
      ]
    }
  ]
}
```

## Cuota

Cada explicación usa una unidad de la cuota mensual de la cuenta de
QuickFigen.
