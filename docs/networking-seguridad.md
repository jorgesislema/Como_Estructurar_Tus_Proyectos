# Networking y Seguridad: DNS, CDN, HTTPS, WAF, Firewalls

Mientras escribes codigo en VSCode, todo funciona en `localhost`. Pero en el mundo real hay DNS, HTTPS, firewalls, CDNs y una capa completa de infraestructura de red que tu aplicacion necesita para ser accesible y segura. Esto es lo que el editor no te muestra.

---

## 1. Dominio y DNS

### Como funciona

Cuando un usuario escribe `tudominio.com` en el navegador:

```
Usuario escribe "tudominio.com"
    |
[DNS Resolver] -> Que IP tiene tudominio.com?
    |
[Nameservers] -> tudominio.com tiene IP 192.168.1.100
    |
[Navegador] -> Conecta a 192.168.1.100 (tu servidor)
```

### Comprar un dominio

Registradores recomendados (2024-2025):

| Registrador | Precio .com | DNS incluido | Privacy gratis | Notas |
|-------------|-------------|--------------|----------------|-------|
| Cloudflare | Precio de costo | Si | Si | Mejor opcion global |
| Namecheap | $10-14/ano | Si | Si | Buen soporte |
| Porkbun | $10/ano | Si | Si | Barato y simple |
| GoDaddy | $12-20/ano | Si | No (cuesta extra) | Evitar: upsells agresivos |

Regla: nunca compres dominio y hosting en el mismo lugar. Si el hosting falla, puedes apuntar el dominio a otro lado en minutos.

### Configuracion de DNS

Registros DNS comunes:

