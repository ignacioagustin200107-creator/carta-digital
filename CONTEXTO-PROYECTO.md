# Proyecto: Carta digital con QR para restaurantes

Documento de contexto para retomar el proyecto (con otra persona o con otra IA).
Última actualización: 14/11/2026.

## Resumen
Ignacio Rodriguez (estudiante de la Tecnicatura en Programación, UGR) está armando una **carta de menú virtual** para restaurantes. Primero una
**demo de hamburguesería** ("La Brasa Burger") para mostrar a posibles clientes y, a partir de ella, una
**versión general (plantilla)** para vender a cualquier restaurante. Solo cambian el logo, los colores y los productos.

## Decisiones tomadas
| Tema | Decisión |
|---|---|
| Formato | Página **web que se abre con un QR** (en la mesa o en el mostrador). Funciona sin instalar nada y está pensada primero para el celular. |
| Funciones | Fotos de los platos, **panel para que el dueño edite** precios y productos, y **promos/combos destacados**. |
| Quién actualiza | **El propio local**, desde el panel (sin tocar código). |
| Material del cliente | Todavía no hay: la demo usa contenido de ejemplo inventado. |
| Uso de la demo | Mostrarla a clientes; el dueño puede **cambiar el diseño a gusto**. |
| Código | Repo de GitHub **público**: `ignacioagustin200107-creator/carta-digital` (rama `main`). |
| Hosting | **Cloudflare Pages** (plan gratis, permite uso comercial y tráfico ilimitado). Se descartó Vercel Hobby y GitHub Pages porque prohíben el uso comercial. |
| Dominio | **irodev.com.ar** en NIC Argentina (AR$ 8.500 por año). Disponible; compra en curso. Un solo dominio para todos los proyectos, con subdominios. |

## Infraestructura (cómo se publica)
```
Pedido de Ignacio → Claude + agentes → GitHub (commit) → Cloudflare Pages (publica solo) → dominio → QR
```
- **Repo:** `https://github.com/ignacioagustin200107-creator/carta-digital`
  - `index.html`: toda la carta (HTML + CSS + JS en un solo archivo, sin dependencias salvo Google Fonts).
  - `CLAUDE.md`: reglas del proyecto (textos en español rioplatense, pensado para celular, un cambio por commit).
  - `.claude/agents/`: equipo de 4 agentes de Claude Code con roles fijos:
    1. `disenador`: propone cómo se ve y se usa (no escribe código).
    2. `programador`: implementa en `index.html`.
    3. `revisor`: revisa el código (errores, seguridad con `esc()`, que no se rompan otros formatos).
    4. `tester`: prueba como cliente y como dueño en los 5 formatos y 3 vistas.
  - `.nojekyll` y `README.md`.
- **Cloudflare Pages:** conectado al repo, rama de producción `main`, sin build command (sitio estático). Cada push a `main` se publica solo en 1–2 minutos.
- **Plan de subdominios** (con irodev.com.ar):
  - `irodev.com.ar` → portfolio de Ignacio.
  - `carta.irodev.com.ar` → carta demo.
  - `<restaurante>.irodev.com.ar` → carta de cada cliente.
  - `almadecampo.irodev.com.ar` → tienda de Alma de Campo (repo `alma-de-campo-tienda`, todavía vacío).
- **Pasos que faltan del dominio:** comprar en nic.ar → agregarlo en Cloudflare (Onboard a domain) → cargar los nameservers de Cloudflare en nic.ar (Delegar) → en el proyecto de Pages, Custom domains → `carta.irodev.com.ar`.
- **Historial:** también se probó GitHub Pages y Vercel (hubo un error por un dominio personalizado inválido, ya corregido). Quedan como pruebas; el hosting definitivo es Cloudflare.

## Estado actual: demo funcionando
**Vista cliente**
- Encabezado con logo, nombre, slogan, horario, dirección y WhatsApp.
- Banner de promo (se puede prender y apagar).
- Categorías en chips que se quedan fijos arriba al bajar y llevan a cada sección.
- Sección de recomendados automática.
- Productos con foto (o emoji si no hay foto), descripción y precio en ARS.
- Etiqueta "Sin stock" (el producto se ve atenuado). Ofertas con `precioAntes`: "-X%", precio tachado y "Ahorrás $X".
- Al tocar un producto se abre una ficha de detalle (animación FLIP, se cierra deslizando hacia abajo). Buscador en vivo.

