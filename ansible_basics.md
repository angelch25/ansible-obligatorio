¿Qué es Ansible? Mencione dos actividades que se puedan hacer con Ansible
Ansible es una herramienta de automatización de TI de código abierto que permite
gestionar la configuración, el aprovisionamiento y la implementación de aplicaciones
en múltiples sistemas de forma sencilla y eficiente. Funciona sin necesidad de agentes
instalados en los nodos, utilizando SSH para conectarse a los servidores.

Dos actividades que se pueden hacer con Ansible:
Automatizar la configuración de servidores: Puedes instalar y configurar servicios
como Apache, NGINX, MySQL, etc., en múltiples servidores con un solo comando,
asegurando que todos estén configurados de forma idéntica.

Desplegar aplicaciones: Ansible permite automatizar todo el proceso de despliegue de
aplicaciones, desde clonar el repositorio, instalar dependencias, hasta reiniciar
servicios o realizar actualizaciones sin intervención manual.

¿Qué es un playbook de Ansible?
Un playbook de Ansible es un archivo escrito en formato YAML que define un conjunto
de instrucciones (llamadas plays) que Ansible debe ejecutar en uno o más hosts. Los
playbooks son la manera principal de automatizar tareas complejas y repetitivas, como
la configuración de sistemas, instalación de software o despliegue de aplicaciones.

Características principales de un playbook:
Está estructurado en listas y diccionarios YAML.
Es declarativo: describes el estado que deseas alcanzar, no los pasos exactos
para lograrlo.
Define roles, tareas, variables, módulos y más.
Permite reutilización de código y organización modular.

¿Qué información contiene un inventario de Ansible?
Un inventario de Ansible contiene la información sobre los hosts (servidores) que
Ansible va a gestionar. Es una pieza clave porque le dice a Ansible a qué máquinas
conectarse, cómo hacerlo y cómo agruparlas para organizar mejor las tareas.
Tipos de inventario:
Estático: archivo plano (por ejemplo, en formato INI o YAML).
Dinámico: generado automáticamente desde una fuente externa (como AWS, Azure,
etc.).

Explique que es un módulo de Ansible y dé un ejemplo.

Un módulo de Ansible es una unidad de trabajo reutilizable que Ansible utiliza para
realizar tareas específicas en los hosts gestionados. Los módulos son la forma en que
Ansible interactúa con los sistemas: realizan operaciones como copiar archivos,
instalar paquetes, gestionar servicios, entre otras.

Características de los módulos:
Autónomos: cada módulo está diseñado para realizar una tarea específica.
Idempotentes: esto significa que pueden ejecutarse varias veces sin cambiar el
estado del sistema si ya está en el estado deseado.
Abstracción: muchos módulos proporcionan una interfaz sencilla para tareas
complejas sin tener que lidiar con los detalles de bajo nivel.
Multiplataforma: Ansible tiene módulos para interactuar con sistemas Linux,
Windows, redes, nubes, etc.

Ejemplo de módulo de Ansible: apt
El módulo apt se usa para gestionar paquetes en sistemas basados en
Debian/Ubuntu.

Uso básico:
yaml
---
   - name: Instalar el paquete htop en todos los servidores
	hosts: servidores_linux
	tasks:
   - name: Instalar htop
	apt:
	name: htop
	state: present
	update_cache: yes

En este ejemplo:
El módulo apt se usa para instalar el paquete htop en los servidores que pertenecen al
grupo servidores_linux.
state: present asegura que el paquete esté instalado. Si ya está instalado, no hará
nada.
update_cache: yes actualiza la caché de paquetes antes de instalar.

Tipos de módulos más comunes en Ansible:
Gestión de paquetes: apt, yum, pip, dnf.
Gestión de archivos y directorios: copy, template, file, fetch.
Gestión de servicios: service, systemd, win_service.
Gestión de usuarios y grupos: user, group.
Gestión de sistemas: reboot, hostname, timezone.

¿Qué ventajas tiene Ansible sobre otros métodos de automatización?

1. Simplicidad y Facilidad de Uso
Sin necesidad de agentes: Ansible no requiere que se instalen agentes en los nodos
remotos, lo que lo hace mucho más fácil de implementar y mantener. Solo necesitas
SSH y Python (preinstalado en la mayoría de sistemas Linux).

