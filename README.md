# Proyecto Ansible Obligatorio

Playbook: hardening.yml

YAML

---
- name: Hardening de servidor Ubuntu
  hosts: all
  become: yes
  vars:
    ansible_ssh_user: sysadmin
    
  handlers:
    - name: reiniciar_sistema
      ansible.builtin.reboot:
      when: ansible_os_family == "Debian"

    - name: reiniciar_ssh
      ansible.builtin.service:
       name: sshd
       state: restarted
      when: ansible_os_family == "Debian"

  tasks:
    - name: Actualizar todos los paquetes del sistema
      ansible.builtin.apt:
       update_cache: yes
       upgrade: dist
      notify: reiniciar_sistema
      when: ansible_os_family == "Debian"

    - name: Habilitar y configurar ufw
      ansible.builtin.ufw:
       state: enabled
       policy: deny
       direction: incoming
       rule: allow
       port: ssh
       proto: tcp
      when: ansible_os_family == "Debian"

    - name: Deshabilitar login de root y autenticación por contraseña
      ansible.builtin.lineinfile:
       path: /etc/ssh/sshd_config
       regexp: '^PermitRootLogin'
       line: 'PermitRootLogin no'
      notify: reiniciar_ssh
      when: ansible_os_family == "Debian"

    - name: Deshabilitar autenticación por contraseña
      ansible.builtin.lineinfile:
       path: /etc/ssh/sshd_config
       regexp: '^PasswordAuthentication'
       line: 'PasswordAuthentication no'
      notify: reiniciar_ssh
      when: ansible_os_family == "Debian"

    - name: Instalar fail2ban
      ansible.builtin.apt:
       name: fail2ban
       state: present
       update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Habilitar y arrancar el servicio de fail2ban
      ansible.builtin.systemd:
       name: fail2ban
       enabled: yes
       state: started
      when: ansible_os_family == "Debian"

Explicación del Playbook

    hosts: all: Este playbook se ejecutará en todos los servidores definidos en el inventario. Sin embargo, todas las tareas y handlers tienen la condición when: ansible_os_family == "Debian" para asegurarse de que solo se apliquen a los servidores Ubuntu.

    become: yes: Todas las tareas se ejecutarán con privilegios de root (sudo).

    handlers: Los handlers son tareas que se ejecutan solo cuando se les notifica un cambio.

       reiniciar_sistema: Reinicia el servidor. Esta asociado a la tarea de actualización de paquetes, ya que a menudo es necesario para aplicar los cambios del kernel y otros componentes del sistema.

       reiniciar_ssh: Reinicia el servicio de SSH. Se notifica cuando se cambian las configuraciones de SSH para que los nuevos ajustes, como la restricción de login de root, se apliquen inmediatamente.

    tasks:

       Actualización de paquetes: Usa el módulo apt para actualizar todos los paquetes. Si se realiza una actualización, se notifica al handler reiniciar_sistema.

       Configuración de UFW: El módulo ufw se encarga de habilitar el firewall (state: enabled), denegar todo el tráfico entrante por defecto (policy: deny) y luego crear una regla para permitir específicamente las conexiones SSH.

       Configuración de SSHD: El módulo lineinfile se usa dos veces para modificar el archivo /etc/ssh/sshd_config. La primera tarea asegura que el login de root esté deshabilitado, y la segunda asegura que la autenticación por contraseña esté deshabilitada. Ambas tareas notifican al handler reiniciar_ssh.

       Fail2ban: Se encarga de instalar el paquete fail2ban y luego se asegura de que su servicio esté habilitado y en ejecución para proteger contra intentos de conexión SSH fallidos.
       
 
 
 
 Playbook: nfs_setup.yml
 
 ---

