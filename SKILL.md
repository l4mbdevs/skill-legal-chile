---
name: legal-chile
description: >-
  Aplica los requisitos legales chilenos a un sitio web o app: Ley 21.719 de
  datos personales (vigente el 1 de diciembre de 2026), Ley 19.628, y Ley
  19.496 del consumidor con su reglamento de comercio electrónico. Úsala al
  revisar qué falta para cumplir, al redactar o actualizar la política de
  privacidad y los términos y condiciones, antes de publicar un sitio que
  recoge datos o cobra en línea, al agregar un formulario, un pago, un
  registro de usuarios o cookies. Disparadores: "legal", "aplica legal",
  "política de privacidad", "términos y condiciones", "cookies", "banner de
  cookies", "datos personales", "Ley 21719", "SERNAC", "consumidor",
  "retracto", "GDPR en Chile", "privacidad", "cumplimiento", "qué exige la ley",
  "cumplimiento legal", "revisión legal", "Ley 21.719".
---

# legal-chile — cumplir de verdad, no poner un texto de adorno

La mayoría de los "generadores de políticas de privacidad" producen un documento
que nadie leyó y que no describe lo que el sistema hace. Eso no protege a nadie
y, frente a un reclamo, es peor que no tener nada: prueba que se declaró algo
distinto de lo que ocurre.

El trabajo real es al revés: **primero se averigua qué datos toca el sistema,
después se escribe lo que corresponde.**

---

## Paso 0 — Pedir los datos del titular (recomendado, no obligatorio)

Antes de auditar el código, usa `AskUserQuestion` con **4 preguntas simultáneas**
para recopilar los datos que la ley exige declarar. Son opcionales — sin ellos
los documentos quedan con `[corchetes]` — pero con ellos salen listos para
publicar.

**REGLA TÉCNICA:** cada pregunta necesita mínimo 2 opciones. Usa siempre al
menos "Tengo el dato → escríbelo en Otro" + "No lo sé aún". Si la pregunta
tiene opciones naturales (ej. política de cancelación), úsalas directamente.

```
pregunta 1 — header: "Titular"
  "¿Cuál es la razón social y RUT del responsable del sitio?"
  opciones:
    - "No lo sé aún"  → deja [Razón social] y [RUT]
    - "Tengo el dato" → el usuario escribe en Otro

pregunta 2 — header: "Dirección"
  "¿Cuál es el domicilio legal y la ciudad?"
  opciones:
    - "No lo sé aún"  → deja [dirección], [ciudad]
    - "Tengo el dato" → el usuario escribe en Otro

pregunta 3 — header: "Contacto privacidad"
  "¿Qué correo usarán para que los usuarios ejerzan derechos de privacidad?"
  opciones:
    - "El correo general del sitio" → busca el correo en el código
    - "No lo sé aún"               → deja [correo de contacto]

pregunta 4 — header: "Cancelación"
  "¿Tiene el servicio política de cancelación o abonos?"
  opciones:
    - "No aplica / sin abono"  → omite la cláusula
    - "Sí, hay abono previo"   → el usuario describe en Otro
    - "No lo sé aún"           → deja [política que defina la empresa]
```

Si el usuario responde con "Otro", usa ese texto directamente en los documentos.
Si elige "No lo sé aún", deja el campo entre corchetes.
Continúa al Paso 1 sin importar si respondió o no.

---

## Paso 1 — Auditoría del código

Antes de redactar una línea, lee el código. Corre estos comandos desde la
raíz del proyecto (ajusta la extensión si el stack es diferente):

