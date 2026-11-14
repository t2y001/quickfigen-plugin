---
name: figuras-quickfigen
description: Dibuja figuras de matemática y física con el motor de QuickFigen —geometría con regla y compás, funciones en el plano cartesiano, cuerpos y fuerzas, óptica— y verifica sus medidas. Usar cuando la persona pide una figura, un gráfico de una función, un diagrama de cuerpo libre o una construcción geométrica para una clase, un examen, un libro o un problema que quiere resolver.
---

# Figuras con QuickFigen

QuickFigen dibuja desde un script (el QF DSL): las construcciones quedan vivas
y exactas, y las fuerzas de un diagrama de cuerpo libre se deducen de los
apoyos. No hay que calcular coordenadas a mano ni dibujar a ojo.

## Flujo

1. **Elige la gramática** (una por script):
   - `geometry`: regla y compás — puntos, rectas, circunferencias, polígonos,
     cruces, centros del triángulo, ángulos, marcas y medidas vivas.
   - `math2d`: el plano cartesiano — funciones, paramétricas, cónicas, áreas,
     inecuaciones y deslizadores.
   - `physics`: cuerpos, planos, poleas, cuerdas, resortes, fuerzas y su
     diagrama de cuerpo libre; lentes, espejos y refracción.

   Si piden algo que QuickFigen todavía no dibuja (circuitos, campos y
   cargas), díselo a la persona en lugar de improvisarlo con otra gramática.
2. **Lee la referencia** con `qf_dsl_reference` (no gasta cuota) antes del
   primer script de esa gramática en la conversación.
3. **Escribe el script**: un enunciado por línea, cada objeto con nombre. Un
   punto que sale de una construcción se construye (`foot`, `midpoint`,
   `intersection`, `on_circle`), no se calcula afuera. El aspecto (colores,
   grosores, ocultar letras) va en un bloque `/qf-style` al final.
4. **Revisa** con `qf_check_figure`: compila igual, te devuelve la figura como
   imagen y no la muestra en el chat. Úsalo mientras corriges, para no dejarle
   a la persona una vista por cada intento. Si la respuesta es un error:
   - `kind: "invalid"`: corrige la línea que nombra el mensaje y vuelve a
     llamar.
   - `kind: "other_grammar"`: vuelve a llamar con el dominio sugerido.
   - `kind: "unsupported"`: eso todavía no se puede dibujar; díselo a la
     persona.
5. **Mira la imagen y los avisos** antes de mostrarla:
   - `ramas`: cada cruce o tangente con dos candidatos dice cuál tomó la regla
     y dónde quedó el otro. Confirma que sea el que pide el enunciado; si no,
     cambia la regla (`cerca_de=P`, `lejos_de=P`, `arriba`…).
   - `labelOverlaps` y las notas `label_overlap`: un rótulo que no encontró
     lugar libre. Se lee por su halo; para despejarlo, agranda el arco
     (`radio=1.5`) o pon la medida dentro (`rotulo=dentro`), o mueve la nota.
6. **Verifica** con `qf_measure` antes de afirmar un resultado numérico: da
   las medidas exactas, sin redondear, para compararlas con la solución
   analítica.
7. **Muestra** la figura terminada con `qf_draw_figure`: la vista de QuickFigen
   la dibuja en el chat.
   - `editLink: true` trae un link para retocarla a mano en el editor.
   - `format: "png"` la da como imagen (`escala: 2` o `4` para imprimirla);
     `format: "pdf"`, como PDF vectorial; `fondo: "transparente"`, sin el
     papel.
   - `respuesta: "resumen"` ahorra el SVG en la respuesta cuando no hace
     falta leerlo.

## Errores frecuentes

- Los nombres no distinguen mayúsculas: `h = segment(C, H)` choca con el punto
  `H`. Usa otro nombre (`hC`).
- El aspecto no va dentro de los constructores: `segment(C, H, dashed)` sí,
  pero el color va en `/qf-style` (`hC { color: rojo }`).
- En física no declares el peso, la normal ni el roce con `force()`:
  `fbd(A)` los deduce de los apoyos.

## Ejemplos

Triángulo con su altura punteada en rojo, el ángulo en A (63°) y una nota con
la medida viva de la altura («CH = 4 u»):

```qf-geometry
A = point(0, 0)
B = point(6, 0)
C = point(2, 4)
T = triangle(A, B, C)
H = foot(C, A, B)
hC = segment(C, H, dashed)
alfa = angle(B, A, C)
note((3, -1.2), "CH = {{dist(C, H)}}")
/qf-style
hC { color: rojo }
```

Con ese mismo script (sin el bloque `/qf-style`), `qf_measure` y
`medidas: ["H", "dist(C, H)", "angle(B, A, C)", "area(T)"]` devuelve
H = (2, 0), 4 u, 63,434948…° y 12 u².

La parábola y = x² − 2, gruesa y azul, con el área entre la curva y el eje x
sombreada de −1 a 1:

```qf-math2d
window x: [-3, 3], y: [-3, 6]
f = plot(x^2 - 2)
S = area(f, [-1:1])
/qf-style
f { color: azul; grosor: grueso }
```

Un bloque de 4 kg sobre una rampa de 30° con roce; `fbd(A)` dibuja el peso,
la normal y el roce:

```qf-physics
rampa = ramp(30)
A = block(4kg, "m")
on(A, rampa, at=0.55, mu=0.3)
fbd(A)
```

## Cuota

Cada dibujo, cada revisión y cada medición usan una unidad de la cuota mensual
de la cuenta de QuickFigen; la referencia no usa ninguna. Si la cuota se agota, la
respuesta dice cuándo se renueva: díselo a la persona.
