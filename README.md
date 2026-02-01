# Astro Blog + Decap CMS Template

Template de blog con CMS visual para clientes no-técnicos. **Costo: $0/mes**.

## Stack

| Componente | Servicio | Costo |
|------------|----------|-------|
| Hosting | GitHub Pages | Gratis |
| CMS | Decap CMS + DecapBridge | Gratis |
| Dominio (opcional) | Namecheap .lat | ~$1.8/año |

## Setup para Nuevo Cliente

### 1. Crear Repositorio

```bash
# Clonar template
git clone https://github.com/cofoundy/astro-decap-blog-template.git portfolio-NOMBRE-CLIENTE
cd portfolio-NOMBRE-CLIENTE

# Limpiar git y crear nuevo repo
rm -rf .git
git init
git add -A
git commit -m "Initial commit from template"

# Crear repo en GitHub
gh repo create cofoundy/portfolio-NOMBRE-CLIENTE --public --source=. --push
```

### 2. Configurar GitHub Pages

1. Ir a **Settings > Pages** del repo
2. Source: **GitHub Actions**
3. El workflow `.github/workflows/deploy.yml` ya está configurado

### 3. Actualizar astro.config.mjs

```javascript
export default defineConfig({
  site: 'https://cofoundy.github.io',
  base: '/portfolio-NOMBRE-CLIENTE',  // <- Cambiar esto
  integrations: [mdx(), sitemap()],
});
```

### 4. Crear GitHub Personal Access Token

1. Ir a https://github.com/settings/tokens
2. **Generate new token (classic)**
3. Nombre: `decapbridge-NOMBRE-CLIENTE`
4. Scopes: `repo` (full control)
5. **Importante**: El token debe tener acceso a la org `cofoundy`
6. Copiar el token (empieza con `ghp_`)

### 5. Configurar DecapBridge

1. Ir a https://decapbridge.com
2. Login con Google (cuenta cofoundy.dev@gmail.com)
3. Click **Add site**
4. Llenar:
   - **Git provider**: GitHub
   - **Repository**: `cofoundy/portfolio-NOMBRE-CLIENTE`
   - **Access token**: (pegar el token del paso 4)
   - **CMS URL**: `https://cofoundy.github.io/portfolio-NOMBRE-CLIENTE/admin/`
   - **Auth type**: PKCE
5. Click **Create site**
6. **Copiar el Site ID** del config generado (ej: `db7ee418-ca76-4804-889f-52043fff322c`)

### 6. Actualizar config.yml del CMS

Editar `public/admin/config.yml`:

```yaml
backend:
  name: git-gateway
  repo: cofoundy/portfolio-NOMBRE-CLIENTE  # <- Cambiar
  branch: main
  auth_type: pkce
  base_url: https://auth.decapbridge.com
  auth_endpoint: /sites/SITE-ID-AQUI/pkce  # <- Cambiar
  auth_token_endpoint: /sites/SITE-ID-AQUI/token  # <- Cambiar
  gateway_url: https://gateway.decapbridge.com

# ... resto del config

site_url: https://cofoundy.github.io/portfolio-NOMBRE-CLIENTE  # <- Cambiar
```

### 7. Agregar Colaboradores en DecapBridge

1. En DecapBridge, ir a **Manage collaborators**
2. Agregar emails de quienes podrán editar:
   - `cofoundy.dev@gmail.com` (admin)
   - `cliente@email.com` (cliente)

### 8. Commit y Deploy

```bash
git add -A
git commit -m "Configure CMS for client"
git push
```

Esperar ~30 segundos para que GitHub Actions despliegue.

### 9. Verificar

- **Sitio**: `https://cofoundy.github.io/portfolio-NOMBRE-CLIENTE/`
- **CMS**: `https://cofoundy.github.io/portfolio-NOMBRE-CLIENTE/admin/`

## Agregar Dominio Custom (Opcional)

### En Namecheap

1. Comprar dominio `.lat` (~$1.8/año)
2. DNS Settings:
   ```
   Type    Host    Value
   A       @       185.199.108.153
   A       @       185.199.109.153
   A       @       185.199.110.153
   A       @       185.199.111.153
   CNAME   www     cofoundy.github.io.
   ```

### En GitHub

1. Settings > Pages > Custom domain: `nombre.lat`
2. Check **Enforce HTTPS**

### En astro.config.mjs

```javascript
export default defineConfig({
  site: 'https://nombre.lat',
  // QUITAR base cuando usas dominio custom
  integrations: [mdx(), sitemap()],
});
```

### En config.yml

```yaml
site_url: https://nombre.lat
```

## Uso del CMS (Para Clientes)

1. Ir a `https://SITIO/admin/`
2. Click **Login**
3. Click **Google** (o Microsoft)
4. Seleccionar cuenta autorizada
5. Crear/editar posts en la interfaz visual
6. Click **Publish** - los cambios se despliegan automáticamente

## Troubleshooting

### "Git Gateway Error: [object Object]"
- El token no tiene acceso al repo o a la org
- Solución: Crear nuevo token con scope `repo` y acceso a la org

### "You don't have permission to access this site"
- El email no está en la lista de colaboradores de DecapBridge
- Solución: Agregar email en DecapBridge > Manage collaborators

### Links van a URLs incorrectas (404)
- Falta actualizar `base` en astro.config.mjs
- O faltan los `${import.meta.env.BASE_URL}` en los componentes

### CMS redirige al dashboard de DecapBridge
- Conflicto de sesión del navegador
- Solución: Abrir CMS en ventana de incógnito

## Estructura del Proyecto

```
├── public/
│   ├── admin/
│   │   ├── config.yml    # Configuración del CMS
│   │   └── index.html    # Entry point del CMS
│   └── images/uploads/   # Imágenes subidas desde el CMS
├── src/
│   ├── content/blog/     # Posts en Markdown (editables via CMS)
│   ├── components/
│   ├── layouts/
│   └── pages/
├── astro.config.mjs      # Config de Astro (site, base)
└── .github/workflows/    # GitHub Actions para deploy
```

## Comandos

```bash
npm install      # Instalar dependencias
npm run dev      # Servidor local en localhost:4321
npm run build    # Build de producción
npm run preview  # Preview del build
```

---

**Cofoundy** | https://cofoundy.dev