```bash
# ── Qué datos le pide al visitante ──────────────────────────────────────────
grep -rn 'placeholder=\|type="email"\|type="tel"\|name="rut"\|name="telefono"\|name="nombre"\|name="direccion"' \
  --include="*.html" --include="*.astro" --include="*.vue" --include="*.php" . \
  | grep -v "node_modules\|dist\|vendor" | head -40

# ── Rastreadores de terceros (decide si hace falta banner de cookies) ────────
grep -roE "googletagmanager|google-analytics|gtag\(|facebook\.net|fbq\(|hotjar|clarity\.ms|tiktok|mixpanel|segment\.com|intercom" \
  --include="*.html" --include="*.astro" --include="*.ts" --include="*.js" . \
  | grep -v "node_modules\|dist" | sort -u

# ── Qué guarda en el navegador ───────────────────────────────────────────────
grep -roE "localStorage|sessionStorage|document\.cookie|indexedDB" \
  --include="*.html" --include="*.astro" --include="*.ts" --include="*.js" . \
  | grep -v "node_modules\|dist" | sort | uniq -c

# ── Con quién se comparten los datos (servicios externos) ────────────────────
grep -rnoE "https://[a-z0-9.-]+\.[a-z]{2,}" \
  --include="*.ts" --include="*.js" --include="*.php" --include="*.py" . \
  | grep -v "node_modules\|dist\|vendor\|w3\.org\|schema\.org\|fonts\.g" \
  | sort -u | head -30

# ── Pasarelas de pago ─────────────────────────────────────────────────────────
grep -rn "webpay\|transbank\|flow\|mercadopago\|stripe\|kushki\|khipu" \
  --include="*.ts" --include="*.js" --include="*.php" --include="*.py" \
  --include="*.astro" . | grep -v "node_modules\|dist" | head -20

# ── Datos sensibles en el código ──────────────────────────────────────────────
grep -rn "salud\|health\|diagnostico\|diagnosis\|raza\|etnia\|religion\|sindicato\|biometri\|ficha_clinica\|ficha_medica" \
  --include="*.html" --include="*.astro" --include="*.php" --include="*.ts" . \
  | grep -v "node_modules\|dist" | head -20

# ── Seguridad básica ──────────────────────────────────────────────────────────
grep -rn "password_hash\|bcrypt\|argon2\|hashpw" \
  --include="*.php" --include="*.py" --include="*.ts" --include="*.js" . \
  | grep -v "node_modules\|dist" | head -10
grep -rn "md5\|sha1\|base64" \
  --include="*.php" --include="*.py" . | grep -i "password\|clave\|pass" \
  | grep -v "node_modules" | head -10
```

Para cada dato encontrado anota: **qué se recoge · para qué · dónde se guarda ·
con quién se comparte · cuánto dura**. Esa tabla es el registro de tratamientos
que la Ley 21.719 exige poder mostrar.

---

## Paso 2 — Marco legal aplicable

### Las tres leyes

| Norma | Qué exige | Vigencia |
|---|---|---|
| **Ley 21.719** | Política de privacidad, base legal por tratamiento, derechos ARCOP + portabilidad + bloqueo, aviso de brechas en **72 h** a la Agencia y a los afectados, consentimiento explícito y desmarcado por defecto para lo no necesario, contratos escritos con encargados (proveedores), registro de tratamientos | **1 dic 2026** — multas hasta **20.000 UTM** (~$1.300 M CLP) |
| **Ley 19.628** | Finalidad declarada; datos de salud como sensibles | Vigente hasta el 1 dic 2026 |
| **Ley 19.496 + DS 6/2021** | Identidad del proveedor visible, precio total con impuestos, condiciones accesibles **antes** de contratar, confirmación escrita, retracto **10 días corridos** (90 si no hubo confirmación) | **Hoy** |

### Bases legales disponibles (Ley 21.719 art. 13)

No todo tratamiento necesita consentimiento. Usa la base que corresponda:

| Base | Cuándo aplica |
|---|---|
| **Ejecución del contrato** | Datos necesarios para prestar el servicio que el usuario pidió |
| **Obligación legal** | Boletas, registros sanitarios, datos exigidos por el SII o el MINJU |
| **Interés legítimo** | Seguridad del sitio, logs técnicos, prevención de fraude |
| **Consentimiento** | Marketing, perfilado, analítica, cookies no técnicas. Debe ser libre, informado, específico y revocable |
| **Interés vital** | Emergencias que afecten la vida o integridad física |

**Regla práctica:** reserva el consentimiento para lo accesorio. Pedirlo para
lo imprescindible confunde al usuario y encima permite retirarlo.

### Datos sensibles (requieren base legal más fuerte)

La Ley 21.719 clasifica como sensibles: datos de salud, origen racial o étnico,
opiniones políticas, convicciones religiosas o filosóficas, afiliación sindical,
datos biométricos identificativos, datos genéticos, orientación sexual.

Si el sistema toca alguno de estos: el consentimiento debe ser **explícito y
específico** (no basta un consentimiento general), o debe existir una obligación
legal expresa. Decláralo en la política con mayor detalle.