| Tipo | Nombre | Valor | Proposito |
|------|--------|-------|-----------|
| A | @ | 192.168.1.100 | IP del servidor (dominio raiz) |
| A | www | 192.168.1.100 | IP para www.tudominio.com |
| CNAME | api | tudominio.com | Subdominio api.tudominio.com |
| CNAME | app | vercel.app | Subdominio apunta a Vercel |
| MX | @ | aspmx.l.google.com | Servidor de correo |
| TXT | @ | v=spf1... | Verificacion de email (SPF) |
| TXT | _acme-challenge | ... | Verificacion de SSL (Let's Encrypt) |

### Patron tipico para proyecto con frontend + API

```
tudominio.com         -> CNAME -> Vercel/Netlify (frontend)
www.tudominio.com     -> CNAME -> Vercel/Netlify
api.tudominio.com     -> A      -> IP del VPS (backend API)
                      -> CNAME -> Railway/Render (backend PaaS)
```

---

## 2. HTTPS y SSL/TLS

### Por que es obligatorio

- Navegadores marcan sitios HTTP como "No seguro"
- APIs externas (OpenAI, Stripe, Google) rechazan requests desde origenes HTTP
- Los datos viajan encriptados (contraseñas, tarjetas, datos personales)
- Es gratis (Let's Encrypt)

### Como obtener HTTPS

**Si usas PaaS (Railway, Render, Vercel, Fly.io, Heroku):**
Automatico. La plataforma gestiona los certificados. No tienes que hacer nada.

**Si usas VPS:**

Opcion 1: Caddy (recomendado, el mas simple)
```bash
# Caddyfile
tudominio.com {
    reverse_proxy localhost:8000
}
```
Caddy obtiene y renueva certificados SSL automaticamente. Cero configuracion.

Opcion 2: Nginx + Certbot
Nginx como reverse proxy + Certbot para certificados Let's Encrypt. Mas configuracion pero mas control.

Opcion 3: Traefik (si usas Docker)
Reverse proxy que detecta contenedores automaticamente y les asigna HTTPS.

**Si usas Cloudflare:**
Puedes usar el proxy de Cloudflare que automaticamente provee HTTPS entre el usuario y Cloudflare. Entre Cloudflare y tu servidor puedes usar HTTP (si es seguro en tu red) o HTTPS con certificado de Cloudflare.

---

## 3. CDN (Content Delivery Network)

### Que es

Una red de servidores distribuidos globalmente que almacenan copias de tu contenido estatico (imagenes, CSS, JS, HTML) cerca de los usuarios.

Sin CDN: un usuario en Tokyo descarga una imagen desde tu servidor en Virginia (200ms de latencia).
Con CDN: el usuario en Tokyo descarga la imagen desde el nodo CDN en Tokyo (5ms de latencia).

### Cuando necesitas CDN

- Tu audiencia es global (usuarios en multiple continentes)
- Sirves archivos estaticos grandes (imagenes, videos, PDFs)
- Necesitas proteccion basica contra DDoS
- Tu sitio es publico (no una app interna)

### Cuando NO necesitas CDN
- API que devuelve JSON (no se cachea bien en CDN)
- App con solo usuarios locales (todo en un pais)
- Prototipo o MVP (optimizacion prematura)

### Proveedores de CDN

| Proveedor | Precio | Ideal para |
|-----------|--------|-----------|
| Cloudflare | Gratis (plan basico) | Todo. La mejor opcion para empezar |
| AWS CloudFront | Pago por uso | Si ya estas en AWS |
| Vercel Edge Network | Incluido | Si usas Vercel |
| Netlify Edge | Incluido | Si usas Netlify |
| Fastly | Pago por uso | Alto trafico, personalizacion avanzada |
| BunnyCDN | Pay-as-you-go | Barato y simple |

---

## 4. WAF (Web Application Firewall)

### Que es

Un firewall que analiza trafico HTTP y bloquea ataques comunes: inyeccion SQL, XSS, CSRF, fuerza bruta, bots maliciosos.

### Cuando necesitas WAF

- Tu aplicacion maneja datos sensibles o pagos
- Eres objetivo de ataques (proyecto visible publicamente)
- Necesitas compliance (PCI DSS, SOC2, HIPAA)
- Recibes trafico significativo (> 10K requests/dia)

### Soluciones WAF

| Solucion | Precio | Complejidad |
|----------|--------|-------------|
| Cloudflare WAF | Gratis (5 reglas) / Pro $20/mes | Baja |
| AWS WAF | Pago por regla/request | Media |
| OWASP ModSecurity | Gratis | Alta (auto-gestionado) |
| Fastly WAF | Pago por uso | Media |

Para el 95% de proyectos: Cloudflare WAF gratuito es suficiente. Activalo desde el dia 1.

---

## 5. Firewalls de Red

El VPS que alquilas viene con puertos abiertos. Si no los cierras, cualquiera puede intentar conectarse.

### Puertos minimos necesarios

| Puerto | Servicio | Recomendacion |
|--------|----------|---------------|
| 22 | SSH | Cambiar a puerto no estandar o usar VPN |
| 80 | HTTP | Abierto (redirige a HTTPS) |
| 443 | HTTPS | Abierto |
| 5432 | PostgreSQL | CERRADO al mundo. Solo localhost o red interna |
| 6379 | Redis | CERRADO al mundo. Solo localhost |
| 27017 | MongoDB | CERRADO al mundo. Solo localhost |

### Reglas basicas de firewall en VPS

1. Solo abrir puertos 80 y 443 al mundo
2. SSH (22) solo desde tu IP o mediante VPN
3. Bases de datos solo aceptan conexiones desde localhost o IPs de tu aplicacion
4. Usar `ufw` (Linux) o el firewall del proveedor cloud para configurarlo

---

## 6. VPN y Acceso Seguro

Cuando tu aplicacion crece, necesitas acceder a recursos internos de forma segura:

- Acceder a la base de datos de produccion para debugging
- Conectarte a servidores de staging
- Administrar infraestructura

### Opciones

**Tailscale (recomendado):** VPN mesh basada en WireGuard. Gratis para hasta 100 dispositivos. Instalas el agente en cada servidor y maquina, y crea una red privada entre ellas. La forma mas simple de acceso seguro.

**Cloudflare Zero Trust / Tunnel:** Acceso a servicios internos sin exponer puertos publicos. Gratis para hasta 50 usuarios.

**WireGuard:** VPN tradicional, rapida, pero requiere mas configuracion manual.

---

## 7. Secretos y Variables de Entorno en Produccion

### El problema

En local usas `.env`. En produccion necesitas:

1. No exponer secretos en el codigo fuente
2. No exponer secretos en logs
3. Rotar secretos periodicamente
4. Diferentes secretos para diferentes entornos
5. Saber quien accedio a que secreto (audit trail)

### Soluciones por nivel

**Nivel basico (proyecto personal):**
Variables de entorno del sistema operativo o del gestor de procesos (systemd, Docker env, PaaS dashboard).

**Nivel intermedio (startup):**
- GitHub Secrets + GitHub Actions (inyecta en CI/CD)
- Vercel/Railway/Render Environment Variables (panel de la plataforma)
- Doppler (gestion centralizada de secretos, free tier generoso)

**Nivel avanzado (empresa):**
- AWS Secrets Manager / Parameter Store
- GCP Secret Manager
- Azure Key Vault
- HashiCorp Vault

### Reglas de seguridad con secretos

1. **NUNCA** commitear secretos en el codigo fuente
2. Usar `.env.example` (sin valores reales) para documentar que variables se necesitan
3. Si accidentalmente commiteaste un secreto, rotalo inmediatamente (cambiarlo en el proveedor)
4. Usar `git-secrets` o `detect-secrets` en pre-commit hooks para evitar leaks
5. Limitar acceso a secretos por necesidad (solo CI/CD y admin)
6. Auditar quien accede a secretos

---

## 8. Checklist de Seguridad Basica (Imprescindible)

```
[ ] HTTPS activo (no aceptar HTTP, redirigir a HTTPS)
[ ] Firewall configurado (solo puertos necesarios abiertos)
[ ] SSH solo con clave (no contraseña)
[ ] Base de datos NO expuesta a internet
[ ] .env en .gitignore
[ ] API keys de produccion diferentes a las de desarrollo
[ ] Dependencias actualizadas (Dependabot/Renovate activo)
[ ] Rate limiting activo en la API
[ ] CORS configurado (solo origenes de confianza, no *)
[ ] Cabeceras de seguridad (HSTS, X-Frame-Options, CSP)
[ ] Backups automaticos de base de datos
[ ] Cloudflare (o similar) como proxy delante del servidor
```

---

## Referencias Cruzadas

- [Decision de Infraestructura](./decision-infraestructura.md) -- Donde ejecutar
- [Arquitecturas de Despliegue](./arquitecturas-despliegue.md) -- Como estructurar
- [Entornos y Stages](./entornos-y-stages.md) -- Separacion de entornos
- [Bases de Datos](./bases-datos-almacenamiento.md) -- Almacenamiento seguro
- [CI/CD y GitOps](./cicd-gitops.md) -- Automatizacion segura
