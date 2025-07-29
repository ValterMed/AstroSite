---
title: 'Prácticas para Escribir Código Resistente a Vulnerabilidades desde el Principio'
pubDate: 2023-11-20
description: 'Guía esencial de principios y técnicas para desarrollar software seguro desde la primera línea de código.'
author: 'Tu Nombre'
image:
    url: 'https://ejemplo.com/secure-coding.jpg'
    alt: 'Código fuente con un candado sobreimpreso representando seguridad'
tags: ["secure-coding", "devsecops", "owasp", "buenas-practicas"]
---

# Prácticas para Escribir Código Resistente a Vulnerabilidades desde el Principio

**Publicado el:** 20 Nov 2023  
**Tiempo de lectura:** 8 min

## Introducción

El *Secure by Design* no es un añadido, sino una filosofía que debe impregnar cada fase del desarrollo. Según el informe *Cost of a Data Breach 2023* de IBM, el 60% de las vulnerabilidades explotadas tenían parches disponibles pero no implementados. Aquí te presento prácticas fundamentales:

## 1. Validación de Inputs (Principio de Confianza Cero)

```python
# Anti-patrón (inseguro)
user_input = request.GET.get('filename')
os.system(f"cat {user_input}")

# Buen patrón (seguro)
import re
from pathlib import Path

safe_pattern = re.compile(r'^[\w-]+\.[a-z]{2,4}$')
if safe_pattern.match(user_input):
    file_path = Path('/safe_dir') / user_input
    if file_path.exists():
        with open(file_path, 'r') as f:
            content = f.read()
```

**Técnicas clave:**
- Whitelisting sobre blacklisting
- Librerías como `validator.js` para Node o `OWASP ESAPI` para Java
- Desinfección contextual (HTML, SQL, OS commands)

## 2. Gestión Segura de Autenticación

**OAuth 2.0 Best Practices:**
- Usar `PKCE` para clientes públicos
- Tiempos de expiración cortos (1-2h para access tokens)
- Almacenamiento seguro:
  - **Frontend:** `HttpOnly + Secure + SameSite` cookies
  - **Mobile:** Keystore/Enclave seguro

```javascript
// Ejemplo seguro en Node.js
import bcrypt from 'bcryptjs';

const saltRounds = 12;
const hashedPassword = await bcrypt.hash(plainPassword, saltRounds);

// Verificación
const isMatch = await bcrypt.compare(inputPassword, storedHash);
```

## 3. Protección contra Inyecciones

| Tipo        | Prevención                          | Herramientas                     |
|-------------|-------------------------------------|----------------------------------|
| SQL         | Prepared statements                 | ORMs, `pg-escape`               |
| NoSQL       | Validación de esquemas              | Mongoose validators             |
| Comandos OS | Argumentos como array               | `child_process.spawn()`         |
| Template    | Escapado contextual                 | DOMPurify, `js-xss`             |

## 4. Seguridad en Dependencias

```bash
# Escaneo de dependencias
npm audit --production
snyk test
```

**Prácticas:**
- Actualizar semanalmente (`npm outdated`)
- Usar lockfiles (`package-lock.json`)
- Verificar checksums (`npm ci --ignore-scripts`)

## 5. Configuración Segura por Defecto

**Ejemplo en Docker:**
```dockerfile
FROM node:18-alpine

USER node:node  # ¡No root!
WORKDIR /home/node/app

COPY --chown=node:node . .

EXPOSE 3000
CMD ["node", "server.js"]
```

**Principios:**
- Mínimos privilegios
- Logging sin datos sensibles
- Headers de seguridad (CSP, HSTS)

## Herramientas Recomendadas

1. **Análisis Estático:**
   - SonarQube (`sonarqube.org`)
   - Semgrep (`semgrep.dev`)

2. **Escaneo Dinámico:**
   - OWASP ZAP (`zaproxy.org`)
   - Burp Suite (`portswigger.net`)

3. **Hardening:**
   - Lynis (Linux hardening)
   - CIS Benchmarks

## Conclusión

> "La seguridad no es un producto, sino un proceso" - Bruce Schneier

Integrar estas prácticas desde el primer commit reduce hasta un 70% las vulnerabilidades críticas (datos de *GitLab DevSecOps Report 2023*). La clave está en:

1. Automatizar verificaciones
2. Educar continuamente al equipo
3. Medir la efectividad (ej: % de findings en SAST)

¿Qué otras prácticas usas en tus proyectos? ¡Compártelas en los comentarios!

---

**🔗 Recursos Adicionales:**
- [OWASP Secure Coding Practices](https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [NIST Secure Software Development Framework](https://csrc.nist.gov/Projects/ssdf)