### Transferencias internacionales

Si los datos viajan a servidores fuera de Chile (hosting en EE.UU., servicios
en la nube, proveedores SaaS), hay que:

1. Declararlo en la política indicando país de destino.
2. Mencionar que el proveedor cuenta con garantías adecuadas de seguridad.
3. Entregar solo lo indispensable al proveedor.

No se puede omitir aunque el proveedor sea "conocido" (AWS, Google, Vercel,
Resend, etc.).

---

## Paso 3 — Checklist de cumplimiento

Completa este checklist para cada proyecto. Usa el semáforo al reportar:

| Símbolo | Significado |
|---|---|
| ✓ | Resuelto en el código |
| 🟡 | Pendiente del cliente (dato, decisión o texto faltante) |
| 🟠 | Requiere corrección antes de publicar |
| 🔴 | Bloqueante — sin esto el sitio no cumple |

### Identidad legal
- [ ] Razón social, RUT y domicilio visibles en el sitio (pie de página o política)
- [ ] Correo o canal real para ejercer derechos de privacidad (que alguien lea)
- [ ] Correo o canal real para reclamos de consumidor (SERNAC puede exigirlo)

### Política de privacidad
- [ ] Existe la página (no en gris en el pie de página, sino publicada y accesible)
- [ ] Declara qué datos se recogen, por categoría
- [ ] Declara la base legal de cada tratamiento
- [ ] Tabla de terceros con nombre, finalidad y país de cada proveedor
- [ ] Menciona transferencias internacionales si las hay
- [ ] Plazos de retención por categoría de dato
- [ ] Derechos ARCOP + portabilidad + bloqueo, con instrucciones para ejercerlos
- [ ] Sección de datos sensibles (si aplica)
- [ ] Sección de menores (aunque sea para decir que el servicio es para mayores de 18)
- [ ] Sección de cookies (técnicas vs. no técnicas)
- [ ] Cómo se avisará si la política cambia

### Consentimientos y formularios
- [ ] Ninguna casilla de consentimiento viene premarcada
- [ ] El enlace a política y términos aparece **antes** del botón que envía el formulario o confirma la compra
- [ ] El formulario de contacto tiene aviso de privacidad (aunque sea una línea)
- [ ] El registro de cuenta tiene aviso antes de crear
- [ ] Si hay marketing por correo: consentimiento separado, no incluido en los de uso

### Cookies y rastreadores
- [ ] Si hay Analytics, píxeles publicitarios u otras herramientas de medición: existe banner de cookies
- [ ] El banner permite rechazar con la misma facilidad con que se acepta
- [ ] Nada se activa antes de que el usuario responda el banner
- [ ] Si no hay rastreadores: la política lo dice explícitamente

### Seguridad técnica
- [ ] HTTPS en todo el sitio, con redirección forzada desde HTTP
- [ ] Contraseñas con hash irreversible (bcrypt, argon2) — nunca MD5, SHA1 ni base64
- [ ] Datos de tarjetas: no pasan por el servidor propio (redirect a pasarela)
- [ ] Base de datos fuera del directorio público del servidor
- [ ] Panel de administración protegido con credenciales y, si es posible, 2FA
- [ ] Respaldos periódicos con procedimiento definido

### Comunicaciones transaccionales
- [ ] Confirmación escrita de cada compra o reserva con detalle y montos
- [ ] Si hay códigos de verificación por correo: el correo advierte que nunca se piden por teléfono o WhatsApp

### Registro de tratamientos (obligación Ley 21.719)
- [ ] Existe un registro interno (puede ser una planilla) que muestre: qué datos, para qué, con qué base legal, con quién se comparten, cuánto tiempo se guardan

### Proveedores (encargados de tratamiento)
- [ ] Existe un contrato o cláusula con cada proveedor que toca datos personales (hosting, correo, pasarela, analítica, soporte), indicando qué pueden hacer con los datos y qué no

### Cobro en línea (si aplica)
- [ ] Derecho a retracto de 10 días corridos declarado en los términos
- [ ] Precio total con impuestos visible antes de confirmar
- [ ] Boleta o factura electrónica emitida por cada transacción
- [ ] Política de cancelación y devolución declarada (con plazos concretos)

### Brecha de seguridad
- [ ] Existe un procedimiento documentado (aunque sea de una página) que define: a quién se avisa internamente, cómo se notifica a la Agencia de Datos en menos de 72 h, y cómo se informa a los afectados

