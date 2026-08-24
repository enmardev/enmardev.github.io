# enmardev.github.io

Sitio interino de GitHub Pages para el App Link de Android de Ready2Check.

## Por qué existe este repo

Android verifica los App Links (`autoVerify: true`) descargando
`https://<host>/.well-known/assetlinks.json` desde la **raíz** del host. El
repo `ready2check-privacy` es un *project page* (solo sirve bajo
`/ready2check-privacy/*`), así que no puede alojar ese archivo. Este repo,
al llamarse exactamente `enmardev.github.io`, es un *user site* y sirve
desde la raíz.

Cuando se contrate un dominio propio, migrar consiste en mover
`.well-known/assetlinks.json` + `app-user/index.html` al nuevo host y
actualizar `host` en el intent-filter de `app.json` (vehicle-checklist-app)
y `APP_LINK_BASE_URL` (secret de la edge function `send-app-user-code`).

## Archivos

- `.nojekyll` — necesario para que GitHub Pages sirva `.well-known/` (Jekyll
  omite por defecto las carpetas que empiezan con punto).
- `.well-known/assetlinks.json` — declaración de App Link para
  `com.enmardev.ready2check`. Los `sha256_cert_fingerprints` deben
  completarse con:
  - El certificado de **Play App Signing** (Play Console → Configuración →
    Integridad de la app → "Certificado de la clave de firma de la app" →
    huella SHA-256). Solo existe una vez que la app se subió a Play.
  - El del keystore de EAS: `eas credentials -p android` → perfil que se
    esté probando (ej. `preview`) → "Keystore" → SHA-256 Fingerprint. Sirve
    para probar el App Link con builds internos antes del release en Play.
- `app-user/index.html` — landing de respaldo cuando el link se abre sin la
  app instalada (redirige a Play Store).

## Verificar que está online

```bash
curl -I https://enmardev.github.io/.well-known/assetlinks.json
```

Debe devolver `200` con `content-type: application/json` **antes** de
instalar un build de la app con el intent-filter — Android solo verifica el
dominio en el momento de la instalación/actualización, no reintenta después.
