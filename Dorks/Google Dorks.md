# 🔍 **Google Dorks Professional Cheatsheet**
## *The Ultimate OSINT and Reconnaissance Guide*

---

## 📋 **Índice**
- [Fundamentos](#fundamentos)
- [Operadores Básicos](#operadores-básicos)
- [Búsquedas por Tipo de Archivo](#búsquedas-por-tipo-de-archivo)
- [Vulnerabilidades y Exploits](#vulnerabilidades-y-exploits)
- [Dispositivos y Cámaras](#dispositivos-y-cámaras)
- [Credenciales y Logs](#credenciales-y-logs)
- [Redes y Servidores](#redes-y-servidores)
- [Redes Sociales](#redes-sociales)
- [Git y Repositorios](#git-y-repositorios)
- [Paneles de Administración](#paneles-de-administración)
- [Bases de Datos y APIs](#bases-de-datos-y-apis)
- [Automoción y Scripting](#automoción-y-scripting)
- [Herramientas Especializadas](#herramientas-especializadas)
- [Consideraciones Éticas](#consideraciones-éticas)

---

## 🔧 **Fundamentos**

### **¿Qué son Google Dorks?**
Técnicas de búsqueda avanzada que utilizan **operadores especiales** para encontrar información no indexada normalmente o sensible.

### **Sintaxis Básica:**
```
operador:parámetro
```
Ejemplo: `site:github.com "password"`

### **Combinación de Operadores:**
```
site:target.com intitle:"index of" "parent directory"
```

---

## 🎯 **Operadores Básicos**

### **Filtros Esenciales:**

| Operador | Descripción | Ejemplo |
|----------|-------------|---------|
| `site:` | Restringe a un dominio | `site:github.com` |
| `intitle:` | Busca en título de página | `intitle:"index of"` |
| `inurl:` | Busca en URL | `inurl:admin` |
| `intext:` | Busca en contenido | `intext:"password"` |
| `filetype:` | Tipo de archivo | `filetype:pdf` |
| `ext:` | Extensión de archivo | `ext:sql` |
| `cache:` | Versión cacheada | `cache:example.com` |
| `link:` | Páginas que enlazan | `link:example.com` |
| `related:` | Sitios relacionados | `related:youtube.com` |
| `define:` | Definiciones | `define:hacking` |
| `info:` | Información del sitio | `info:example.com` |

### **Operadores Lógicos:**
```
"texto exacto"      # Frase exacta
OR                  # O lógico (|)
AND                 # Y lógico (espacio)
-                   # Excluir término
*                   # Comodín
( )                 # Agrupación
```

### **Rangos:**
```
2010..2015          # Números del 2010 al 2015
$100..$500          # Precios entre 100 y 500
```

---

## 📄 **Búsquedas por Tipo de Archivo**

### **Documentos Sensibles:**
```google
filetype:pdf "confidential" OR "internal use only"
filetype:docx "password" OR "credentials"
filetype:xlsx intext:"email" "password"
filetype:ppt "confidential" site:company.com
```

### **Archivos de Configuración:**
```google
ext:env DB_PASSWORD
ext:yml OR ext:yaml database password
ext:json "api_key" OR "secret"
ext:ini "passw" OR "pwd"
ext:cfg OR ext:conf "password"
```

### **Backups y Logs:**
```google
ext:sql "dump" OR "backup"
ext:log "error" "password"
ext:bkf OR ext:bkp site:target.com
"backup" ext:zip OR ext:rar OR ext:tar.gz
```

### **Archivos de Desarrollo:**
```google
ext:php intext:"mysql_connect"
ext:asp "connection string"
ext:js "apiKey" OR "secret"
ext:py "password" "import"
ext:java "String password" "="
```

---

## 🎯 **Vulnerabilidades y Exploits**

### **Inyección SQL:**
```google
inurl:"id=" "product" OR "category"
inurl:"page=" "php" "id"
inurl:"index.php?id="
inurl:"cat=" "php"
inurl:"item=" "php"
```

### **XSS (Cross-Site Scripting):**
```google
inurl:"search=" "php"
inurl:"q=" "php"
inurl:"s=" "php"
inurl:"query=" "php"
inurl:"keyword=" "php"
```

### **LFI/RFI (File Inclusion):**
```google
inurl:"page=" "include"
inurl:"file=" "include"
inurl:"document=" "php"
inurl:"folder=" "php"
inurl:"path=" "php"
```

### **Paneles de Login Vulnerables:**
```google
intitle:"login" "admin" "password"
intitle:"administrator login"
inurl:"/admin/login.php"
inurl:"/wp-admin" -wordpress
```

---

## 📹 **Dispositivos y Cámaras**

### **Cámaras IP:**
```google
intitle:"webcamXP 5"
inurl:"/view/view.shtml"
intitle:"Live View / - AXIS"
inurl:"axis-cgi/mjpg"
"Network Camera" "Live View"
```

### **Router/Modems:**
```google
intitle:"RouterOS" "Winbox"
intitle:"DD-WRT" "Status"
inurl:"/cgi-bin/luci"
"Router UPS" "Status"
intitle:"MikroTik" "Login"
```

### **Sistemas de Control:**
```google
intitle:"SCADA" "HMI"
"inurl:graficos.html" "PLC"
"PLC" "WebServer"
"Modbus" "Web Interface"
```

### **Impresoras y Multifuncionales:**
```google
intitle:"HP LaserJet" "Status"
inurl:"/hp/device/this.LCDispatcher"
"Printer Status" "WebJetAdmin"
```

---

## 🔐 **Credenciales y Logs**

### **Contraseñas Expuestas:**
```google
"login:" "password" filetype:txt
"username" "password" ext:csv
intext:"@gmail.com" "password"
"jdbc:mysql://" "password"
```

### **Archivos de Configuración con Credenciales:**
```google
filetype:properties "password"
ext:yml "database" "pass"
"config.php" "mysql_connect"
".env" "DB_PASSWORD" -github
```

### **Logs con Información Sensible:**
```google
ext:log "GET /" "POST /"
"error" "warning" ext:log
"ACCESS_LOG" "HTTP"
"apache.log" "password"
```

### **Tokens y API Keys:**
```google
"api_key" "xxxx"
"sk_live" "rzp_test"
"AWS_ACCESS_KEY_ID"
"ghp_" "github_pat"
```

---

## 🌐 **Redes y Servidores**

### **Directorios Listados:**
```google
intitle:"index of" "parent directory"
intitle:"index of" "www"
"Index of /" "admin"
"Index of /" ".git"
```

### **Paneles de Control:**
```google
intitle:"phpMyAdmin" "Welcome"
inurl:"/phpmyadmin/" "Welcome"
intitle:"Webmin" "Login"
inurl:"/cpanel" "login"
```

### **Servidores y Puertos:**
```google
"Apache/2.4" "Server at"
"nginx/" "Welcome"
"Microsoft-IIS/8.5" "Server"
"port 22" "OpenSSH"
```

### **Subdominios:**
```google
site:*.target.com
site:*.*.target.com
-site:www.target.com site:target.com
```

---

## 👥 **Redes Sociales**

### **Perfiles Públicos:**
```google
site:linkedin.com/in "CEO"
site:twitter.com "from:username"
site:facebook.com "people named"
site:instagram.com "location:"
```

### **Información Personal:**
```google
"@gmail.com" "resume" OR "CV"
"phone" "email" "resume" filetype:pdf
"contact me" "@gmail.com" "CV"
```

### **Grupos y Foros:**
```google
site:reddit.com "target.com"
site:stackoverflow.com "target.com"
site:github.com "target.com" "password"
```

---

## 💾 **Git y Repositorios**

### **Repositorios Expuestos:**
```google
".git" intitle:"index of"
".git/config" intitle:"index of"
"git" intitle:"index of"
```

### **GitHub Sensible:**
```google
site:github.com "password" "remove"
site:github.com "secret" "key"
site:github.com "aws_access_key_id"
"filename:.env" "DB_PASSWORD"
```

### **Archivos .gitignore Violados:**
```google
"# .gitignore" "node_modules" filetype:txt
"gitignore" "credentials" "example"
```

---

## 🛠️ **Paneles de Administración**

### **WordPress:**
```google
inurl:/wp-admin/admin-ajax.php
"WordPress" "Database Error"
inurl:"/wp-content/plugins/"
```

### **Joomla:**
```google
inurl:"/administrator/" "Joomla"
intitle:"Joomla Administration Login"
```

### **Drupal:**
```google
inurl:"/user/login" "Drupal"
"Drupal" "Database error"
```

### **Otros CMS:**
```google
"Magento" "Admin" "Login"
"PrestaShop" "administration"
"OpenCart" "Administration"
```

---

## 🗄️ **Bases de Datos y APIs**

### **Bases de Datos Expuestas:**
```google
"phpMyAdmin" "running on"
"MySQL dump" filetype:sql
"MongoDB" "port 27017"
"Redis" "port 6379"
```

### **Endpoints de API:**
```google
inurl:"/api/v1/" "key"
"swagger" "API" "json"
"REST API" "documentation"
"endpoint" "api" "json"
```

### **Documentación Técnica:**
```google
filetype:pdf "API documentation"
"technical manual" site:target.com
"user guide" "admin" filetype:pdf
```

---

## 🤖 **Automoción y Scripting**

### **Herramientas Automáticas:**
```bash
# Install dork-cli
pip install dork-cli

# Basic usage
dork -q 'site:target.com "password"' -n 100

# Use with tor
proxychains dork -q 'filetype:sql' -p 5
```

### **Python Script para Dorking:**
```python
import requests
from googlesearch import search

def google_dork(query, num_results=50):
    results = []
    try:
        for url in search(query, num_results=num_results):
            results.append(url)
    except Exception as e:
        print(f"Error: {e}")
    return results

# Usage
queries = [
    'site:target.com filetype:pdf',
    'intitle:"index of" "parent directory"',
    'inurl:admin filetype:php'
]

for query in queries:
    print(f"\nSearching: {query}")
    urls = google_dork(query, 20)
    for url in urls:
        print(f"  - {url}")
```

### **Bash Automation:**
```bash
#!/bin/bash
# automated_dorking.sh

TARGET="$1"
DORKS_FILE="dorks.txt"
OUTPUT_DIR="results"

mkdir -p "$OUTPUT_DIR"

while read -r dork; do
    echo "[*] Searching: $dork"
    encoded_dork=$(echo "$dork" | sed 's/ /%20/g')
    curl -s "https://www.google.com/search?q=$encoded_dork" \
        -A "Mozilla/5.0" \
        | grep -oP 'href="\/url\?q=\K[^&]*' \
        | grep -v "google.com" \
        > "$OUTPUT_DIR/$(echo $dork | md5sum | cut -d' ' -f1).txt"
    sleep 2
done < "$DORKS_FILE"
```

---

## 🛡️ **Herramientas Especializadas**

### **1. Dork Scanner (Python):**
```python
import concurrent.futures
import requests
from bs4 import BeautifulSoup

class DorkScanner:
    def __init__(self):
        self.session = requests.Session()
        self.session.headers.update({'User-Agent': 'Mozilla/5.0'})
    
    def search_dork(self, dork, pages=3):
        results = []
        for page in range(0, pages*10, 10):
            url = f"https://www.google.com/search?q={dork}&start={page}"
            try:
                response = self.session.get(url, timeout=10)
                soup = BeautifulSoup(response.text, 'html.parser')
                for link in soup.find_all('a', href=True):
                    href = link['href']
                    if '/url?q=' in href:
                        clean_url = href.split('/url?q=')[1].split('&')[0]
                        if clean_url.startswith('http'):
                            results.append(clean_url)
            except:
                continue
        return list(set(results))

# Usage
scanner = DorkScanner()
dorks = ['site:github.com "password"', 'filetype:sql "dump"']
with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
    results = list(executor.map(scanner.search_dork, dorks))
```

### **2. Dork Collections:**
```python
DORK_CATEGORIES = {
    "sql_injection": [
        'inurl:"id=" "product"',
        'inurl:"cat=" "php"',
        'inurl:"page=" "id"'
    ],
    "exposed_docs": [
        'filetype:pdf "confidential"',
        'filetype:docx "password"',
        'filetype:xlsx "credentials"'
    ],
    "cameras": [
        'intitle:"webcamXP"',
        'inurl:"/view/view.shtml"',
        '"Network Camera" "Live View"'
    ],
    "admin_panels": [
        'intitle:"admin login"',
        'inurl:"/admin/" "login"',
        'intitle:"Login" "Administrator"'
    ]
}
```

---

## ⚠️ **Consideraciones Éticas y Legales**

### **LEGALIDAD:**
```markdown
✅ PERMITIDO:
- Auditorías autorizadas
- Testing en sistemas propios
- Bug bounty programs
- Investigación académica

❌ ILEGAL:
- Acceso no autorizado
- Robo de información
- Alteración de datos
- Denegación de servicio
```

### **Mejores Prácticas:**
```markdown
1. 🔒 **Siempre obtener permiso por escrito**
2. 📝 **Documentar todo el proceso**
3. ⏱️ **Respetar rate limits de Google**
4. 🎯 **Limitar scope a objetivos autorizados**
5. 🗑️ **Destruir información sensible post-auditoría**
```

### **Protección Contra Dorking:**
```markdown
1. robots.txt bien configurado
2. No indexar directorios sensibles
3. Usar autenticación apropiada
4. Monitorear logs de Googlebot
5. Revisar regularmente qué hay indexado
```

---

## 🎯 **Plantillas de Búsqueda por Objetivo**

### **Pentesting Web:**
```google
site:target.com ext:php OR ext:asp
inurl:target.com intitle:"index of"
site:target.com "error" "warning"
site:target.com inurl:admin OR login
```

### **Reconocimiento Corporativo:**
```google
site:company.com filetype:pdf OR docx OR xlsx
"company.com" "@gmail.com" OR "@company.com"
site:linkedin.com "company" "employee"
site:github.com "company" "config"
```

### **Bug Bounty:**
```google
site:*.target.com -www
site:target.com inurl:api OR endpoint
"target.com" "subdomain" filetype:txt
site:target.com "staging" OR "test"
```

### **OSINT Personal:**
```google
"nombre apellido" "@gmail.com"
"nombre" "teléfono" "dirección"
"nombre" site:facebook.com OR site:twitter.com
"nombre" filetype:pdf "curriculum"
```

---

## 📚 **Recursos Adicionales**

### **Repositorios de Dorks:**
```markdown
- https://github.com/search?q=google+dorks
- https://www.exploit-db.com/google-hacking-database
- https://github.com/opsdisk/pagodo
```

### **Herramientas Recomendadas:**
```bash
# Pagodo - Google Hacking Tool
git clone https://github.com/opsdisk/pagodo.git

# GooDork - Dorking Tool
git clone https://github.com/k3170makan/GooDork

# Dorks Hunter
git clone https://github.com/ClownQ/Dorks_hunter
```

### **Listas de Dorks:**
```bash
# GHDB (Google Hacking Database)
wget https://gitlab.com/kalilinux/packages/exploitdb/-/raw/main/files_exploits.csv?inline=false

# Dorks Collections
/usr/share/exploitdb/exploits/google-dorks/
```



---

## 🚨 **Ejemplos de Búsquedas Peligrosas (Solo Educativos)**

### **⚠️ SOLO PARA LABORATORIOS ⚠️**
```google
# Credenciales de Bases de Datos
"$dbuser = " "password" filetype:php

# Configuraciones de Correo
"SMTP_PASSWORD" "MAIL_PASS" filetype:env

# Tokens de API en JS
const API_KEY = " ext:js

# SSH Keys Públicas
"-----BEGIN RSA PRIVATE KEY-----"

# Configuraciones de Firebase
"apiKey" "authDomain" "projectId"
```

---

## 🎓 **Consejos Profesionales**

### **Para Pentesters:**
```markdown
1. **Comienza con dorks genéricos** y luego específicos
2. **Combina múltiples operadores** para filtrar resultados
3. **Usa el cache de Google** para ver contenido eliminado
4. **Monitorea los logs** de tu búsqueda
5. **Documenta cada hallazgo** con captura de pantalla
```

### **Para Defensores:**
```markdown
1. **Busca tu propia empresa** regularmente
2. **Configura alertas** para términos sensibles
3. **Revisa robots.txt** y meta tags noindex
4. **Educa al personal** sobre información sensible
5. **Implementa WAF** que bloquee scraping
```

### **Para Investigadores:**
```markdown
1. **Verifica la fuente** de cada hallazgo
2. **Contextualiza la información**
3. **Respeta la privacidad**
4. **Reporta vulnerabilidades** de forma responsable
5. **Mantén ética profesional**
```

---

## 📞 **Reporte Responsable**

### **Plantilla de Reporte:**
```markdown
# Google Dork Finding Report

## Información General
- **URL encontrada**: [URL]
- **Tipo de información**: [Credenciales/Config/etc]
- **Severidad**: [Alta/Media/Baja]
- **Fecha de descubrimiento**: [Fecha]

## Detalles Técnicos
- **Dork utilizado**: `[dork aquí]`
- **Evidencia**: [Screenshot/URL]
- **Impacto potencial**: [Descripción]

## Recomendaciones
1. [Recomendación 1]
2. [Recomendación 2]
3. [Recomendación 3]

## Contacto
- **Reportado por**: [Tu nombre/alias]
- **Email**: [Email para contacto]
- **PGP Key**: [Si aplica]
```

---

|Dork|Details|
|---|---|
|inurl:notepad.link + keyword|Sensitive Information disclosure / Juicy Info|
|intitle:"GlobalProtect Portal"|Juicy Information for PAN-OS 0day CVE-2024-3400|
|site:notion.so + keyword|Juicy Information|
|site:notes.io + keyword|Juicy Information|
|site:hackmd.io + keyword|Juicy Information|
|inurl:orgId=1|Exposed Grafana Dashboards|
|site:s3.amazonaws.com + keyword|Exposed Files on Amazon S3 buckets|
|site:blob.core.windows.net + keyword|Exposed Files on Azure Storage blobs|
|inurl:app.powerbi.com|Exposed PowerBi Panels|
|intitle:iDRAC* inurl:login.html|Dell iDRAC Login pages|
|inurl:node_modules/ua-parser-js|Vulnerable ua-parser-js search (bower.json)|
|inurl:protocol/openid-connect|OpenID SSO Login Portals|
|inurl:smb/web/view|Plesk Login pages|
|inurl:auth/realms|SSO Login pages|
|inurl:nav_to.do|Service Now Login Pages|
|inurl:nwa/sysinfo|SAP Login Pages|
|intitle:"Smart Network Application"|Cisco RECON|
|inurl:SAAS/auth/login|VMware Workspace ONE Access RCE by SSTI - CVE-2022-22954|
|site:snapshot.raintank.io|Grafana Public Snapshots|
|inurl:administrator/manifests|CMS Files and Directory Listings|
|intitle:"Carestream Vue Motion"|Health Systems Login pages (x)|
|intitle:"VM VIRTUAL FILM HTML5"|Health Systems Login pages (x)|
|inurl:Portal/default.aspx?usertoken=|Philips MyVue Leaks (x)|
|inurl:wp-includes/sodium_compat/src|Wordpress Dir listing|
|inurl:memoqweb|Memoqweb Login Pages|
|inurl:"auth/login?from_page="|WatchGuard Login Pages|
|intitle:"WatchGuard Access Portal"|WatchGuard Login Pages|
|intitle:"epirus \| blockchain explorer - web3 labs" network|Epirus Blockchain Explorer|
|inurl:application.wadl|Juicy Information|