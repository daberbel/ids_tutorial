# Tutorial IDS/IPS con Suricata y ELK
Esto es una prueba de tutorial para que los alumnos accedan.

# Suricata 
Suricata es un motor de análisis de tráfico que opera inspeccionando paquetes de red y comparándolos con reglas predefinidas para detectar amenazas. Se puede usar para la creación de sistemas de detección y prevención de intrusiones (IDS/IPS) de código abierto junto a otras herramientas como ELK Stack (Elasticsearch, Logstash, Kibana). Fue desarrollado por la Open Information Security Foundation (OISF) y es ampliamente utilizado para monitorear y proteger redes contra amenazas cibernéticas.

Sus principales funciones son:
* 📡 Detección de intrusiones (IDS): Analiza el tráfico de red en tiempo real para detectar actividades sospechosas o maliciosas, comparándolas con firmas o reglas conocidas (similares a Snort).
* 🔥 Prevención de intrusiones (IPS): No solo detecta, sino que puede bloquear o interrumpir conexiones peligrosas automáticamente.
* 🔍 Análisis de protocolos: Inspecciona y decodifica múltiples protocolos (HTTP, TLS, DNS, SMB, etc.) para buscar anomalías o ataques.
* 📊 PCAP y registro de eventos: Captura y registra el tráfico para realizar análisis forenses o investigaciones post-incidente.
* ⚡ Alto rendimiento: Optimizado para manejar grandes volúmenes de tráfico, aprovechando múltiples núcleos de CPU y aceleración por hardware.
* 📈 Compatibilidad con herramientas de análisis: Se integra bien con sistemas como ELK Stack (Elasticsearch, Logstash, Kibana), Zeek o Splunk para visualizar y correlacionar eventos.

En resumen, Suricata es una herramienta muy poderosa para proteger redes modernas contra amenazas avanzadas.

## Modos de Suricata
Puede trabajar en tres modos:
1. Modo IDS (Intrusion Detection System)
    * Pasivo: Monitorea el tráfico, detecta amenazas y genera alertas, pero no bloquea nada.
    * Se usa con herramientas como Wireshark, Kibana o Splunk para visualizar eventos.
2. Modo IPS (Intrusion Prevention System)
    * Activo: Además de detectar, bloquea tráfico malicioso.
    * Se integra con iptables/netfilter en Linux para filtrar paquetes.
3. Modo de captura de paquetes (PCAP)
    * Similar a Wireshark: Guarda tráfico en archivos para análisis posterior.

## Funcionamiento de Suricata
Fases del procesamiento en Suricata
1. Captura de paquetes → Obtiene tráfico desde una interfaz de red o archivos PCAP.
2. Decodificación → Interpreta protocolos (Ethernet, TCP, UDP, HTTP, TLS, etc.).
3. Análisis de flujo → Ensambla sesiones de comunicación para detectar patrones más complejos.
4. Inspección de reglas → Compara los paquetes con las reglas configuradas para detectar amenazas.
5. Acciones y registros → Si una regla coincide, genera alertas o bloquea el tráfico.

## Instalación de Suricata
Instalación en Linux (Ubuntu)
1. Actualizar repositorios:
```
sudo apt update && sudo apt upgrade -y
```
2. Añadir repositorio de Suricata:
```
sudo add-apt-repository ppa:oisf/suricata-stable -y
sudo apt update
```
3. Instalar Suricata:
```
sudo apt install suricata -y
```
4. Verificar instalación:
```
suricata --build-info
```

5. Habilitar y arrancar el servicio:
```
sudo systemctl enable --now suricata
```

## Configuración de Suricata
* 🎯 Las reglas son el núcleo de Suricata, definiendo qué tráfico vigilar o bloquear.
* 🛠 Puedes crear y gestionar tus propias reglas en local.rules.
* 🚀 Con suricata-update puedes mantener actualizadas las reglas comunitarias.
* 🧩 La gestión eficiente en suricata.yaml es clave para un IDS/IPS organizado.

Suricata usa archivos de reglas (`.rules`) para definir el comportamiento del IDS/IPS.

Principales ubicaciones:
* Directorio principal: `/etc/suricata/`
* Configuración principal: `/etc/suricata/suricata.yaml`
* Reglas oficiales: `/etc/suricata/rules/`

| Archivo         | Descripción                                    |
|-----------------|------------------------------------------------|
| `suricata.rules`| Archivo combinado con todas las reglas activas.|
| `local.rules`   | Archivo recomendado para reglas personalizadas.|

### Configuración con suricata.yaml
Localiza las siguientes secciones en el archivo `/etc/suricata/suricata.yaml`
```yaml
# Configuración de la interfaz de red
af-packet:
  - interface: eth0

# Configuración de logs
outputs:
  - eve-log:
      enabled: yes
      filetype: json
      filename: /var/log/suricata/eve.json

# Archivos de reglas
default-rule-path: /etc/suricata/rules
rule-files:
  - suricata.rules
  - local.rules

```
Para aplicar cambios ejecuta: `sudo systemctl restart suricata`
<br>
O si prefieres probar directamente sin tocar el servicio, puedes lanzar Suricata en modo prueba:
```
sudo suricata -c /etc/suricata/suricata.yaml -i eth0
```

---

### Configuración de las reglas
Dentro del archivo `suricata.yaml`, indicamos los archivos `.rules` que Suricata cargará:
```yaml
rule-files:
  - suricata.rules
  - local.rules
  - emerging-threats.rules
```

### Orden de los archivos de reglas
Cuando Suricata carga las reglas, las procesa en el orden en que aparecen en la lista. Esto afecta principalmente a dos aspectos importantes:
   1. Orden de evaluación de reglas: Suricata analiza los paquetes siguiendo el orden de los archivos.
   2. Sobrescritura de reglas: si tienes reglas duplicadas (la misma firma o ID de regla) en diferentes archivos, la última regla cargada sobrescribirá las anteriores.

📘 Estrategia recomendada:
   * Reglas oficiales o predefinidas primero: como las de Emerging Threats o las reglas base de Suricata.
   * Reglas personalizadas al final: para que puedas ajustar o sobrescribir reglas sin modificar los archivos originales (esto evita problemas al actualizar).

Un ejemplo de orden ótpimo sería:
```yaml
rule-files:
  - emerging-threats.rules      # Reglas comunitarias actualizadas
  - suricata.rules              # Reglas base o estándar
  - local.rules                 # Reglas personalizadas o ajustes locales
```

---

### Agregar una regla personalizada
Para agregar una regla personalizada debemos editar el archivo de reglas `local.rules`.

Editar el archivo de reglas:
```
sudo nano /etc/suricata/rules/local.rules
```
Agrega la siguiente regla:
```
alert tcp any any -> any 80 (msg:"Tráfico HTTP detectado"; sid:100002; rev:1;)
```
Para aplicar la nueva regla tenemos 2 opciones:
```
sudo systemctl restart suricata   // Reinicia completamente Suricata (detiene y vuelve a iniciar).
pkill -HUP suricata               // Recarga reglas y configuración sin interrumpir la captura de tráfico.
```

