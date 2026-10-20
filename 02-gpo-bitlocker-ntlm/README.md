[← Volver al laboratorio](../README.md)

# 02 · GPO: BitLocker y bloqueo de NTLM

![Windows Server](https://img.shields.io/badge/Windows%20Server-2022-c084fc?style=flat-square&logo=windows&logoColor=white)
![BitLocker](https://img.shields.io/badge/BitLocker-GPO-f472b6?style=flat-square)
![NTLM](https://img.shields.io/badge/NTLM-Restricci%C3%B3n-60a5fa?style=flat-square)
![Estado](https://img.shields.io/badge/Estado-Completado-34d399?style=flat-square)

Segunda práctica del laboratorio. Sobre el dominio creado en la [práctica 01](../01-active-directory), se usan **directivas de grupo (GPO)** para desplegar el cifrado con **BitLocker** (con desbloqueo por PIN y copia de las claves de recuperación en Active Directory) y para **restringir el protocolo NTLM** en todo el dominio.

## Objetivos

| Punto | Descripción |
|:--:|---|
| **A** | Crear una GPO para desplegar BitLocker y habilitar el cifrado de disco. Configurar el desbloqueo por PIN y habilitar que las claves de recuperación de los equipos cifrados se guarden en Active Directory |
| **B** | Implementar una GPO que bloquee NTLM y desplegarla a los equipos del dominio |

> [!NOTE]
> **Alcance de la demostración.** La GPO define las políticas de inicio con PIN, TPM y recuperación en Active Directory. La demostración práctica del cifrado se realizó sobre una **partición nueva (`E:`)** creada en el servidor, con desbloqueo por contraseña.

## Entorno

| Elemento | Valor |
|---|---|
| Hipervisor | VMware Workstation Pro 17 |
| Servidor (controlador de dominio) | Windows Server 2022, `Neury0829` |
| Dominio | `Neury0829.local` |
| GPO creadas | `GPO BitLocker` y `GPO NTLM`, ambas vinculadas al dominio |

## Requisitos previos

- Práctica 01 completada: dominio de Active Directory funcionando.
- Sesión iniciada con un usuario administrador del dominio.

## Índice

- [Parte A: BitLocker mediante GPO](#parte-a-bitlocker-mediante-gpo)
- [Parte B: Bloqueo de NTLM mediante GPO](#parte-b-bloqueo-de-ntlm-mediante-gpo)
- [Verificación final](#verificación-final)
- [Solución de problemas](#solución-de-problemas-frecuentes)

---

## Parte A: BitLocker mediante GPO

Antes de iniciar, nos encontramos en el Administrador del servidor.

![Administrador del servidor](images/a01-administrador-servidor.jpg)


### Instalar la característica BitLocker

**1.** Hacemos clic en **Administrar** y luego en **Agregar roles y características**.

![Administrar: Agregar roles y características](images/a01-agregar-roles.jpg)


**2.** Se abre el asistente. Hacemos clic en **Siguiente** hasta llegar a la pantalla de **características** y seleccionamos **Cifrado de unidad BitLocker**.

![Característica Cifrado de unidad BitLocker](images/a02-seleccionar-bitlocker.jpg)


**3.** Aparece un cuadro con las características requeridas (Enhanced Storage y las herramientas de administración de BitLocker, entre ellas el visor de contraseñas de recuperación). Hacemos clic en **Agregar características**.

![Características requeridas para BitLocker](images/a03-agregar-caracteristicas.jpg)


**4.** Hacemos clic en **Siguiente** hasta llegar a la confirmación.

![Confirmación de la instalación](images/a04-confirmar-instalacion.jpg)


**5.** Marcamos **Reiniciar automáticamente el servidor de destino en caso necesario**, confirmamos con **Sí** y hacemos clic en **Instalar**.

**6.** Esperamos a que finalice la instalación y el reinicio.

### Crear la GPO

**7.** Con la instalación completada, vamos a **Herramientas** y seleccionamos **Administración de directivas de grupo**.

![Herramientas: Administración de directivas de grupo](images/a07-administracion-gpo.jpg)


**8.** Hacemos clic derecho sobre nuestro dominio y seleccionamos **Crear un GPO en este dominio y vincularlo aquí**.

![Crear un GPO en este dominio y vincularlo aquí](images/a08-crear-gpo.jpg)


**9.** Le asignamos un nombre; en este caso, `GPO BitLocker`.

![Nombre del GPO: GPO BitLocker](images/a09-nombre-gpo-bitlocker.jpg)


**10.** Hacemos clic en **Aceptar**.

**11.** Desplegamos el dominio para ver la GPO creada.

![Dominio desplegado en la consola de directivas de grupo](images/a11-desplegar-dominio.jpg)


**12.** Hacemos clic derecho sobre la GPO y seleccionamos **Editar**.

![Editar la GPO BitLocker](images/a12-editar-gpo.jpg)


### Configurar las directivas de BitLocker

**13.** En **Configuración del equipo**, desplegamos **Directivas → Plantillas administrativas → Componentes de Windows** y seleccionamos **Cifrado de unidad BitLocker**.

![Componentes de Windows: Cifrado de unidad BitLocker](images/a13-componentes-bitlocker.jpg)


**14.** Hacemos clic en **Unidades del sistema operativo**.

![Unidades del sistema operativo](images/a14-unidades-so.jpg)


**15.** Configuraremos las directivas siguientes, empezando por **Requerir autenticación adicional al iniciar**.

![Directivas de las unidades del sistema operativo](images/a15-directivas-so.jpg)


**16.** Hacemos doble clic en la directiva, la **habilitamos**, hacemos clic en **Aplicar** y luego en **Aceptar**.

![Requerir autenticación adicional al iniciar: Habilitada](images/a16-autenticacion-adicional.jpg)


Las opciones de inicio quedan así (permiten el desbloqueo por PIN):

| Opción | Valor |
|---|---|
| Configurar inicio del TPM | Permitir TPM |
| Configurar PIN de inicio del TPM | Permitir PIN de inicio con TPM |
| Configurar clave de inicio del TPM | Permitir clave de inicio con TPM |
| Configurar la clave de inicio y el PIN del TPM | Permitir clave y PIN de inicio con TPM |

> [!NOTE]
> La casilla **Permitir BitLocker sin un TPM compatible** es necesaria en equipos que no tienen TPM, como muchas máquinas virtuales. En ese caso, BitLocker requiere contraseña o clave de inicio en una unidad USB.

**17.** Repetimos el mismo procedimiento con las directivas **Permitir los PIN mejorados para el inicio** y **Configurar longitud mínima de PIN para el inicio**. En esta última establecemos el mínimo de caracteres deseado; en este caso, **4**.

![Longitud mínima de PIN para el inicio: 4](images/a17-longitud-minima-pin.jpg)


> [!TIP]
> La propia ayuda de la directiva indica que, con una longitud mínima menor a 6 dígitos, Windows intenta ampliar el periodo de bloqueo del TPM 2.0. En un entorno real conviene usar un PIN más largo.

**18.** De la misma forma, habilitamos las directivas de **perfil de validación de plataforma del TPM** (resaltadas en la captura).

![Perfiles de validación de plataforma del TPM](images/a18-perfil-validacion-tpm.jpg)


**19.** Hacemos clic en **Retroceder** para volver al nivel de **Cifrado de unidad BitLocker** y abrimos con doble clic la directiva **Almacenar información de recuperación de BitLocker en los Servicios de dominio de Active Directory**.

![Botón Retroceder](images/a19-boton-retroceder.jpg)

![Directiva de recuperación de BitLocker en Active Directory](images/a19-recuperacion-active-directory.jpg)


**20.** La **habilitamos** y aplicamos los cambios. Con esto, las claves de recuperación de los equipos cifrados se guardan en Active Directory.

> [!TIP]
> Para equipos con Windows 7 o superior, la copia de seguridad en Active Directory también se controla desde las directivas **Elegir cómo se pueden recuperar...** de cada tipo de unidad (sistema operativo, datos fijos y extraíbles), donde se puede marcar *guardar información de recuperación en AD DS* y exigirla antes de activar BitLocker.

### Aplicar la GPO

**21.** Para aplicar los cambios de manera efectiva, abrimos el **Símbolo del sistema (CMD)** como administrador y ejecutamos:

```cmd
gpupdate /force
```

> [!NOTE]
> Este comando fuerza la aplicación inmediata de las GPO, sin esperar al ciclo normal de actualización.

![Ejecución de gpupdate /force](images/a21-gpupdate-force.jpg)


**22.** Presionamos **Enter** y esperamos a que se apliquen las directivas de equipo y de usuario.

![Resultado de gpupdate /force](images/a22-gpupdate-resultado.jpg)


### Cifrar una unidad de prueba

**23.** Para cifrar una unidad, abrimos el Explorador de archivos y creamos una carpeta donde guardaremos la clave de recuperación; en este caso, `Key recovery`.

![Carpeta Key recovery en el escritorio](images/a23-carpeta-key-recovery.png)


> [!WARNING]
> Esta carpeta es solo para la demostración. En un entorno real, la clave de recuperación no se guarda en el mismo equipo cifrado. Active Directory (paso 20) es el repositorio adecuado. **Nunca subas a GitHub una clave de recuperación real.**

**24.** En **Administración de discos**, hacemos clic derecho sobre el disco `C:`, seleccionamos **Reducir volumen** y establecemos el tamaño de la nueva partición.

**25.** En el espacio sin asignar resultante, hacemos clic derecho y seleccionamos **Nuevo volumen simple**.

![Administración de discos: Nuevo volumen simple](images/a25-nuevo-volumen-simple.jpg)


**26.** Seguimos los pasos del asistente.

**27.** Con la partición creada (`E:`), procedemos a cifrarla.

**28.** Hacemos clic derecho sobre la unidad y seleccionamos **Activar BitLocker**.

![Activar BitLocker sobre la unidad](images/a28-activar-bitlocker.jpg)


**29.** Elegimos cómo desbloquear la unidad; en este caso, con **contraseña**. La escribimos, la confirmamos y hacemos clic en **Siguiente**.

![Desbloqueo de la unidad con contraseña](images/a29-desbloqueo-contrasena.png)


**30.** El asistente pregunta dónde guardar la copia de seguridad de la clave de recuperación. Seleccionamos **Guardar en un archivo** y elegimos la carpeta `Key recovery`.

![Copia de seguridad de la clave de recuperación](images/a30-copia-clave-recuperacion.png)

![Guardar la clave en la carpeta Key recovery](images/a30-guardar-clave.jpg)


> [!NOTE]
> El mensaje *"El administrador del sistema administra ciertas configuraciones"* confirma que la GPO ya se está aplicando en este equipo.

**31.** Hacemos clic en **Guardar** y luego en **Siguiente**.

**32.** Elegimos cuánto cifrar. Para este caso, seleccionamos **Cifrar la unidad entera**.

![Cifrar la unidad entera](images/a32-cifrar-unidad-entera.jpg)


**33.** Elegimos el modo de cifrado. Para este caso, seleccionamos **Modo Compatible** y hacemos clic en **Siguiente**.

![Elección del modo de cifrado](images/a33-modo-cifrado.jpg)


**34.** En la pantalla de confirmación, hacemos clic en **Iniciar cifrado**.

![Botón Iniciar cifrado](images/a34-iniciar-cifrado.jpg)


**35.** Esperamos. Al terminar, la unidad aparece cifrada en el Explorador de archivos.

![Unidad cifrada correctamente](images/a35-cifrado-completado.jpg)


---

## Parte B: Bloqueo de NTLM mediante GPO

**1.** En el Administrador del servidor, hacemos clic en **Herramientas**.

![Menú Herramientas del Administrador del servidor](images/b01-herramientas.jpg)


**2.** Seleccionamos **Administración de directivas de grupo**.

![GPO existentes en el dominio](images/b02-gpo-existentes.jpg)


**3.** Hacemos clic derecho sobre el dominio y seleccionamos **Crear un GPO en este dominio y vincularlo aquí**.

![Crear un GPO en este dominio y vincularlo aquí](images/b03-crear-gpo-ntlm.jpg)


**4.** Le asignamos el nombre que deseemos y hacemos clic en **Aceptar**.

![Nombre del GPO para NTLM](images/b04-nombre-gpo-ntlm.png)


> [!NOTE]
> En las capturas el nombre aparece como `GPO NTML` por un error tipográfico. El nombre correcto del protocolo es **NTLM**.

**5.** Hacemos clic derecho sobre la GPO y seleccionamos **Editar**.

**6.** En **Configuración del equipo**, desplegamos **Directivas → Configuración de Windows → Configuración de seguridad → Directivas locales → Opciones de seguridad**. Aquí buscamos la directiva **Seguridad de red: restringir NTLM: auditar la autenticación NTLM en este dominio**.

![Opciones de seguridad: directivas de restricción de NTLM](images/b06-opciones-seguridad.jpg)


**7.** Hacemos doble clic en ella y marcamos **Definir esta configuración de directiva**.

![Propiedades de la directiva de auditoría de NTLM](images/b07-auditar-ntlm-propiedades.png)


**8.** El valor por defecto es **Deshabilitar**. Lo cambiamos por **Habilitar todo**.

![Auditoría de NTLM en Habilitar todo](images/b08-habilitar-todo.png)


**9.** Hacemos clic en **Aplicar** y luego en **Aceptar**.

**10.** Repetimos el procedimiento con la siguiente directiva: **Seguridad de red: restringir NTLM: autenticación NTLM en este dominio**.

![Directiva de autenticación NTLM en este dominio](images/b10-segunda-directiva.jpg)


**11.** Aquí, en vez de habilitar, **denegamos todo**: seleccionamos **Denegar todo** para evitar cualquier intento de autenticación NTLM en el dominio.

![Opción Denegar todo](images/b11-denegar-todo.png)


> [!TIP]
> Las dos directivas cumplen funciones distintas. La **primera audita**: registra en el Visor de eventos los intentos de autenticación NTLM. La **segunda bloquea** la autenticación. En un entorno real conviene auditar primero y revisar qué servicios o aplicaciones todavía usan NTLM antes de denegarlo. De lo contrario, pueden dejar de funcionar. Si es necesario, existen directivas para agregar excepciones de servidor.

**12.** Aplicamos los cambios, confirmamos con **Sí** en el aviso y hacemos clic en **Aceptar**.

**13.** Forzamos la aplicación de la directiva desde el CMD:

```cmd
gpupdate /force
```

![gpupdate /force tras configurar NTLM](images/b13-gpupdate-force.jpg)


**14.** De vuelta en la consola de directivas de grupo, hacemos clic derecho sobre la GPO de NTLM y seleccionamos **Actualizar** para refrescar la vista.

![GPO NTLM en la lista de objetos de directiva](images/b14-gpo-ntlm-lista.jpg)

![Opción Actualizar del menú contextual](images/b14-actualizar.jpg)


---

## Verificación final

Comandos opcionales para comprobar el resultado.

**GPO aplicadas** (CMD como administrador):

```cmd
gpresult /r /scope computer
```

Debe listar `GPO BitLocker` y la GPO de NTLM entre los objetos de directiva aplicados.

**Estado de BitLocker** (PowerShell como administrador):

```powershell
manage-bde -status E:
Get-BitLockerVolume
```

**Clave de recuperación en Active Directory:** en *Usuarios y equipos de Active Directory*, abre el objeto del equipo cifrado y revisa su pestaña de recuperación de BitLocker (disponible gracias al visor de contraseñas de recuperación instalado en el paso 3).

**Auditoría de NTLM:** en el Visor de eventos, revisa *Registros de aplicaciones y servicios → Microsoft → Windows → NTLM → Operational*.

## Solución de problemas frecuentes

| Problema | Causa probable | Solución |
|---|---|---|
| La GPO no se aplica | Aún no se actualizó la directiva | Ejecutar `gpupdate /force` y revisar con `gpresult /r` |
| BitLocker no permite activarse sin TPM | El equipo no tiene TPM | Habilitar **Permitir BitLocker sin un TPM compatible** en la directiva de autenticación adicional |
| Servicios o aplicaciones dejan de autenticarse | Todavía usaban NTLM | Revisar los eventos de auditoría y, si hace falta, agregar excepciones de servidor |
| Se perdió la contraseña de desbloqueo | — | Usar la clave de recuperación guardada en AD o en el archivo de respaldo |

---

[← Volver al laboratorio](../README.md)
