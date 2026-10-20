# legal-chile — Skill para Claude Code

Skill de cumplimiento legal chileno para sitios web y aplicaciones.
Audita el código fuente, aplica las tres leyes principales y genera la
documentación legal lista para publicar.

## Qué hace

1. **Pregunta los datos del titular** (razón social, RUT, domicilio, correo) antes de auditar.
2. **Audita el código** en busca de qué datos se recogen, qué rastreadores hay, qué pasarelas de pago están activas y si hay datos sensibles.
3. **Aplica el marco legal** correspondiente: Ley 21.719, Ley 19.628 y Ley 19.496.
4. **Genera tres archivos**:
   - `estado-legal.md` — informe de cumplimiento con semáforo
   - `politica-privacidad.md` — política redactada con datos reales del código auditado
   - `terminos-y-condiciones.md` — términos (solo si el sitio cobra o contrata)

## Leyes cubiertas

| Norma | Vigencia |
|---|---|
| **Ley 21.719** de datos personales | 1 de diciembre de 2026 — multas hasta 20.000 UTM |
| **Ley 19.628** de datos personales (vigente) | Hasta el 1 dic 2026 |
| **Ley 19.496 + DS 6/2021** del consumidor y comercio electrónico | Hoy |

## Instalación

1. Copia `SKILL.md` en `~/.claude/skills/legal-chile/SKILL.md`
2. En Claude Code, actívala con `/legal-chile` o escribe cualquier disparador

```bash
mkdir -p ~/.claude/skills/legal-chile
curl -sL https://raw.githubusercontent.com/l4mbdevs/skill-legal-chile/main/SKILL.md \
  -o ~/.claude/skills/legal-chile/SKILL.md
```

## Ejemplo de resultado

Al ejecutar la skill sobre un proyecto, genera un informe como este:

---

### `estado-legal.md` — extracto

```markdown
# Estado de Cumplimiento Legal — Tienda Ejemplo SpA
Revisado el 2026-10-20

## ✓ Resuelto en el código

- ✓ HTTPS activo con redirección forzada desde HTTP
- ✓ Contraseñas con bcrypt (src/auth/hash.ts:14)
- ✓ Datos de tarjeta no pasan por el servidor (redirect a Transbank)

## Pendientes del cliente

1. 🔴 No existe página de política de privacidad publicada
2. 🔴 Footer no muestra razón social ni RUT
3. 🟠 Formulario de contacto no tiene aviso de privacidad antes del botón
4. 🟠 Links "Política" y "Términos" apuntan a href="#" (no accesibles)
5. 🟡 Definir correo dedicado para ejercer derechos de privacidad
6. 🟡 Documentar política de cancelación con plazos concretos

## Hallazgos del código

| # | Hallazgo | Dónde | Qué hacer |
|---|---|---|---|
| 1 | Google Analytics activo — requiere banner de cookies | src/layouts/Base.astro:8 | Implementar banner con opción de rechazar |
| 2 | Resend (EE.UU.) recibe nombre y correo del formulario | src/api/contacto.ts:48 | Declarar como tercero en la política |
| 3 | Webpay y Flow ambos integrados — solo Flow activo en producción | src/payments/ | Declarar solo Flow en la política |

## Prioridades

| Prioridad | Ítem | Fecha límite |
|---|---|---|
| 🔴 Bloqueante | Publicar política de privacidad | Antes de cualquier lanzamiento |
| 🔴 Bloqueante | Mostrar razón social y RUT | Antes de cualquier lanzamiento |
| 🟠 Antes de publicar | Aviso en formulario + links funcionales | Antes de publicar |
| 🟡 Antes del 1 dic 2026 | Banner de cookies | Antes de la Ley 21.719 |
```

---

### `politica-privacidad.md` — extracto

```markdown
# Política de Privacidad

**Última actualización:** octubre de 2026

## 1. Quién trata tus datos

**Tienda Ejemplo SpA**, RUT 76.123.456-7, con domicilio en Av. Providencia 1234,
Santiago. Contacto para privacidad: privacidad@ejemplo.cl

## 2. Qué datos recogemos

| Categoría | Dato | Origen |
|---|---|---|
| Identificación | Nombre completo | Formulario de contacto y compra |
| Contacto | Correo electrónico | Formulario de contacto y compra |
| Transacción | Monto y referencia de pago | Flow (pasarela de pago) |

## 7. Con quién compartimos tus datos

| Proveedor | Finalidad | País | Base legal |
|---|---|---|---|
| Flow S.A. | Procesamiento de pagos | Chile | Ejecución del contrato |
| Resend Inc. | Envío de correos transaccionales | EE.UU. | Interés legítimo |
| Vercel Inc. | Hospedaje del sitio web | EE.UU. | Interés legítimo |
```

---

## Semáforo de cumplimiento

| Símbolo | Significado |
|---|---|
| ✓ | Resuelto en el código |
| 🟡 | Pendiente del cliente (dato o decisión faltante) |
| 🟠 | Requiere corrección antes de publicar |
| 🔴 | Bloqueante — sin esto el sitio no cumple |

## Licencia

MIT — úsala, modifícala y compártela libremente.

---

Desarrollado por [L4MB](https://l4mb.cl) · Desarrollo e IA para empresas chilenas.