- name: Configurar servidor NFS para CentOS y Ubuntu

  hosts: nfs_servers

  become: yes


  vars:

    nfs_shared_dir: "/var/nfs_shared"

    nfs_exports_file: "/etc/exports"

    nfs_port: 2049


  tasks:

    - name: Instalar el paquete NFS en CentOS

      ansible.builtin.yum:

       name: nfs-utils

       state: present

      when: ansible_os_family == "RedHat"


    - name: Instalar el paquete NFS en Ubuntu

      ansible.builtin.apt:

       name: nfs-kernel-server

       state: present

       update_cache: yes

      when: ansible_os_family == "Debian"


    - name: Asegurar que el servicio NFS esté iniciado y habilitado en CentOS

      ansible.builtin.systemd:

       name: nfs-server

       state: started

       enabled: yes

      when: ansible_os_family == "RedHat"


    - name: Asegurar que el servicio NFS esté iniciado y habilitado en Ubuntu

      ansible.builtin.systemd:

       name: nfs-kernel-server

       state: started

       enabled: yes

      when: ansible_os_family == "Debian"

- name: Permitir el servicio NFS en el firewall (firewalld para CentOS)

      ansible.posix.firewalld:

       service: nfs

       permanent: yes

       state: enabled

      when: ansible_os_family == "RedHat"

      notify: "recargar_firewall_centros"


    - name: Permitir el servicio NFS en el firewall (ufw para Ubuntu)

      ansible.builtin.command: ufw allow 2049/tcp

      when: ansible_os_family == "Debian"


    - name: Crear el directorio compartido en CentOS

      ansible.builtin.file:

       path: "{{ nfs_shared_dir }}"

       state: directory

       owner: nobody

       group: nobody

       mode: '0777'

      when: ansible_os_family == "RedHat"


    - name: Crear el directorio compartido en Ubuntu

      ansible.builtin.file:

       path: "{{ nfs_shared_dir }}"

       state: directory

       owner: nobody

       group: nogroup

       mode: '0777'

      when: ansible_os_family == "Debian"


handlers:

    - name: recargar_exportaciones_nfs

      ansible.builtin.command: exportfs -ra

      listen: "recargar_exportaciones_nfs"


    - name: recargar_firewall_centros

      ansible.builtin.command: firewall-cmd --reload

      listen: "recargar_firewall_centros"

      when: ansible_os_family == "RedHat" 
  
 name y hosts: El playbook se llama "Configurar servidor NFS para CentOS y Ubuntu" y se ejecutará en los servidores que estén definidos en el grupo nfs_servers del inventario de Ansible. La línea become: yes le dice a Ansible que ejecute todas las tareas como (root).

  vars: Aquí se definen variables para hacer el código más fácil de leer y modificar.

    nfs_shared_dir: Es la ruta del directorio que se va a compartir.

    nfs_exports_file: Es la ubicación del archivo de configuración de NFS.

    nfs_port: Es el puerto que usa el servicio NFS.

 tasks: Esta es la parte principal donde se definen las tareas a ejecutar. El playbook utiliza la condición when: ansible_os_family == "RedHat" o when: ansible_os_family == "Debian" para decidir qué tareas realizar en cada tipo de sistema operativo.

    Instalación de paquetes: Instala el paquete necesario para NFS. Usa dnf para CentOS (RedHat) y apt para Ubuntu (Debian).

    Gestión de servicios: Se asegura de que el servicio de NFS esté iniciado (started) y habilitado para que se inicie automáticamente al arrancar el servidor (enabled: yes). Nuevamente, usa nombres de servicios diferentes para cada sistema operativo (nfs-server vs. nfs-kernel-server).

    Configuración del firewall: Abre el puerto NFS (2049/tcp) en el firewall. Usa el módulo firewalld para CentOS y el comando ufw para Ubuntu.

    Creación del directorio: Crea el directorio que se va a compartir (/var/nfs_shared) con los permisos correctos (mode: '0777').

 handlers: Los handlers son tareas que se ejecutan solo si una tarea anterior les envía una señal (usando notify). En este caso, solo se definen dos handlers. Uno de ellos recargará el firewall de CentOS para que los cambios surtan efecto.
 
 
 
