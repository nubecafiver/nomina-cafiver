# Nómina CAFIVER — Tableros

Tableros web de nómina alimentados desde el extractor ABAP de SAP HCM.
Reconciliación exacta contra `/560` (Importe de pago).

- **`index.html`** — portada con acceso a los dos tableros.
- **`director.html`** — indicadores ejecutivos, conceptos y recibo CFDI.
- **`operador.html`** — control previo al timbrado: incidencias, descuadres vs `/560`, comparación entre quincenas.
- **`firebase-config.js`** — configuración del proyecto Firebase (único lugar a editar).
- **`firestore.rules`** — reglas de seguridad (restringen la nómina a correos autorizados).

---

## 1. Iniciar sesión

Ya está conectado al proyecto Firebase **`gantttracker-cafiver`** (los mismos usuarios
y contraseñas de tus apps de Gantt e inventario).

Para tener un acceso por rol, crea estos dos usuarios en
**Firebase Console → Authentication → Users → Agregar usuario**:

| Rol      | Correo sugerido            |
|----------|----------------------------|
| Director | `director@cafiver.com.mx`  |
| Operador | `operador@cafiver.com.mx`  |

(También puedes entrar con cualquier usuario que ya exista.)

> El correo viene prellenado en cada tablero; solo se escribe la contraseña.

---

## 2. Seguridad de la nómina (IMPORTANTE)

La nómina contiene datos confidenciales (RFC, CURP, CLABE, sueldos — LFPDPPP).
Como el proyecto es compartido, aplica las reglas incluidas para que **solo** los
correos autorizados puedan leer la colección `nominaRows`:

1. Firebase Console → **Firestore Database → Reglas**.
2. Pega el contenido de **`firestore.rules`** (ajusta la lista de correos).
3. **Publicar**.

Recomendado además: en **Google Cloud Console → Credenciales**, restringe el
`apiKey` por **referente HTTP** al dominio donde publiques (ver paso 3).

> Si prefieres aislar por completo la nómina, crea un proyecto Firebase
> **dedicado** y pega sus 6 valores en `firebase-config.js`. No se toca nada más.

---

## 3. Publicar en GitHub Pages

### a) Subir el repositorio
**Opción rápida (web):** en GitHub → *New repository* → nombre `nomina-cafiver`
→ *Add file → Upload files* → arrastra todos estos archivos → *Commit*.

**Opción consola (git):**
```bash
cd nomina-cafiver
git init
git add .
git commit -m "Tableros de nómina CAFIVER"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/nomina-cafiver.git
git push -u origin main
```

### b) Activar Pages
Repositorio → **Settings → Pages** → *Source: Deploy from a branch* →
**Branch: `main` / root** → *Save*.

En 1–2 minutos queda en:
```
https://TU_USUARIO.github.io/nomina-cafiver/
```

### c) Autorizar el dominio en Firebase
Firebase Console → **Authentication → Settings → Dominios autorizados** →
agrega `TU_USUARIO.github.io`. (Si no, el login marcará dominio no autorizado.)

---

## 4. Cargar la nómina

En cualquiera de los dos tableros: botón **+ Cargar CSV** y selecciona el archivo
que genera el extractor ABAP. Se guarda en Firestore y queda disponible para ambos.
Para comparar quincenas, carga dos o más períodos.

> El `.gitignore` evita subir CSVs reales al repositorio. **Nunca** publiques
> archivos de nómina en GitHub: los datos viven en Firebase, no en el repo.