**Modo dueño** (botón "⚙️ Modo dueño", PIN de la demo: `1234`). Tiene 4 pestañas:
1. **Diseño**: código de formato, 10 plantillas, personalidad, efecto de tarjetas, colores, tipografías, bordes, distribución, mostrar u ocultar fotos/descripciones/animaciones, restablecer la demo.
2. **Local**: logo (o emoji), nombre, slogan, horario, dirección y WhatsApp.
3. **Promo**: activar o desactivar, y editar emoji, título y texto.
4. **Productos**: editar nombre, descripción, precio, precio anterior, categoría, foto, disponible y recomendado; agregar y eliminar productos y categorías.

**Motor de estilos**
- **Personalidades** (`body.fmt-*`): `clasico`, `lujo`, `noche`, `delivery`, `cristal`.
- **Efecto de tarjetas** (`body.card-*`): `sombra`, `neumorfismo`, `vidrio`, `borde`. **Distribución** (`body.layout-*`): `grilla`, `lista`, `grande`.
- **Variables CSS**: `--pri`, `--bg`, `--card`, `--txt`, `--onpri` (contraste automático), `--r`, `--fb`, `--ft`.
- **Código de formato** (`S.estilo.codigo`): códigos base CLASICO, LUJO, NOCHE, DELIVERY, CRISTAL, ORO, NEON, PIZARRA, FRESH, SUAVE, más ajustes `clave=valor` (formato, color, fondo, superficie, texto, letra, titulos, radio 0-32, vista, efecto, anim/fotos/desc si/no). Ejemplo: `NEON vista=lista radio=28 color=#ff00aa`.

**Detalles técnicos**
- Todo el estado está en un objeto `S` (`local`, `estilo`, `promo`, `categorias`, `productos`).
- Se guarda en `localStorage` (clave `cartaDemo_v2`, migra datos de `cartaDemo_v1`).
- Todo texto del dueño pasa por `esc()` antes de ir al HTML.

## Limitación importante
La demo guarda los cambios **solo en el navegador de quien edita**. Si el dueño cambia un
precio, sus clientes no lo ven. Además, el PIN está en el código, así que no es seguro.
**Para usarla con clientes reales falta un backend.**

## Plan de trabajo (en orden)
1. ✅ Repos en GitHub, carta subida, equipo de agentes armado.
2. ✅/⏳ Hosting en Cloudflare Pages (cuenta creada y repo conectado).
3. ⏳ Dominio irodev.com.ar: compra en nic.ar y conexión a Cloudflare.
4. Forma de trabajo segura: cada cambio en una rama + pull request con link de vista previa de Cloudflare; los agentes revisan; Ignacio aprueba.
5. Datos reales por restaurante: backend + base de datos (por ejemplo Cloudflare D1/KV, Supabase o Firebase) y login real del dueño.
6. Funciones nuevas: pedido por WhatsApp, agregados/variantes de producto, horario abierto/cerrado, generador de QR imprimible, multi-restaurante.

## Modelo de negocio propuesto (todavía no confirmado)
- **Armado inicial** (pago único): diseño, carga de la carta y QR imprimible.
- **Abono mensual**: hosting, panel y soporte.
- **Extras / plan premium**: pedidos por WhatsApp, fotos profesionales y diseño a medida.
- Si un restaurante quiere su propio dominio, lo paga el restaurante y se apunta al mismo proyecto.

## Preguntas abiertas
- ¿Cuál es el precio del armado y del abono?
- ¿La hamburguesería va a ser un cliente real? Si es así, falta su nombre, logo, fotos y carta.
- ¿Qué funciones nuevas van primero?
- ¿Qué backend se usa para los datos compartidos?

## Cómo colaborar sin pisarse
- Todo cambio de código pasa por GitHub; el repo es la fuente de verdad.
- Sugerencias de otras IAs: se le pasan a Claude, que las hace pasar por el revisor antes de subirlas.
