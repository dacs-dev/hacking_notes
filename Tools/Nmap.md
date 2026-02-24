## 📋 Índice
- [Fundamentos](#fundamentos)
- [Descubrimiento de Hosts](#descubrimiento-de-hosts)
- [Escaneos TCP](#escaneos-tcp)
- [Escaneos UDP](#escaneos-udp)
- [Detección de Servicios y OS](#detección-de-servicios-y-os)
- [Scripting (NSE)](#scripting-nse)
- [Opciones de Timing](#opciones-de-timing)
- [Evasión y Stealth](#evasión-y-stealth)
- [Salida y Formato](#salida-y-formato)
- [Escaneos Avanzados](#escaneos-avanzados)
- [Perfiles Personalizados](#perfiles-personalizados)

---

## 🔧 Fundamentos

### Estructura Básica
```bash
nmap [Tipo de Escaneo] [Opciones] [Objetivo]
```

### Tipos de Objetivos
```bash
nmap 192.168.1.1            # IP única
nmap 192.168.1.1-100        # Rango de IPs
nmap 192.168.1.0/24         # Subred CIDR
nmap scanme.nmap.org        # Dominio
nmap -iL targets.txt        # Lista desde archivo
```

### Privilegios Requeridos
| Escaneo | Requiere Root | Por Qué |
|---------|--------------|---------|
| `-sS` (SYN) | ✅ | RAW socket packets |
| `-sU` (UDP) | ✅ | RAW socket packets |
| `-O` (OS) | ✅ | Modifica TTL, analiza respuestas |
| `-sT` (Connect) | ❌ | Usa syscall connect() |
| `-sn` (Ping) | ❌* | *Algunas técnicas sí requieren |

---

## 🔍 Descubrimiento de Hosts

### Ping Scan (Host Discovery)
```bash
nmap -sn 192.168.1.0/24          # Solo descubrimiento, no escanea puertos
nmap -sn -PE 192.168.1.0/24      # ICMP Echo
nmap -sn -PP 192.168.1.0/24      # ICMP Timestamp
nmap -sn -PM 192.168.1.0/24      # ICMP Netmask
```

### Sin Descubrimiento (Skip Discovery)
```bash
nmap -Pn 192.168.1.1             # Asume host activo, omite ping
```

### Técnicas de Descubrimiento
```bash
nmap -sn --packet-trace 192.168.1.1    # Ver paquetes enviados
nmap -sn --reason 192.168.1.0/24       # Mostrar razón de estado
```

---

## 🎯 Escaneos TCP

### SYN Scan (Stealth/Half-open)
```bash
sudo nmap -sS 192.168.1.1
```
**Explicación**: Envía SYN, si recibe SYN-ACK → puerto abierto, envía RST para cerrar (no completa 3-way handshake).

### TCP Connect Scan
```bash
nmap -sT 192.168.1.1
```
**Explicación**: Usa connect() del sistema, completa 3-way handshake. Menos sigiloso pero no requiere root.

### FIN Scan
```bash
sudo nmap -sF 192.168.1.1
```
**Explicación**: Envía paquete FIN. Puertos cerrados responden RST, abiertos ignoran (según RFC).

### NULL Scan
```bash
sudo nmap -sN 192.168.1.1
```
**Explicación**: Envía paquete sin flags. Mismo comportamiento que FIN scan.

### XMAS Scan
```bash
sudo nmap -sX 192.168.1.1
```
**Explicación**: Envía paquete con FIN, PSH y URG encendidos.

### ACK Scan
```bash
sudo nmap -sA 192.168.1.1
```
**Explicación**: Detecta reglas de firewall. Si recibe RST → no filtrado, si no responde → filtrado.

### Window Scan
```bash
sudo nmap -sW 192.168.1.1
```
**Explicación**: Similar a ACK pero analiza el tamaño de ventana TCP.

### Maimon Scan
```bash
sudo nmap -sM 192.168.1.1
```
**Explicación**: Envía FIN/ACK. Comportamiento similar a FIN scan.

---

## 📨 Escaneos UDP

### UDP Scan Básico
```bash
sudo nmap -sU 192.168.1.1
```
**⚠️ ADVERTENCIA**: Extremadamente lento (minutos/horas). Puertos cerrados responden ICMP Port Unreachable.

### UDP Scan Optimizado
```bash
sudo nmap -sU --top-ports 100 192.168.1.1    # 100 puertos más comunes
sudo nmap -sU -p 53,161,162,123,137,138 192.168.1.1  # Puertos comunes UDP
```

### UDP Scan con Timeouts Ajustados
```bash
sudo nmap -sU --max-retries 1 --min-rate 50 192.168.1.1
```

---

## 🔬 Detección de Servicios y OS

### Detección de Versión de Servicios
```bash
nmap -sV 192.168.1.1                    # Versión básica
nmap -sV --version-intensity 9 192.168.1.1  # Máxima intensidad (0-9)
nmap -sV --version-light 192.168.1.1    # Intensidad 2 (rápido)
nmap -sV --version-all 192.168.1.1      # Prueba todas las pruebas
```

### Detección de Sistema Operativo
```bash
sudo nmap -O 192.168.1.1
sudo nmap -O --osscan-guess 192.168.1.1  # Adivina si no es seguro
sudo nmap -O --osscan-limit 192.168.1.1  # Solo si hay puertos abiertos
```

### Combinación Completa
```bash
sudo nmap -A 192.168.1.1  # -sV -O --traceroute --script default
```

### Traceroute
```bash
nmap --traceroute 192.168.1.1
sudo nmap -sn --traceroute 192.168.1.1  # Solo traceroute
```

---

## 🛠️ Scripting (NSE)

### Categorías de Scripts
```bash
# Scripts por categoría
nmap --script "default" 192.168.1.1
nmap --script "safe" 192.168.1.1
nmap --script "vuln" 192.168.1.1
nmap --script "exploit" 192.168.1.1
nmap --script "auth" 192.168.1.1        # Autenticación
nmap --script "brute" 192.168.1.1       # Fuerza bruta
nmap --script "discovery" 192.168.1.1   # Descubrimiento
nmap --script "dos" 192.168.1.1         # Denegación de servicio
nmap --script "intrusive" 192.168.1.1   # Intrusivos
```

### Ejemplos Específicos
```bash
# Vuln scan común
nmap --script vuln 192.168.1.1

# SSL/TLS testing
nmap --script ssl-enum-ciphers -p 443 192.168.1.1

# SMB enumeration
nmap --script smb-enum-shares -p 445 192.168.1.1

# HTTP information
nmap --script http-enum,http-headers -p 80,443 192.168.1.1

# Multiple scripts
nmap --script "http-* and not http-brute" 192.168.1.1
```

### Opciones de Scripts
```bash
nmap --script-args <args> 192.168.1.1
nmap --script-args "userdb=users.txt,passdb=passwords.txt" --script ftp-brute 192.168.1.1
nmap --script-updatedb  # Actualizar base de datos de scripts
```

---

## ⏱️ Opciones de Timing

### Plantillas de Timing (-T)
```bash
nmap -T0 192.168.1.1    # Paranoid (muy lento, evasión)
nmap -T1 192.168.1.1    # Sneaky
nmap -T2 192.168.1.1    # Polite
nmap -T3 192.168.1.1    # Normal (DEFAULT)
nmap -T4 192.168.1.1    # Aggressive
nmap -T5 192.168.1.1    # Insane (muy rápido, ruidoso)
```

### Control Manual de Timing
```bash
nmap --min-hostgroup 10 192.168.1.0/24      # Mínimo hosts escaneados en paralelo
nmap --max-hostgroup 256 192.168.1.0/24     # Máximo hosts en paralelo
nmap --min-parallelism 10 192.168.1.1        # Mínimo probes en paralelo
nmap --max-parallelism 100 192.168.1.1       # Máximo probes en paralelo
nmap --scan-delay 100ms 192.168.1.1          # Delay entre probes
nmap --max-scan-delay 5s 192.168.1.1         # Delay máximo autoajustable
```

### Control de Paquetes/segundo
```bash
nmap --min-rate 100 192.168.1.1      # Mínimo 100 paquetes/segundo
nmap --max-rate 1000 192.168.1.1     # Máximo 1000 paquetes/segundo
```

### Timeouts y Reintentos
```bash
nmap --initial-rtt-timeout 100ms 192.168.1.1
nmap --max-rtt-timeout 1s 192.168.1.1
nmap --host-timeout 30m 192.168.1.1      # Máximo 30 minutos por host
nmap --max-retries 3 192.168.1.1         # Reintentos (default: 10)
```

---

## 🕵️ Evasión y Stealth

### Fragmentación de Paquetes
```bash
nmap -f 192.168.1.1                    # Fragmenta paquetes (8 bytes)
nmap -ff 192.168.1.1                   # Fragmenta más (16 bytes)
nmap --mtu 24 192.168.1.1              # MTU personalizada
```

### IP/Port Decoys
```bash
nmap -D RND:10 192.168.1.1             # 10 decoys aleatorios
nmap -D decoy1,decoy2,ME,decoy3 192.168.1.1  # Decoys específicos
```

### Source Manipulation
```bash
nmap -S 192.168.1.100 -e eth0 192.168.1.1    # Spoof source IP
nmap --source-port 53 192.168.1.1             # Usar puerto fuente específico
nmap -g 80 192.168.1.1                       # Usar puerto fuente específico
```

### Otros Métodos de Evasión
```bash
nmap --data-length 100 192.168.1.1            # Añade datos aleatorios
nmap --ttl 128 192.168.1.1                    # Modifica TTL
nmap --randomize-hosts 192.168.1.0/24         # Aleatoriza orden de hosts
nmap --spoof-mac Apple 192.168.1.1            # Spoof MAC address
nmap --badsum 192.168.1.1                     # Usa checksums inválidos (IDS evasion)
```

---

## 📊 Salida y Formato

### Formatos de Salida
```bash
nmap -oN scan.txt 192.168.1.1          # Normal
nmap -oX scan.xml 192.168.1.1          # XML
nmap -oG scan.gnmap 192.168.1.1        # Grepable
nmap -oS scan.script 192.168.1.1       # Script kiddie
nmap -oA scan 192.168.1.1              # Todos los formatos (normal, XML, grepable)
```

### Verbosidad y Debugging
```bash
nmap -v 192.168.1.1                    # Verbose
nmap -vv 192.168.1.1                   # Más verbose
nmap -d 192.168.1.1                    # Debug level 1
nmap -dd 192.168.1.1                   # Debug level 2
nmap --packet-trace 192.168.1.1        # Trace packets sent/received
nmap --reason 192.168.1.1              # Muestra razón del estado
nmap --stats-every 10s 192.168.1.1     # Stats cada 10 segundos
nmap --iflist                          # Lista interfaces de red
```

---

## 🚀 Escaneos Avanzados

### Selección de Puertos
```bash
nmap -p 1-1000 192.168.1.1             # Rango
nmap -p 80,443,8080 192.168.1.1        # Lista específica
nmap -p U:53,T:80,443 192.168.1.1      # Mixto TCP/UDP
nmap -p http,https 192.168.1.1         # Por nombre de servicio
nmap -p "*" 192.168.1.1                # Todos los puertos
nmap -p- 192.168.1.1                   # Todos (1-65535)
nmap --top-ports 100 192.168.1.1       # 100 más comunes
```

### Escaneo de Banner Grabbing
```bash
nmap -sV --script=banner 192.168.1.1
nmap -p 21,22,25,80 --script=banner 192.168.1.1
```

### Escaneo de IPv6
```bash
nmap -6 2001:db8::1
nmap -6 -sT fe80::a00:27ff:fe43:1518
```

### Escaneo a través de Proxy
```bash
nmap --proxies socks4://127.0.0.1:9050 192.168.1.1
```

### Escaneo con TOR
```bash
nmap -sT -Pn --proxies socks4://127.0.0.1:9050 192.168.1.1
```

### Firewall/IDS Evasion
```bash
# Escaneo muy lento para evitar detección
nmap -T0 --max-parallelism 1 --scan-delay 5s 192.168.1.1

# Escaneo con diferentes técnicas
nmap -sS -sV -O -T4 -f --mtu 16 --data-length 50 --randomize-hosts 192.168.1.1
```

---

## 📁 Perfiles Personalizados

### Crear Perfil Personalizado
```bash
# Crear archivo ~/.nmap/mi-perfil.nse
--max-retries 1
--min-rate 100
-T4
-v
--open
```

### Usar Perfil
```bash
nmap --datadir ~/.nmap --script mi-perfil 192.168.1.1
```

### Ejemplo: Perfil Pentest Completo
```bash
# ~/.nmap/pentest-completo.nse
-sS          # SYN scan
-sV          # Version detection
-O           # OS detection
--traceroute
--script default,vuln
--min-rate 150
--max-retries 2
-T4
-v
-oA pentest-scan
```

---

## 🎯 Escenarios Comunes

### Reconocimiento Inicial
```bash
nmap -sn 192.168.1.0/24                # Descubrir hosts activos
nmap -sS -p- -T4 192.168.1.100         # Escaneo completo TCP
nmap -sU --top-ports 50 192.168.1.100  # Puertos UDP comunes
```

### Pentest Web Server
```bash
nmap -p 80,443,8080,8443 -sV --script=http-enum,http-vuln* 192.168.1.100
```

### Escaneo Sigiloso
```bash
sudo nmap -sS -T2 --max-parallelism 10 --scan-delay 100ms -f 192.168.1.100
```

### Escaneo Rápido
```bash
nmap -T5 --min-rate 1000 --max-retries 0 -p- 192.168.1.100
```

### Auditar Firewall
```bash
sudo nmap -sA -p 1-1000 192.168.1.1    # Detectar reglas de firewall
sudo nmap -sF -p 1-1000 192.168.1.1    # Evasión básica
```

---

## ⚠️ Consideraciones Legales y Éticas

### **ANTES DE ESCANEAR:**
1. ✅ **Permiso por escrito** del propietario del sistema
2. ✅ **Redes propias** o de prueba controladas
3. ✅ **Entornos autorizados** para pentesting

### **NUNCA ESCANEAR:**
1. ❌ Redes públicas sin autorización
2. ❌ Sistemas críticos (hospitales, aeropuertos)
3. ❌ Infraestructura gubernamental

### **Mejores Prácticas:**
```bash
# Siempre documentar comandos usados
# Usar opciones -v para logging
# Guardar evidencias con -oA
# Considerar impacto en red (timing conservative)
```

---

## 🆘 Troubleshooting

### Problemas Comunes y Soluciones

| Problema | Causa Probable | Solución |
|----------|---------------|----------|
| `sudo nmap` no funciona | Permisos, SELinux | `sudo -s` o revisar políticas |
| Escaneo UDP muy lento | Timeouts, pérdida de paquetes | Usar `--max-retries 1`, `--top-ports` |
| Host parece apagado | Bloqueo ICMP | Usar `-Pn` |
| Output truncado | Buffer de terminal | Redirigir a archivo `-oN` |
| No detecta servicios | Servicios no estándar | Usar `-sV --version-all` |

### Comandos de Diagnóstico
```bash
nmap --iflist                          # Ver interfaces disponibles
nmap --packet-trace 192.168.1.1        # Ver tráfico de red
nmap -d3 192.168.1.1                   # Debug detallado
sudo tcpdump -i eth0 host 192.168.1.1  # Capturar tráfico durante escaneo
```

---

## 📚 Recursos Adicionales

### Documentación Oficial
```bash
man nmap                    # Manual completo
nmap -h                     # Ayuda rápida
nmap --servicedb           # Base de datos de servicios
nmap --versiondb           # Base de datos de versiones
```

### Actualización
```bash
sudo nmap --script-updatedb            # Actualizar scripts NSE
# En Kali: sudo apt update && sudo apt upgrade nmap
```

### Comunidad
- **Sitio oficial**: https://nmap.org
- **NSE Documentation**: https://nmap.org/nsedoc/
- **Nmap Book**: "Nmap Network Scanning" (Gordon Lyon)