Sintaxis sencilla y declarativa: Los playbooks de Ansible están escritos en YAML, un
formato legible y sencillo de entender, lo que facilita la escritura y la colaboración entre
equipos.

2. Idempotencia
Los módulos de Ansible son idempotentes, lo que significa que puedes ejecutar las
mismas tareas múltiples veces y el sistema solo realizará cambios si es necesario.
Esto asegura que el estado final sea siempre el mismo, sin importar cuántas veces
ejecutes el playbook.

3. Configuración como Código (IaC)
Al usar archivos YAML para definir la infraestructura, Ansible permite definir toda la
configuración y gestión de infraestructura como código. Esto facilita la versionabilidad,
auditoría y la recuperación de configuraciones.

4. Escalabilidad
Aunque Ansible no requiere agentes, puede gestionar un número muy alto de nodos
simultáneamente mediante conexiones SSH, lo que lo hace adecuado para entornos
grandes.
Control de inventarios: Ansible permite organizar los hosts en grupos, facilitando la
administración de entornos complejos.

5. Sin Dependencias Externas

Ansible es autónomo y no necesita servidores adicionales o bases de datos para
funcionar. La única dependencia real es SSH y Python, lo que lo hace muy ligero y
fácil de instalar.

6. Automatización de Despliegues y Configuración Completa
Ansible no solo se utiliza para tareas de configuración, sino que también es útil para la
orquestación de aplicaciones, despliegues de software y gestión de contenedores
(como Docker).

Esto lo convierte en una herramienta versátil que abarca desde la configuración de
servidores hasta la implementación continua de aplicaciones.

7. Comunidad y Ecosistema
Gran comunidad: Ansible tiene una comunidad activa y extensa, lo que asegura un
flujo constante de actualizaciones y nuevos módulos. Además, Ansible Galaxy ofrece
roles y colecciones listas para usar, lo que facilita la reutilización de configuraciones y
buenas prácticas.

Soporte de múltiples plataformas: Ansible soporta una gran cantidad de plataformas,
desde sistemas operativos (Linux, Windows, macOS) hasta servicios de nube (AWS,
Azure, GCP) y herramientas de orquestación de contenedores (Docker, Kubernetes).

8. No es necesario tener conocimientos profundos de programación
Ansible está diseñado para ser accesible a personas que no tienen experiencia en
programación. Aunque puedes escribir tareas complejas, no es necesario ser un
experto en desarrollo de software para crear playbooks útiles.

9. Gestión de Configuraciones y Orquestación de Flujos
Ansible no solo configura máquinas, también puede orquestar flujos de trabajo
complejos entre diferentes máquinas. Esto lo hace útil no solo para configuraciones de
infraestructura, sino también para la gestión de procesos entre servidores.

10. Ejecución Remota en Tiempo Real
Los playbooks de Ansible se pueden ejecutar de forma ad-hoc, es decir, puedes hacer
cambios o ejecutar tareas en sistemas remotos en tiempo real, sin necesidad de una
configuración previa. Esto es útil para tareas rápidas o de emergencia.

Comparación con Otros Métodos de Automatización
Chef y Puppet
Chef/Puppet: Son herramientas potentes y ampliamente utilizadas, pero requieren la
instalación de agentes en los nodos. Además, usan lenguajes de programación (Ruby
para Chef, Puppet DSL para Puppet), lo que puede ser una barrera para quienes no
tienen experiencia en programación.

Ansible: No necesita agentes y usa un lenguaje sencillo (YAML), lo que lo hace mucho
más accesible.

SaltStack
SaltStack: También es muy potente y permite la ejecución en tiempo real, pero al igual
que Chef y Puppet, depende de agentes (aunque tiene modo sin agente).

Ansible: La ventaja de Ansible es que es más fácil de aprender y tiene un modelo sin
agente por defecto.

Scripts Tradicionales (Bash, Python, etc.)
Scripts: Si bien los scripts pueden ser una solución rápida, carecen de la
idempotencia, modularidad y escabilidad que ofrece Ansible. Además, no tienen la
misma facilidad de uso ni permiten una gestión estructurada de la infraestructura.

Ansible: Proporciona un enfoque más organizado y escalable, con la ventaja adicional
de ser más seguro y menos propenso a errores humanos.

En resumen, Ansible sobresale principalmente por su simplicidad, accesibilidad, y por
la manera en que permite automatizar tareas de forma rápida y eficaz sin la necesidad
de una infraestructura compleja.
