# QuickFigen para Claude

Figuras de matemática y física con calidad de libro, dibujadas por el motor de
[QuickFigen](https://www.quickfigen.com) desde un script: geometría con regla
y compás, funciones en el plano cartesiano, cuerpos y fuerzas, óptica. Las
construcciones son exactas y siguen vivas (un punto medio sigue siendo el
punto medio), y las fuerzas de un diagrama de cuerpo libre se deducen de los
apoyos. Nada se dibuja a ojo.

El plugin conecta Claude con el servidor MCP de QuickFigen y le enseña a
usarlo con tres skills:

| Skill | Para qué |
|---|---|
| `figuras-quickfigen` | Dibujar una figura, revisar sus cruces y rótulos, y verificar sus medidas. |
| `explicar-paso-a-paso` | Explicar un problema de geometría momento a momento, con la figura que se construye. |
| `boletin-verificado` | Armar un boletín de problemas con la clave comprobada, en versión alumno y profesor. |

## Herramientas del servidor

| Herramienta | Qué hace | Unidades de la cuota |
|---|---|---|
| `qf_dsl_reference` | Devuelve la referencia del lenguaje de figuras (QF DSL) de una gramática. | 0 |
| `qf_draw_figure` | Dibuja un script y devuelve la figura en SVG o PNG; la muestra en el chat. | 1 |
| `qf_check_figure` | Dibuja un script y le devuelve a Claude la imagen y los avisos del motor, sin mostrarla en el chat, para revisarla antes. | 1 |
| `qf_measure` | Mide una figura de geometría o de QF 3D: coordenadas, distancias, ángulos, áreas y volúmenes exactos. | 1 |
| `qf_explain` | Compila una explicación paso a paso y la muestra navegable en el chat; si Claude lo pide, le devuelve además la imagen de hasta tres cuadros para revisarlos. | 1 |
| `qf_booklet` | Arma un boletín verificado como HTML listo para imprimir. | 1 más 1 por problema |

Cada cuenta de QuickFigen tiene una cuota mensual de unidades según su plan
(ver [Precios](https://www.quickfigen.com/pricing)); dibujar no usa
inteligencia artificial ni créditos de QuickFigen.

## Instalación

Hace falta una cuenta de QuickFigen. Al conectar, se abre QuickFigen para
iniciar sesión y autorizar la conexión (OAuth); la autorización se ve y se
revoca en Configuración → API y MCP → Aplicaciones conectadas.

**Claude Code:**

```
/plugin marketplace add t2y001/quickfigen-plugin
/plugin install quickfigen@quickfigen
```

Después, `/mcp`, elige `quickfigen` y sigue el inicio de sesión en el
navegador.

**claude.ai:** los pasos para agregar QuickFigen como conector están en
[quickfigen.com/conectar](https://www.quickfigen.com/conectar#claude).

## Qué datos se envían

El plugin no ejecuta código en tu equipo: son instrucciones para Claude
(los skills) y la dirección del servidor, `https://www.quickfigen.com/api/mcp`.
Cuando Claude llama a una herramienta, envía a QuickFigen solo lo que esa
herramienta necesita:

- **Dibujar, revisar y medir:** el script de la figura, su gramática y las
  opciones (tamaño, formato, medidas pedidas).
- **Explicar:** la explicación completa (enunciado, datos, pasos, narración y
  figura).
- **Boletín:** los problemas (enunciados, figuras, alternativas, clave y
  resoluciones) y el encabezado, incluido el logo si se envía.

La conversación con Claude no se envía: solo lo que recibe cada herramienta.
QuickFigen procesa esos datos para responder, sin pasarlos por inteligencia
artificial ni enviarlos a proveedores de IA, y guarda un registro de cada
llamada (fecha, herramienta, unidades, si salió bien o el tipo de error, y
cuánto tardó) para la cuota y la seguridad.

Lo que queda guardado o viaja en un link:

- Una explicación con `viewLink: true` se guarda en la cuenta de quien la
  publica, con un link de solo lectura que cualquier persona que lo tenga puede
  abrir sin cuenta. Vence según la cuenta (30 días si no se cambió) y se puede
  revocar en Configuración.
- El link de edición (`editUrl`) lleva el script dentro del link; no se guarda
  nada hasta que la persona guarde la figura en el editor.
- Los links de un boletín (`viewLink: true`) llevan el boletín firmado dentro
  del link: no se guarda nada y vencen a los 7 días. El link de la versión
  profesor muestra la clave a quien lo tenga.

Más detalle en la [Política de privacidad](https://www.quickfigen.com/legal/privacy)
y los [Términos](https://www.quickfigen.com/legal/terms).

## Soporte

Escribe a [soporte@quickfigen.com](mailto:soporte@quickfigen.com). La guía de
conexión está en [quickfigen.com/conectar](https://www.quickfigen.com/conectar).

## Licencia

Este plugin (los skills, el manifiesto y esta documentación) se publica con
licencia [Apache-2.0](LICENSE). La licencia no da permiso para usar el nombre
QuickFigen ni su logo como marca (sección 6 y [NOTICE](NOTICE)). El motor de
QuickFigen es un servicio y no forma parte de este repositorio.

---

## English summary

QuickFigen draws textbook-quality math and physics figures from a short
script: ruler-and-compass geometry, function graphs on the Cartesian plane,
bodies and forces with free-body diagrams, and optics. This plugin connects
Claude to the QuickFigen MCP server (`https://www.quickfigen.com/api/mcp`,
OAuth sign-in with a QuickFigen account) and adds three skills: drawing and
verifying figures, step-by-step geometry explanations, and verified problem
booklets with student and teacher versions. When Claude calls a tool, only the
tool's input (the figure script, the explanation or the booklet) is sent to
QuickFigen; the conversation is not. QuickFigen does not run these calls
through AI models and keeps a per-call log (date, tool, units, outcome,
duration). Shared explanation links are stored in the publisher's account and
can be revoked. See the [privacy policy](https://www.quickfigen.com/legal/privacy).
Support: [soporte@quickfigen.com](mailto:soporte@quickfigen.com).
Licensed under Apache-2.0; the QuickFigen name and logo are not licensed.
