[← Volver al laboratorio](../README.md)

# 06 · Evaluación de vulnerabilidades con Nessus

![Windows Server](https://img.shields.io/badge/Windows%20Server-2022-c084fc?style=flat-square&logo=windows&logoColor=white)
![Nessus](https://img.shields.io/badge/Tenable-Nessus%20Essentials-f472b6?style=flat-square)
![Vulnerabilidades](https://img.shields.io/badge/Vulnerabilidades-Escaneo%20y%20remediaci%C3%B3n-60a5fa?style=flat-square)
![Estado](https://img.shields.io/badge/Estado-Completado-34d399?style=flat-square)

Sexta práctica del laboratorio. Se instala **Nessus**, se crean condiciones de vulnerabilidad en el servidor, se escanea el controlador de dominio, se aplican las soluciones sugeridas y se ejecuta un segundo escaneo para comparar los resultados.

## Objetivos

| Punto | Descripción |
|:--:|---|
| **A** | Explicar qué es Nessus e implementarlo para el escaneo de vulnerabilidades |
| **B** | Crear vulnerabilidades de seguridad en el servidor (mínimo 3 de severidad Medium o superior) |
| **C** | Escanear el servidor controlador de dominio de Active Directory |
| **D y E** | Analizar los resultados y aplicar las soluciones sugeridas por el CVE |
| **F** | Realizar un nuevo escaneo, comparar los reportes y concluir sobre los resultados y la herramienta |

## Entorno

| Elemento | Valor |
|---|---|
| Hipervisor | VMware Workstation Pro 17 |
| Servidor (controlador de dominio) | Windows Server 2022, `Neury0829` |
| IP del servidor en esta práctica | `192.168.0.9` (asignada por DHCP) |
| Dominio | `Neury0829.local` |
| Herramienta | Tenable Nessus Essentials |
| Plantilla de escaneo | Basic Network Scan, con credenciales de Windows |

> [!WARNING]
> Esta práctica **desactiva deliberadamente** protecciones de seguridad para generar hallazgos. Hazlo únicamente en un entorno de laboratorio aislado, y **escanea solo equipos propios o con autorización**. Al terminar, reactiva Windows Defender y el firewall (ver [Restaurar la configuración de seguridad](#restaurar-la-configuración-de-seguridad)).

## Índice

- [Parte A: Qué es Nessus e instalación](#parte-a-qué-es-nessus-e-instalación)
- [Parte B: Crear vulnerabilidades](#parte-b-crear-vulnerabilidades)
- [Parte C: Primer escaneo](#parte-c-primer-escaneo-del-controlador-de-dominio)
- [Partes D y E: Analizar y remediar](#partes-d-y-e-analizar-los-resultados-y-aplicar-soluciones)
- [Parte F: Segundo escaneo y comparación](#parte-f-segundo-escaneo-y-comparación)
- [Restaurar la configuración de seguridad](#restaurar-la-configuración-de-seguridad)
- [Lo aprendido](#lo-aprendido)

---

## Parte A: Qué es Nessus e instalación

**Nessus**, de Tenable, es un escáner de vulnerabilidades. Analiza equipos de la red en busca de actualizaciones de seguridad faltantes, configuraciones inseguras y otros fallos conocidos. Funciona con *plugins* (comprobaciones) que se actualizan constantemente, y clasifica cada hallazgo por severidad (**Critical, High, Medium, Low e Info**), con referencias a los identificadores **CVE** y una solución sugerida.

**1.** Buscamos "descargar Nessus" en el navegador y descargamos el ejecutable para nuestro sistema operativo.

![Página de descarga de Tenable Nessus](images/a01-descarga-nessus.jpg)

![Instalador de Nessus descargado](images/a01-instalador.jpg)


**2.** Ejecutamos el instalador, creamos una cuenta y se nos asigna una licencia para utilizarlo (en este caso, **Nessus Essentials**).

![Nessus Essentials en ejecución](images/a02-nessus-essentials.jpg)


> [!IMPORTANT]
> Hay que esperar a que terminen de instalarse los *plugins*. Mientras no finalicen, Nessus no permite realizar escaneos.

> [!NOTE]
> En esta práctica Nessus se instaló en la máquina virtual del servidor y se accede a su interfaz web desde el navegador (`https://localhost:8834`). La versión Essentials está limitada a un número reducido de direcciones IP por escaneo.

---

## Parte B: Crear vulnerabilidades

Para generar hallazgos de severidad Medium o superior, desactivamos funcionalidades de seguridad del servidor.

**1.** Buscamos "defender" en el menú de inicio y abrimos **Protección antivirus y contra amenazas**.

![Búsqueda de Windows Defender](images/b01-buscar-defender.png)

![Protección antivirus y contra amenazas](images/b01-proteccion-antivirus.jpg)


**2.** Hacemos clic en **Configuración de antivirus y protección contra amenazas** y luego en **Administrar la configuración**.

![Configuración de antivirus y protección contra amenazas](images/b02-configuracion-antivirus.jpg)

![Administrar la configuración](images/b02-administrar-configuracion.jpg)


**3.** Por defecto y por seguridad, las opciones vienen activadas. Las **desactivamos**:

- Protección en tiempo real
- Protección basada en la nube
- Envío de muestras automático
- Protección contra alteraciones

![Protección en tiempo real desactivada](images/b03-tiempo-real-desactivado.jpg)

![Protección basada en la nube desactivada](images/b03-nube-desactivada.jpg)

![Envío de muestras automático desactivado](images/b03-muestras-desactivado.jpg)

![Protección contra alteraciones desactivada](images/b03-alteraciones-desactivado.jpg)


**4.** Hacemos lo mismo con el **Firewall y protección de red**, desactivándolo en las tres redes: dominio, privada y pública.

![Firewall y protección de red](images/b04-firewall-proteccion-red.jpg)

![Firewall de red de dominio desactivado](images/b04-red-dominio.jpg)

![Firewall de red privada desactivado](images/b04-red-privada.jpg)

![Firewall de red pública desactivado](images/b04-red-publica.jpg)


---

## Parte C: Primer escaneo del controlador de dominio

**1.** Cuando terminan de instalarse los *plugins*, se habilita la opción **New Scan**. Hacemos clic en ella.

![Nessus: My Scans y botón New Scan](images/c01-new-scan.png)


**2.** Seleccionamos la plantilla **Basic Network Scan**.

![Plantilla Basic Network Scan](images/c02-basic-network-scan.jpg)


**3.** Nos pide nombre, descripción y *targets*. En *Targets* colocamos la dirección IP de la máquina. Para obtenerla, abrimos el Símbolo del sistema y ejecutamos:

```cmd
ipconfig
```

![Configuración del escaneo](images/c03-configuracion-scan.jpg)

![Resultado de ipconfig](images/c03-ipconfig.jpg)

![Nombre y targets del escaneo](images/c03-nombre-targets.jpg)

![Pestaña Credentials](images/c03-credentials-tab.jpg)


**4.** Pasamos a **Credentials → Windows**. Dejamos el método de autenticación por **contraseña** y colocamos el usuario, la contraseña y el dominio del servidor (en este caso, `Neury0829.local`). Guardamos.

![Credenciales de Windows para el escaneo](images/c04-credenciales-windows.jpg)


Para comprobar el nombre del dominio, vamos al Administrador del servidor y luego a **Servidor local**.

![Dominio en Servidor local](images/c04-verificar-dominio.jpg)


> [!TIP]
> Un escaneo con credenciales (*authenticated scan*) obtiene resultados más completos que uno sin credenciales. En un entorno real conviene usar una cuenta dedicada con los permisos mínimos necesarios, no la cuenta de administrador.

**5.** Después de guardar, lanzamos el escaneo con **Launch**.

![Lanzar el escaneo](images/c05-lanzar-escaneo.png)

![Escaneo en curso](images/c05-escaneo-en-curso.jpg)


---

## Partes D y E: Analizar los resultados y aplicar soluciones

**1.** Cuando el escaneo finaliza, lo abrimos haciendo clic en su nombre.

![Escaneo finalizado](images/d01-escaneo-finalizado.jpg)


**2.** Se muestra un resumen por barra y por gráfico. Para ver más detalle, hacemos clic en el host.

![Resultado por host](images/d02-resultado-host.jpg)

![Lista de vulnerabilidades del host](images/d02-lista-vulnerabilidades.jpg)


Al abrir una vulnerabilidad se muestra su ID, la descripción, la severidad, la solución sugerida y las referencias, entre ellas el **CVE**, que identifica públicamente la vulnerabilidad y permite llegar hasta su solución.

![Detalle de una vulnerabilidad](images/d02-detalle-vulnerabilidad.jpg)


**3.** Para aplicar las soluciones sugeridas, vamos a **Configuración → Actualización y seguridad**.

![Configuración de Windows: Actualización y seguridad](images/d03-configuracion-windows.jpg)


**4.** Hacemos clic en **Instalar ahora**.

![Windows Update: Instalar ahora](images/d04-instalar-ahora.jpg)


**5.** Se muestra la instalación de las actualizaciones y se solicita reiniciar.

![Windows Update: Reiniciar ahora](images/d05-reiniciar.jpg)


**6.** Reiniciamos el servidor.

---

## Parte F: Segundo escaneo y comparación

**1.** Realizamos otro escaneo, ya con las soluciones aplicadas.

![Segundo escaneo](images/f01-segundo-escaneo.jpg)


**2.** Abrimos los resultados de ambos escaneos.

![Lista con los dos escaneos](images/f02-escaneos-comparar.jpg)


**3.** Comparamos los resultados.

| Severidad | Primer escaneo (*Segundo Parcial*) | Segundo escaneo (*Segundo escaneo*) |
|---|:--:|:--:|
| **Critical** | 21 | 0 |
| **High** | 42 | 3 |
| **Medium** | 8 | 4 |
| Vulnerabilidades (total, incluye informativas) | 66 | 60 |
| Remediaciones sugeridas | 8 | 1 |

**Critical:** de 21 pasamos a ninguno.

![Primer escaneo: Critical 21](images/f03-primero-critical-21.jpg)


**High:** de 42 pasamos a 3.

![Primer escaneo: High 42](images/f03-primero-high-42.jpg)

![Segundo escaneo: High 3](images/f03-segundo-high-3.jpg)


**Medium:** de 8 pasamos a 4.

![Primer escaneo: Medium 8](images/f03-primero-medium-8.jpg)

![Segundo escaneo: Medium 4](images/f03-segundo-medium-4.jpg)


### Conclusión

Tras aplicar las soluciones sugeridas por Nessus y reiniciar el servidor, las vulnerabilidades **Critical** pasaron de 21 a 0 (−100 %), las **High** de 42 a 3 (−93 %) y las **Medium** de 8 a 4 (−50 %). Las remediaciones sugeridas bajaron de 8 a 1. Esto confirma que la mayor parte de los hallazgos más graves provenían de actualizaciones de seguridad pendientes, y que mantener el sistema parcheado reduce de forma drástica la superficie de ataque.

Sobre la herramienta: Nessus automatiza la detección de vulnerabilidades, prioriza por severidad, relaciona cada hallazgo con su CVE y sugiere la solución, lo que facilita planificar la remediación y **verificarla** con un nuevo escaneo. Como limitaciones, depende de que los *plugins* estén actualizados, puede generar falsos positivos que hay que validar, la versión Essentials limita el alcance, y un escaneo solo **detecta**: la corrección debe aplicarse y comprobarse después. Los hallazgos restantes (3 High y 4 Medium) deben revisarse y tratarse en un siguiente ciclo.

---

## Restaurar la configuración de seguridad

Al finalizar la práctica, vuelve a dejar el servidor protegido:

1. Activar la **protección en tiempo real**, la **protección basada en la nube**, el **envío de muestras automático** y la **protección contra alteraciones** de Windows Defender.
2. Activar el **firewall** en las redes de dominio, privada y pública.
3. Volver a ejecutar un escaneo para comprobar el estado final.

---

<!-- Demostración en video: agregar aquí el enlace de YouTube de la práctica 06 cuando esté disponible -->

## Lo aprendido

Esta práctica permitió trabajar el ciclo completo de gestión de vulnerabilidades: detectar con un escáner, interpretar los hallazgos por severidad y CVE, remediar mediante actualizaciones y verificar el resultado con un nuevo escaneo.

Se comprendió el impacto de las protecciones desactivadas y de las actualizaciones pendientes sobre la postura de seguridad de un servidor, y la diferencia entre un escaneo con y sin credenciales. La comparación de ambos reportes mostró una reducción de 21 a 0 vulnerabilidades críticas y de 42 a 3 altas tras parchear el sistema. También se reforzó que un escáner solo detecta: la remediación y la verificación posterior son lo que cierra el ciclo.

---

[← Volver al laboratorio](../README.md)
