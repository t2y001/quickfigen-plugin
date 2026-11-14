---
name: boletin-verificado
description: Arma con QuickFigen un boletín de problemas de matemática o física con figuras, alternativas y clave verificadas por el motor, en versión alumno y profesor, como HTML listo para imprimir o guardar como PDF. Usar cuando la persona pide una práctica, una hoja de ejercicios, un examen o un boletín con respuestas comprobadas.
---

# Boletines verificados con QuickFigen

`qf_booklet` arma un boletín de hasta 20 problemas y comprueba cada uno: la
clave contra lo que mide la figura, que haya una sola alternativa correcta,
los datos del enunciado medidos en la figura y que la respuesta no cambie al
mover los puntos libres. Un problema que pasa todo lleva «Verificado por QF».

## Flujo

1. **Resuelve cada problema** y escribe su figura con el QF DSL (lee
   `qf_dsl_reference` de la gramática antes del primer script).
2. **Declara cómo se comprueba**, siempre que haya figura:
   - `measure`: qué medir para comprobar la clave (un ángulo, una longitud,
     una razón, un área, un valor o una expresión).
   - `givens`: los datos del enunciado medidos en la figura.
   - `free`: los puntos que se pueden mover sin cambiar la respuesta (`[]` si
     no hay).

   Sin `measure`, `givens` o `free`, el problema queda «partial», no
   verificado.
3. **Llama a `qf_booklet`.** Si algo no se puede armar, el error trae el
   número de problema, la ruta (`problems[0].figure.script[6]`), la línea, qué
   pasó y qué hacer: corrige eso y vuelve a llamar.
4. **Revisa el resumen**: el estado de cada problema (`verified`, `partial`,
   `failed`, `unverified`) con sus comprobaciones. Si alguno no quedó
   verificado, díselo a la persona con el motivo.
5. **Entrega**: el HTML de cada versión se imprime o se guarda como PDF desde
   el navegador. Con `viewLink: true` hay links firmados que vencen en 7 días.
   El link de la versión profesor muestra la clave a quien lo tenga: díselo a
   la persona antes de que lo comparta.

## Ejemplo

Un problema de ángulo inscrito con cuatro alternativas; queda verificado (la
clave, las alternativas, el dato de 100° y el movimiento de P).

```json qf_booklet
{
  "title": "Ángulos en la circunferencia",
  "layout": {
    "columns": 1
  },
  "problems": [
    {
      "name": "Ángulo inscrito",
      "statement": "En la figura, $O$ es el centro de la circunferencia y $\\angle AOB = 100^\\circ$. Calcule $x = \\angle APB$.",
      "figure": {
        "domain": "geometry",
        "script": [
          "O = point(0, 0)",
          "c = circle(O, 3)",
          "A = on_circle(c, 220)",
          "B = on_circle(c, 320)",
          "P = on_circle(c, 90)",
          "sOA = segment(O, A)",
          "sOB = segment(O, B)",
          "sPA = segment(P, A)",
          "sPB = segment(P, B)",
          "central = angle(A, O, B)",
          "x = angle(A, P, B, x)"
        ]
      },
      "choices": [
        "$40^\\circ$",
        "$50^\\circ$",
        "$80^\\circ$",
        "$100^\\circ$"
      ],
      "answer": {
        "choice": "B",
        "value": 50,
        "unit": "°",
        "tolerance": 0.5
      },
      "measure": {
        "kind": "angle",
        "vertex": "P",
        "from": "A",
        "to": "B"
      },
      "givens": [
        {
          "label": "ángulo AOB",
          "expression": "angulo(A, O, B)",
          "value": 100,
          "unit": "°"
        }
      ],
      "free": [
        {
          "point": "P",
          "from": 30,
          "to": 150
        }
      ],
      "solution": "El ángulo inscrito mide la mitad del central que abarca el mismo arco: $x = 100^\\circ / 2 = 50^\\circ$."
    }
  ]
}
```

## Cuota

Un boletín usa una unidad más una por problema de la cuota mensual de la
cuenta de QuickFigen.
