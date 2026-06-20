---
title: "Infraestructura Convergente: Enrutamiento, MPLS-TE y Servicios Contenerizados"
excerpt: "Simulación avanzada en GNS3 que integra OSPF, BGP y despliegue de microservicios en Docker con análisis perceptual de video (VMAF)."
collection: portfolio
teaser: /images/topologia-gns3.png
---

## Resumen Ejecutivo del Proyecto

Para validar la convergencia de los protocolos de misión crítica, se diseñó la siguiente arquitectura de red dentro del entorno de emulación GNS3:

![Topología Completa GNS3](/images/topologia-gns3.png)

*La topología expone la segmentación de los Sistemas Autónomos (AS 5 para el Core y AS 10 para la zona de servidores), la ubicación de los routers de borde con sus respectivas sesiones eBGP y la ruta explícita del túnel RSVP-TE.*

Este proyecto demuestra el diseño de una infraestructura de red corporativa a gran escala utilizando **GNS3** como entorno de emulación crítica. El escenario integra la interoperabilidad de múltiples Sistemas Autónomos (AS), la optimización del core mediante MPLS y la entrega de servicios contenerizados de alta demanda en el borde de la red.

### Objetivos de Ingeniería Alcanzados

1. **Core de Red Robusto:** Configuración de un backbone (AS 5) utilizando **OSPF** como protocolo de pasarela interna. Se implementó un esquema **iBGP con Router Reflector (RR)** para eliminar la necesidad de una malla completa (*Full Mesh*), reduciendo drásticamente la carga de procesamiento en los equipos de enrutamiento.
2. **Ingeniería de Tráfico con MPLS-TE:** Ante la necesidad de transportar flujos multimedia críticos (Streaming de Video), se diseñó un túnel **RSVP-TE** con una reserva estricta de **50 Mbps**. Esto garantiza que el tráfico de video viaje por un camino dedicado y optimizado, inmune a las ráfagas de tráfico de datos generales de la red.
3. **Borde Contenerizado (Docker):** El sistema autónomo de servicios (AS 10) fue virtualizado usando contenedores controlados por **Docker-Compose**. Se desplegaron de forma aislada:
   * Servidor de streaming y transcodificación (**NGINX + FFmpeg**).
   * Plataforma multimedia local (**Jellyfin**).
   * Servidor de correo corporativo seguro (**Poste.io**).
   * Almacenamiento en la nube privada (**Nextcloud**).
4. **Túneles de Transición (GRE):** Implementación de túneles **GRE** para interconectar de manera transparente redes heterogéneas IPv4/IPv6 sin recurrir a implementaciones complejas de doble stack en el core.

---

## Evaluación Científica de Calidad de Video (QoS vs. QoE)

Para validar la eficiencia de la infraestructura, se sometió al protocolo **RTMP** abajo estrés, se implementó un script de automatización en Python conectado a **FFmpeg** y la librería **Libvmaf**. Se contrastó el flujo de video original contra cuatro escenarios de degradación controlada en el cliente.

* **VMAF (Video Multi-Method Assessment Fusion):** Desarrollado por Netflix, utiliza Machine Learning para evaluar la calidad tal como la vería un ojo humano.
* **SSIM & PSNR:** Métricas matemáticas estructurales y de error de píxeles.

A continuación se presenta la correlación matemática y perceptual de los resultados obtenidos en el laboratorio:

![Comparativa de Métricas de Calidad de Video RTMP](/images/METRICAS.png)


### Análisis de la Evidencia Experimental

Como se observa en la gráfica de barras comparativa (*Métricas RTMP Comparación*), el rendimiento de la transmisión de video expone comportamientos críticos dependiendo del tipo de degradación de red:

* **Escenarios de Limitación de Ancho de Banda:** Las restricciones moderadas de ancho de banda mantuvieron niveles estables en las tres métricas debido a que el bit rate del flujo de video se encontraba optimizado por debajo del umbral de saturación. Sin embargo, al aplicar límites extremos, se hace evidente una divergencia: Mientras que las métricas basadas puramente en píxeles estructurales (**PSNR** y **SSIM**) decrecen linealmente, el indicador de percepción humana (**VMAF**) cae abruptamente. Esto demuestra técnicamente que el congelamiento de fotogramas penaliza la experiencia del usuario (QoE) con mayor severidad de lo que el análisis estático de imágenes estima.

### Conclusiones Técnicas del Análisis:
* Las métricas tradicionales (PSNR) suelen ocultar micro-cortes que el ojo humano sí percibe de forma molesta.
* La **pérdida de paquetes (incluso un 5%)** destruye la experiencia perceptual (caída crítica del score VMAF), debido a los tiempos de retransmisión TCP en transmisiones en vivo, lo que justifica la necesidad absoluta de implementar Ingeniería de Tráfico (MPLS-TE) como se hizo en este proyecto.
* La métrica más importante sigue siendo la **calidad de la experiencia (QoE)**, ya que nos da una noción real de lo que el usuario percibe y no solo se basa en modelos entrenados.
