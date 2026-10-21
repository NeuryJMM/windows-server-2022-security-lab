[← Volver al laboratorio](../README.md)

# Windows Server 2022 + Active Directory + Cliente Windows 10 en dominio

![Windows Server](https://img.shields.io/badge/Windows%20Server-2022-c084fc?style=flat-square&logo=windows&logoColor=white)
![Active Directory](https://img.shields.io/badge/Active%20Directory-AD%20DS-f472b6?style=flat-square)
![VMware](https://img.shields.io/badge/VMware-Workstation%20Pro%2017-60a5fa?style=flat-square&logo=vmware&logoColor=white)
![Nivel](https://img.shields.io/badge/Nivel-Laboratorio-fbbf24?style=flat-square)

How-To paso a paso de la **Práctica 1** de *Seguridad de Sistemas Operativos* (ITLA). Se construye un laboratorio de dominio desde cero: se instala Windows Server, se activa Active Directory y se une un cliente Windows al dominio.

## Objetivos

| Punto | Descripción |
|:--:|---|
| **A** | Instalar Windows Server 2019 o superior (en este caso, **2022**) |
| **B** | Activar los servicios de Active Directory y usar el mismo nombre del equipo para el dominio |
| **C** | Instalar un cliente Windows 8 o superior (en este caso, **Windows 10**) y agregarlo al dominio |

## Datos del laboratorio

| Elemento | Valor |
|---|---|
| Hipervisor | VMware Workstation Pro 17 |
| Servidor | Windows Server 2022 Standard Evaluation (con experiencia de escritorio) |
| Nombre del servidor | `Neury0829` |
| Disco del servidor | 60 GB, dividido en múltiples archivos |
| IP del servidor | `192.168.0.10` |
| Dominio (bosque raíz) | `Neury0829.local` |
| Nombre NetBIOS | `NEURY08290` |
| Cliente | Windows 10 Pro, nombre de equipo `WS0829` |

## Requisitos previos

- VMware Workstation Pro instalado.
- ISO de **Windows Server 2022** (versión de evaluación) descargada.
- ISO o máquina virtual de **Windows 10** ya instalada para el Punto C.
- Ambas máquinas virtuales en la misma red (en este laboratorio, adaptador en modo *Bridged*).

> [!NOTE]
> La ISO de evaluación de Windows Server funciona sin clave de producto durante 180 días. Por eso, más adelante, se continúa sin ingresar clave.

## Índice

- [Parte A: Instalar Windows Server 2022](#parte-a-instalar-windows-server-2022)
- [Parte B: Activar Active Directory](#parte-b-activar-active-directory-y-crear-el-dominio)
- [Parte C: Unir un cliente Windows 10 al dominio](#parte-c-unir-un-cliente-windows-10-al-dominio)
- [Verificación final](#verificación-final)
- [Solución de problemas](#solución-de-problemas-frecuentes)

---

## Parte A: Instalar Windows Server 2022

Antes de iniciar, nos encontramos en el escritorio de Windows. Para instalar Windows Server 2019 o superior necesitamos un hipervisor; en este caso utilizaremos **VMware Workstation Pro**.

![Escritorio de Windows antes de iniciar](images/a00-escritorio.jpg)


### Crear la máquina virtual

**1.** Abrimos VMware Workstation Pro con doble clic en su icono.

![Icono de VMware Workstation Pro en el escritorio](images/a01-icono-vmware.jpg)

![Pantalla de inicio de VMware Workstation Pro 17](images/a01-vmware-abierto.jpg)


**2.** Para crear una nueva máquina virtual, hacemos clic en **Create a New Virtual Machine**.

![Opción Create a New Virtual Machine](images/a02-crear-nueva-vm.jpg)


**3.** Se abre el asistente del hipervisor para crear máquinas virtuales. Dejamos la opción recomendada, **Typical**, y hacemos clic en **Next**.

![Asistente de nueva máquina virtual en modo Typical](images/a03-asistente-typical.jpg)


**4.** El asistente pregunta cómo queremos instalar el sistema operativo: desde un disco, desde una imagen ISO ya descargada o más tarde. En este caso usaremos una **imagen ISO**.

![Selección del método de instalación del sistema operativo](images/a04-instalacion-so.jpg)


**5.** Para seleccionar la imagen ISO, hacemos clic en **Browse**.

![Botón Browse para buscar la ISO](images/a05-boton-browse.jpg)

![Explorador con la ISO de Windows Server seleccionada](images/a05-seleccionar-iso.jpg)


**6.** Seleccionamos la ISO de Windows Server previamente descargada y hacemos clic en **Abrir**. En este caso instalaremos la versión **2022**.

![Windows Server 2022 detectado por el asistente](images/a06-so-detectado.jpg)


**7.** El asistente detecta la versión automáticamente y activa **Easy Install**. Hacemos clic en **Next**.

![Pantalla Easy Install Information](images/a07-easy-install.jpg)


> [!NOTE]
> Este cuadro corresponde a Easy Install. El método que utilizaremos no usa esta función, así que dejamos los campos vacíos y hacemos clic en **Next**.

**8.** Aparece un aviso porque no ingresamos la clave de producto. Hacemos clic en **Yes** y continuamos.

![Aviso por no ingresar la clave de producto](images/a08-sin-clave.jpg)


**9.** Asignamos el nombre a la máquina virtual (en este caso, `Neury0829`) y hacemos clic en **Next**.

![Nombre de la máquina virtual](images/a09-nombre-vm.jpg)


**10.** Configuramos el tamaño del disco y cómo se almacenarán los archivos de Windows Server 2022. Dejamos los valores por defecto (60 GB, *Split virtual disk into multiple files*).

![Capacidad del disco y archivos múltiples](images/a10-capacidad-disco.jpg)


> [!TIP]
> **¿Un solo archivo o varios?** Si guardamos el disco virtual en un único archivo y este se corrompe, se pierde toda la máquina virtual. Si se divide en múltiples archivos, la corrupción de uno solo afecta únicamente a ese archivo y no a la integridad total de la máquina.

**11.** En el resumen podemos revisar la configuración. Si queremos asignar más o menos hardware, usamos **Customize Hardware**. Luego hacemos clic en **Finish** y la máquina virtual aparece lista para ejecutarse.

![Resumen de la VM con el botón Customize Hardware](images/a11-resumen-vm.jpg)

![Máquina virtual lista para ejecutar](images/a11-vm-lista.jpg)


### Quitar el dispositivo Floppy (Easy Install)

**12.** Easy Install agrega un dispositivo de tipo **Floppy** que debemos eliminar para evitar errores durante la instalación. Para esto, hacemos clic en **Edit virtual machine settings**.

![Configuración de la VM con el dispositivo Floppy](images/a12-editar-configuracion.jpg)


**13.** Seleccionamos **Floppy**, hacemos clic en **Remove** y luego en **OK**.

![Botón Remove sobre el dispositivo Floppy](images/a13-quitar-floppy.jpg)


### Instalar el sistema operativo

**14.** Con la configuración lista, encendemos la máquina con **Power on this virtual machine**.

![Botón Power on this virtual machine](images/a14-encender-vm.jpg)


**15.** Al encender, aparece el mensaje *Press any key to boot from CD or DVD*. Presionamos **Enter** lo más rápido posible. Luego esperamos a que pase la pantalla negra de Windows.

![Mensaje Press any key to boot from CD or DVD](images/a15-presionar-tecla.png)


> [!WARNING]
> Si no presionamos una tecla a tiempo, la máquina no arranca desde la ISO. En ese caso, reiniciamos la máquina virtual y lo intentamos de nuevo.

**16.** Seleccionamos el idioma de instalación y hacemos clic en **Siguiente**.

![Selección del idioma de instalación](images/a16-idioma.jpg)


**17.** Hacemos clic en **Instalar ahora** y esperamos a que aparezca la siguiente ventana.

![Botón Instalar ahora](images/a17-instalar-ahora.jpg)


**18.** Elegimos la edición del sistema operativo. Hay 4 opciones, según la necesidad. Aquí usaremos la **segunda**, *Windows Server 2022 Standard Evaluation (experiencia de escritorio)*, que es la versión Standard con interfaz gráfica. La **cuarta** (Datacenter con experiencia de escritorio) también es válida.

![Selección de la edición de Windows Server 2022](images/a18-seleccion-edicion.jpg)


> [!WARNING]
> La **primera** y la **tercera** opción (las que no dicen "experiencia de escritorio") son instalaciones **Server Core**: no tienen interfaz gráfica, solo línea de comandos y PowerShell.

**19.** Aceptamos los términos y condiciones y hacemos clic en **Siguiente**.

**20.** Elegimos **Personalizar: instalar solo el sistema operativo de servidor de Microsoft (avanzado)**, seleccionamos la partición configurada previamente (espacio sin asignar de 60 GB) y hacemos clic en **Siguiente**.

![Tipo de instalación: Personalizar](images/a20-tipo-instalacion.jpg)

![Selección de la partición donde se instalará](images/a20-particion.jpg)


**21.** Comienza la instalación. Esperamos a que termine.

![Progreso de la instalación](images/a21-instalando.jpg)


**22.** Escribimos la contraseña de nuestra preferencia para la cuenta **Administrador** y hacemos clic en **Finalizar**.

![Contraseña del administrador](images/a22-contrasena-admin.jpg)


**23.** En la pantalla de bloqueo usamos la combinación de teclas de VMware para iniciar sesión:

```text
Ctrl derecho + Alt derecho + Insert
```

![Pantalla de bloqueo del servidor](images/a23-pantalla-bloqueo.jpg)


**24.** Ingresamos la contraseña.

### Cambiar el nombre del equipo

**25.** Se abre automáticamente el **Administrador del servidor**. Lo último que nos queda en este punto es cambiar el nombre del equipo:

1. Clic en **Servidor local**.
2. Clic en el **nombre del equipo**.
3. Clic en **Cambiar**.
4. Asignamos el nombre (en este caso, `Neury0829`).

![Administrador del servidor](images/a25-administrador-servidor.jpg)

![Cambio del nombre del equipo](images/a25-cambiar-nombre.jpg)


> [!NOTE]
> Windows solicitará reiniciar el servidor para aplicar el nuevo nombre.

---

## Parte B: Activar Active Directory y crear el dominio

El objetivo es instalar los servicios de dominio de Active Directory y utilizar el mismo nombre del equipo para el dominio.

> [!WARNING]
> Antes de promover el servidor a controlador de dominio, Windows recomienda asignarle una **dirección IP estática**. En este laboratorio el servidor usa una IP asignada por DHCP (`192.168.0.10`), por lo que el asistente mostrará una advertencia al respecto. Para un entorno real, configura una IP fija antes de continuar.

### Instalar el rol

**1.** En el Administrador del servidor, hacemos clic en **Agregar roles y características**.

![Opción Agregar roles y características](images/b01-agregar-roles.jpg)


**2.** Se abre el asistente.

![Asistente para agregar roles y características](images/b02-asistente-inicio.jpg)


**3.** Hacemos clic en **Siguiente**.

**4.** Dejamos la opción por defecto, **Instalación basada en características o en roles**, y hacemos clic en **Siguiente**.

![Tipo de instalación basada en roles o características](images/b03-tipo-instalacion.jpg)


**5.** Dejamos seleccionado el servidor de destino y hacemos clic en **Siguiente**.

![Selección del servidor de destino](images/b05-servidor-destino.jpg)


**6.** Buscamos y marcamos **Servicios de dominio de Active Directory**.

![Lista de roles de servidor](images/b06-roles-servidor.jpg)


**7.** Aparece un cuadro que nos pide agregar las características requeridas. Hacemos clic en **Agregar características**. Con el rol marcado, hacemos clic en **Siguiente** hasta llegar a la pantalla de confirmación.

![Cuadro para agregar las características requeridas](images/b07-agregar-caracteristicas.jpg)


**8.** Marcamos **Reiniciar automáticamente el servidor de destino en caso necesario**, confirmamos con **Sí** y hacemos clic en **Instalar**.

![Confirmación de la instalación](images/b08-confirmar-instalacion.jpg)


**9.** Esperamos a que termine la instalación.

![Progreso de la instalación del rol](images/b09-progreso-instalacion.jpg)


**10.** Cuando finaliza, hacemos clic en **Cerrar** y continuamos con la configuración.

### Promover el servidor a controlador de dominio

**11.** Hacemos clic en el botón de **Notificaciones** del Administrador del servidor.

![Botón de notificaciones del Administrador del servidor](images/b11-notificaciones.jpg)


**12.** Hacemos clic en **Promover este servidor a controlador de dominio**.

![Promover este servidor a controlador de dominio](images/b12-promover-dc.jpg)


**13.** Seleccionamos **Agregar un nuevo bosque** y especificamos el nombre de dominio raíz (en este caso, `Neury0829.local`). Hacemos clic en **Siguiente**.

![Configuración de implementación: nuevo bosque](images/b13-nuevo-bosque.jpg)


**14.** Establecemos la contraseña DSRM (modo de restauración de servicios de directorio) y hacemos clic en **Siguiente**. Dejamos los niveles funcionales y las capacidades por defecto (DNS y Catálogo global marcados).

![Opciones del controlador de dominio](images/b14-opciones-dc.jpg)


**15.** La opción de crear delegación DNS la dejamos desmarcada, tal como viene por defecto. Hacemos clic en **Siguiente**.

**16.** Aparece automáticamente el nombre de dominio **NetBIOS**. Hacemos clic en **Siguiente** hasta llegar a la comprobación de requisitos previos.

![Nombre de dominio NetBIOS](images/b16-netbios.jpg)


**17.** Cuando todas las configuraciones se comprueban correctamente, hacemos clic en **Instalar**. Pueden aparecer advertencias (algoritmos de cifrado e IP estática); no impiden la instalación.

![Comprobación de requisitos previos](images/b17-requisitos-previos.jpg)


**18.** Esperamos. El servidor se reiniciará automáticamente.

### Verificar el dominio

**19.** Al iniciar, el inicio de sesión ya muestra el dominio (`NEURY08290\Administrador`), lo que confirma que la configuración se aplicó correctamente.

![Inicio de sesión con el usuario del dominio](images/b19-inicio-sesion-dominio.jpg)


**20.** Al abrir el Administrador del servidor, en **Servidor local** vemos que el dominio fue agregado.

![Servidor local mostrando el dominio](images/b20-servidor-local-dominio.jpg)


---

## Parte C: Unir un cliente Windows 10 al dominio

Se puede usar Windows 8 o superior; en este caso se utiliza **Windows 10**. Con la máquina cliente ya instalada y encendida:

**1.** Presionamos la tecla de Windows y buscamos el **Panel de control**.

**2.** Hacemos clic en **Redes e Internet**.

![Panel de control: Redes e Internet](images/c02-panel-control.jpg)


**3.** Hacemos clic en **Centro de redes y recursos compartidos**.

![Redes e Internet: Centro de redes y recursos compartidos](images/c03-redes-internet.jpg)


**4.** Hacemos clic en **Cambiar configuración del adaptador**.

![Centro de redes: Cambiar configuración del adaptador](images/c04-centro-redes.jpg)


**5.** En el adaptador (`Ethernet0`), hacemos clic derecho y luego en **Propiedades**.

![Adaptador Ethernet0](images/c05-ethernet0.png)


**6.** Hacemos doble clic en **Protocolo de Internet versión 4 (TCP/IPv4)**.

![Propiedades de Ethernet0](images/c06-propiedades-ethernet.jpg)


### Configurar el DNS

**7.** Marcamos **Usar las siguientes direcciones de servidor DNS**. En **DNS preferido** colocamos la IP del servidor (`192.168.0.10`). Para ver la IP del servidor, abrimos el símbolo del sistema en él y ejecutamos:

```cmd
ipconfig
```

![Propiedades del protocolo IPv4 con los DNS](images/c07-ipv4-dns.jpg)


> [!TIP]
> En este laboratorio se agregó `8.8.8.8` como DNS alternativo. En un cliente unido a un dominio es buena práctica usar **solo el DNS del controlador de dominio** y dejar que este reenvíe las consultas externas. Un DNS público como secundario puede provocar fallos intermitentes al resolver el dominio.

**8.** Hacemos clic en **Aceptar**.

### Unir el equipo al dominio

**9.** Abrimos el Explorador de archivos, vamos a **Este equipo**, hacemos clic derecho en un espacio en blanco y seleccionamos **Propiedades**.

![Este equipo: Propiedades](images/c09-este-equipo.jpg)


**10.** Hacemos clic en **Cambiar configuración**.

![Propiedades del sistema: Cambiar configuración](images/c10-propiedades-sistema.jpg)


**11.** En la pestaña **Nombre de equipo**, hacemos clic en **Cambiar**.

![Pestaña Nombre de equipo: botón Cambiar](images/c11-nombre-equipo.jpg)


**12.** Establecemos el nombre del equipo (en este caso, `WS0829`) y, en **Miembro del**, seleccionamos **Dominio** e indicamos `Neury0829.local`.

**13.** Hacemos clic en **Aceptar**.

![Cambios en el dominio o el nombre del equipo](images/c12-cambios-dominio.jpg)


**14.** Se pedirá un usuario y contraseña con permisos para unirse al dominio (por ejemplo, el **Administrador** del dominio).

**15.** Hacemos clic en **Aceptar**.

![Credenciales con permiso para unirse al dominio](images/c14-credenciales.jpg)


**16.** Aparece el mensaje de que el equipo se unió correctamente al dominio. Nos pedirá reiniciar el equipo y procedemos.

![Mensaje de unión correcta al dominio](images/c16-union-exitosa.jpg)


**17.** Con el cliente reiniciado, verificamos que formamos parte del dominio: en **Otro usuario** debe aparecer la opción de iniciar sesión en el dominio.

![Pantalla de inicio de sesión en el dominio](images/c17-inicio-sesion-dominio.jpg)


---

## Verificación final

Comandos opcionales para confirmar que todo funciona.

**En el servidor** (PowerShell como administrador):

```powershell
Get-ADDomain | Select-Object DNSRoot, NetBIOSName, DomainMode
Get-ADComputer -Filter * | Select-Object Name, DNSHostName
```

**En el cliente** (PowerShell):

```powershell
Get-CimInstance Win32_ComputerSystem | Select-Object Name, Domain, PartOfDomain
nslookup Neury0829.local
```

Resultado esperado: `PartOfDomain` en `True`, el dominio `Neury0829.local` y el equipo `WS0829` listado en Active Directory.
## Demostración en video

Esta práctica cuenta con demostraciones en video donde se muestra el proceso completo en el laboratorio. Las capturas del procedimiento se encuentran en la carpeta `images/`.

| Parte del How-To | Laboratorio | Video |
|:--:|---|---|
| A | Lab A: Instalación de Windows Server 2022 | [Ver en YouTube](https://www.youtube.com/watch?v=4aqgDtxuHpw&list=PLIJEt4hLYnOWqOb7Y7uE-x2Y58xE4Wx_M&index=3) |
| B | Lab B: Instalación y configuración de Active Directory | [Ver en YouTube](https://www.youtube.com/watch?v=Zir3aKPCbaA&list=PLIJEt4hLYnOWqOb7Y7uE-x2Y58xE4Wx_M&index=2) |
| C | Lab C: Enrolamiento a dominio de AD de VM Windows 10 Pro | [Ver en YouTube](https://www.youtube.com/watch?v=K6fxlcUOUbM&list=PLIJEt4hLYnOWqOb7Y7uE-x2Y58xE4Wx_M) |

---
## Lo aprendido

Esta práctica permitió trabajar los fundamentos necesarios para construir una infraestructura basada en Active Directory: instalación de Windows Server 2022 en un hipervisor, creación de un dominio y unión de un cliente Windows 10 al dominio.

Durante el proceso se identificaron puntos críticos que conviene cuidar: eliminar el dispositivo Floppy de Easy Install para evitar errores de instalación, presionar una tecla a tiempo para arrancar desde la ISO, y configurar el DNS del cliente con la IP del controlador de dominio para que pueda resolver y unirse al dominio. También se comprendió la diferencia entre almacenar el disco virtual en un solo archivo o en varios, y por qué se recomienda asignar una IP estática a un controlador de dominio.

---

## Solución de problemas frecuentes

| Problema | Causa probable | Solución |
|---|---|---|
| La VM no arranca desde la ISO | No se presionó una tecla a tiempo | Reiniciar la VM y presionar **Enter** al ver *Press any key to boot...* |
| Errores durante la instalación | Dispositivo Floppy de Easy Install presente | Quitar el Floppy desde la configuración de la VM (paso 12 y 13 de la Parte A) |
| El cliente no encuentra el dominio | DNS del cliente mal configurado o redes distintas | Verificar que el DNS preferido sea la IP del servidor y que ambas VM estén en la misma red |
| Pide credenciales y las rechaza | Usuario o contraseña incorrectos | Usar una cuenta con permiso para unir equipos, por ejemplo `NEURY08290\Administrador` |
| La IP del servidor cambió | IP asignada por DHCP | Configurar una IP estática en el servidor y actualizar el DNS del cliente |

---

> Documentación realizada como parte de mi formación práctica en Ciberseguridad. Entorno 100 % de laboratorio.


[← Volver al laboratorio](../README.md)
