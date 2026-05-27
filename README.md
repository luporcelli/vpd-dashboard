# VPD Grow Dashboard

Dashboard web para monitorear y controlar tu setup de cultivo en tiempo real.
Se deploya gratis en Vercel. Al guardar la configuración desde la app, los cambios se aplican en n8n **instantáneamente** — sin tener que entrar a n8n.

## Cómo funciona la sincronización

```
[Tu dashboard] --POST /webhook/vpd-config--> [n8n guarda en staticData]
                                                      ↓
                                          [CONFIGURACION lee de staticData]
```

Cuando apretás **Guardar y sincronizar**, la app manda todos los parámetros al webhook de n8n. El workflow los guarda en `staticData` (memoria persistente). La próxima vez que corre el Schedule (cada 25s), lee esos valores actualizados automáticamente.

---

## Estructura del proyecto

```
vpd-dashboard/
├── index.html              ← toda la app (HTML + CSS + JS)
├── vercel.json             ← config de deploy
└── README.md
```

Y por separado:
```
vpd-workflow-final.json     ← importar en n8n
```

---

## Deploy paso a paso

### 1. Subir a GitHub

```bash
# Si es la primera vez
git init
git add .
git commit -m "init vpd dashboard"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/vpd-dashboard.git
git push -u origin main

# Para actualizaciones futuras
git add .
git commit -m "actualizo config"
git push
```

### 2. Deploy en Vercel (gratis)

1. Entrá a https://vercel.com → **New Project**
2. Importá el repo de GitHub
3. Framework: **Other** (no framework)
4. Hacé clic en **Deploy**
5. En ~30 segundos tenés una URL pública tipo `https://vpd-dashboard-xxx.vercel.app`

### 3. Importar el workflow en n8n

1. En n8n, hacé clic en **+** para crear un workflow nuevo (o abrí el existente)
2. Menú → **Import from file** → seleccioná `vpd-workflow-final.json`
3. Activá el workflow (toggle arriba a la derecha)
4. Copiá las URLs de los webhooks:
   - Clic en el nodo **Webhook Config** → copiá la URL de producción (ej: `https://tu-n8n.com/webhook/vpd-config`)
   - Clic en el nodo **Webhook Control Manual** → copiá `https://tu-n8n.com/webhook/vpd-control`

### 4. Conectar la app con n8n

1. Abrí tu app en Vercel
2. Hacé clic en **⚙ Setup** (arriba a la derecha)
3. Pegá:
   - **Pulse API Key** y **Pulse Device ID** (los mismos de antes)
   - **N8N Webhook — Config**: la URL del webhook de config
   - **N8N Webhook — Control**: la URL del webhook de control
4. Guardá

---

## Uso diario

Para cambiar el VPD objetivo (ej: querés pasar de 1.1 a 0.9 kPa):

1. Abrí el dashboard
2. Pestaña **⚙ Configuración**
3. Cambiá el valor
4. Clic en **💾 Guardar y sincronizar con n8n**
5. Ves el banner verde "✓ Último sync con n8n: HH:MM:SS"
6. Listo — el próximo ciclo de n8n (≤25s) ya usa el nuevo valor

**No necesitás abrir n8n.**