---

## Paso 4 — Generar los documentos

### Informe de estado

Produce un archivo markdown con tres secciones:

1. **Lo que ya está resuelto** — con ✓ y evidencia del código (archivo:línea)
2. **Pendientes del cliente** — numerados, con responsable y urgencia
3. **Hallazgos del código** — tabla con columnas: # · Hallazgo · Dónde · Qué hacer

Usa el semáforo (🔴/🟠/🟡) en cada ítem. Termina con una tabla de prioridades.

### Política de privacidad

Redacta en el idioma del cliente, sin jerga jurídica. Estructura mínima:

1. Quién trata los datos (razón social, RUT, domicilio, correo)
2. Qué datos se recogen — por categoría, basado en el código auditado
3. Para qué los usamos
4. Con qué fundamento legal — base por base
5. Si hay datos sensibles — sección dedicada
6. Si eres la otra parte / tercero mencionado (si aplica al negocio)
7. Con quién los compartimos — tabla: proveedor · finalidad · país
8. Cuánto tiempo los guardamos — por categoría
9. Tus derechos — ARCOP + portabilidad + bloqueo + reclamación ante la Agencia
10. Cómo los protegemos
11. Cookies y almacenamiento en el navegador
12. Menores de edad
13. Cambios a esta política

### Términos y condiciones (si el sitio cobra o contrata)

1. Quiénes somos (identidad legal completa)
2. Qué se contrata (descripción exacta del servicio)
3. Precios y pago (moneda, impuestos, pasarela)
4. Confirmación escrita
5. Citación de terceros / otra parte (si aplica)
6. Cambios y cancelaciones (plazos concretos o [corchetes])
7. Derecho a retracto — 10 días corridos, art. 3° bis Ley 19.496
8. Obligaciones del cliente
9. Alcance y límites del servicio
10. Reclamos y SERNAC
11. Ley aplicable y tribunales

**Regla de oro:** deja entre `[corchetes]` lo que no sabes — razón social, RUT,
plazos de cancelación. Un documento con datos inventados es peor que uno
incompleto.

---

## Errores que se repiten

| Error | Por qué importa |
|---|---|
| Copiar política de otro sitio | Declara tratamientos que no existen y omite los que sí. Es prueba en contra |
| Links legales con `href="#"` | La ley exige condiciones accesibles **antes** de contratar |
| Casilla premarcada | La 21.719 exige consentimiento inequívoco: premarcada no lo es |
| Banner solo con "Aceptar" | Rechazar debe costar lo mismo que aceptar |
| "No compartimos datos con nadie" | Falso si hay hosting, correo o pasarela. Hay que nombrarlos |
| Prometer borrado total | Boletas (6 años) y fichas clínicas tienen plazos legales que mandan |
| Mencionar solo una pasarela cuando hay dos activas | El código puede tener Webpay Y Flow — verificar cuál está activa en producción |
| No declarar transferencias internacionales | Vercel, AWS, Resend, Google están en EE.UU. — hay que declararlos |
| Datos sensibles sin base legal explícita | Fichas clínicas, datos de salud o de hijos menores requieren tratamiento reforzado |

---

## Output esperado al terminar

La skill produce **3 archivos** en `docs/legal/` (o donde corresponda al proyecto):

| Archivo | Contenido |
|---|---|
| `estado-legal.md` | Informe de cumplimiento con semáforo, evidencia del código y tabla de prioridades |
| `politica-privacidad.md` (o `.html`) | Política redactada con datos reales del código auditado, sin corchetes salvo lo que el cliente deba completar |
| `terminos-y-condiciones.md` (o `.html`) | Términos redactados (solo si el sitio cobra o contrata en línea) |

---

## Perfiles de riesgo

La complejidad del análisis depende del tipo de sistema. Algunos perfiles comunes:

| Perfil | Qué implica |
|---|---|
| Solo formulario de contacto | Sin cobro ni base de datos propia — el análisis es breve |
| Agenda + cobro en línea | Requiere términos con retracto, confirmación escrita y tabla de pasarelas |
| Datos sensibles (salud, menores) | Consentimiento explícito y específico; sección dedicada en la política |
| Plataforma SaaS con usuarios | Registro de tratamientos, contratos con encargados, procedimiento de brechas |
