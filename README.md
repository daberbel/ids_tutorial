# Tutorial IDS/IPS con Suricata y ELK
Esto es una prueba de tutorial para que los alumnos accedan.

# Suricata
Suricata es un sistema de detección y prevención de intrusiones (IDS/IPS) de código abierto, que también funciona como un analizador de tráfico de red. Fue desarrollado por la Open Information Security Foundation (OISF) y es ampliamente utilizado para monitorear y proteger redes contra amenazas cibernéticas.

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
