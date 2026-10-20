[← Volver al laboratorio](../README.md)

# 04 · DNSSEC

![Windows Server](https://img.shields.io/badge/Windows%20Server-2022-c084fc?style=flat-square&logo=windows&logoColor=white)
![DNSSEC](https://img.shields.io/badge/DNS-DNSSEC-f472b6?style=flat-square)
![GPO](https://img.shields.io/badge/GPO-NRPT-60a5fa?style=flat-square)
![Estado](https://img.shields.io/badge/Estado-Completado-34d399?style=flat-square)

Cuarta práctica del laboratorio. Sobre el dominio de la [práctica 01](../01-active-directory), se firma la zona DNS del dominio con **DNSSEC** (extensiones de seguridad de DNS, que agregan autenticación de origen e integridad a las respuestas DNS) y se propaga la configuración a los clientes mediante una **GPO**.

## Objetivos

| Punto | Descripción |
|:--:|---|
| **A** | Habilitar DNSSEC en una zona de búsqueda del dominio |
| **B** | Propagar la configuración de DNSSEC a los clientes mediante GPO |

## Entorno

| Elemento | Valor |
|---|---|
| Hipervisor | VMware Workstation Pro 17 |
| Servidor (controlador de dominio y DNS) | Windows Server 2022, `Neury0829` |
| Zona DNS | `Neury0829.local` (zona primaria integrada de Active Directory) |
| Unidad organizativa | `Empresas` (creada en la [práctica 03](../03-credential-guard-block-usb)) |
| GPO creada | `DNSSEC`, vinculada a la OU `Empresas` |

> [!NOTE]
> La GPO de esta práctica está vinculada a la unidad organizativa **Empresas**. Solo se aplica a los equipos que estén dentro de esa OU.

## Requisitos previos

- Práctica 01 completada: dominio con el rol de servidor DNS funcionando.
- Unidad organizativa `Empresas` creada (práctica 03).
- Sesión iniciada con un usuario administrador del dominio.

## Índice

- [Parte A: Firmar la zona DNS con DNSSEC](#parte-a-firmar-la-zona-dns-con-dnssec)
- [Parte B: Propagar DNSSEC a los clientes mediante GPO](#parte-b-propagar-dnssec-a-los-clientes-mediante-gpo)
- [Verificación final](#verificación-final)
- [Solución de problemas](#solución-de-problemas-frecuentes)

---

## Parte A: Firmar la zona DNS con DNSSEC

Antes de iniciar, nos encontramos en el Administrador del servidor.

![Administrador del servidor](images/a00-administrador-servidor.jpg)


### Abrir el Administrador de DNS

**1.** Hacemos clic en **Herramientas** y luego en **DNS**.

![Herramientas: DNS](images/a01-herramientas-dns.jpg)


**2.** Se abre el Administrador de DNS. Hacemos clic derecho sobre nuestro dominio (`Neury0829.local`) y buscamos la opción **DNSSEC** en el submenú. Es posible que aparezca en gris y no permita seleccionarla.

![Opción DNSSEC en gris en el menú de la zona](images/a02-dnssec-deshabilitado.jpg)


**3.** Para solucionarlo, hacemos clic en **Actualizar**.

![Opción Actualizar del menú de la zona](images/a03-actualizar.jpg)


**4.** Repetimos el procedimiento. Con la opción ya habilitada, hacemos clic en **DNSSEC** y luego en **Firmar la zona**.

![DNSSEC: Firmar la zona](images/a04-firmar-la-zona.jpg)


### Asistente para firmar la zona

**5.** Se abre el **Asistente para firmar zona**.

![Asistente para firmar zona](images/a05-asistente-firmar-zona.jpg)


**6.** Hacemos clic en **Siguiente** hasta llegar a la configuración de la **clave de firma de clave (KSK)**.

![Clave de firma de clave (KSK): botón Agregar](images/a06-ksk-agregar.jpg)


**7.** Hacemos clic en **Agregar** y se abre el cuadro de configuración de la KSK.

![Cuadro Nueva clave de firma de clave (KSK)](images/a07-nueva-ksk.jpg)


**8.** Lo único que cambiamos es la **frecuencia de sustitución (días)** de la sustitución automática de claves: pasamos de **755** (valor por defecto) a **100**. Hacemos clic en **Aceptar**.

![KSK: frecuencia de sustitución en 100 días](images/a08-ksk-sustitucion-100.jpg)


**9.** Hacemos clic en **Siguiente** hasta llegar a la configuración de la **clave de firma de zona (ZSK)**.

![Clave de firma de zona (ZSK): botón Agregar](images/a09-zsk-agregar.jpg)


**10.** Hacemos clic en **Agregar**.

![Cuadro Nueva clave de firma de zona (ZSK)](images/a10-nueva-zsk.png)


**11.** Aquí cambiamos dos valores:

| Opción | Valor por defecto | Valor configurado |
|---|:--:|:--:|
| Frecuencia de sustitución (días) | 90 | **100** |
| Longitud de clave (bits) | 1024 | **2048** |

![ZSK: longitud de clave de 2048 bits](images/a11-zsk-longitud-2048.jpg)


> [!NOTE]
> **KSK y ZSK.** La **KSK** (clave de firma de clave) firma el conjunto de claves DNSKEY de la zona; la **ZSK** (clave de firma de zona) firma los registros de la zona. En ambos casos se mantiene el algoritmo por defecto, **RSA/SHA-256**.

> [!TIP]
> En un entorno real suele rotarse la ZSK con más frecuencia que la KSK, ya que la KSK es la que se publica como ancla de confianza. En este laboratorio ambas se configuraron en 100 días.

**12.** Con las configuraciones aplicadas, hacemos clic en **Aceptar**.

**13.** Hacemos clic en **Siguiente** hasta aplicar la configuración de DNSSEC.

**14.** Cuando la firma de la zona se complete correctamente, hacemos clic en **Finalizar**.

![La zona se firmó correctamente](images/a14-zona-firmada.jpg)


### Comprobar la zona firmada

**15.** Hacemos clic derecho sobre el dominio y seleccionamos **Actualizar**. Veremos los registros creados por la firma de la zona.

![Actualizar la vista del dominio](images/a15-actualizar-dominio.jpg)

![Registros DNSSEC creados en la zona](images/a15-registros-dnssec.jpg)


> [!NOTE]
> Tras firmar la zona aparecen nuevos tipos de registro: **DNSKEY** (claves públicas), **RRSIG** (firmas de los registros) y registros **NSEC3** (denegación de existencia autenticada).

---

## Parte B: Propagar DNSSEC a los clientes mediante GPO

Una zona firmada solo protege a los clientes que están configurados para **exigir la validación**. Esto se logra con una regla en la **tabla de directivas de resolución de nombres (NRPT)**, que se distribuye mediante una GPO.

**1.** Hacemos clic en **Herramientas** y luego en **Administración de directivas de grupo**.

![Administración de directivas de grupo](images/b01-administracion-gpo.jpg)


**2.** Nos dirigimos a la unidad organizativa donde propagaremos la configuración a los clientes (`Empresas`).

**3.** Hacemos clic derecho sobre la unidad organizativa y seleccionamos **Crear un GPO en este dominio y vincularlo aquí...**.

![Crear un GPO en la unidad organizativa Empresas](images/b03-crear-gpo.jpg)


**4.** Establecemos el nombre (en este caso, `DNSSEC`) y hacemos clic en **Aceptar**.

![Nombre del GPO: DNSSEC](images/b04-nombre-gpo-dnssec.png)


**5.** Hacemos clic derecho sobre la GPO creada y seleccionamos **Editar**.

![Editar la GPO DNSSEC](images/b05-editar-gpo.jpg)


**6.** Se abre el editor de administración de directivas de grupo.

![Editor de administración de directivas de grupo](images/b06-editor-gpo.jpg)


**7.** En **Configuración del equipo**, desplegamos **Directivas → Configuración de Windows → Directiva de resolución de nombres**.

![Directiva de resolución de nombres](images/b07-directiva-resolucion-nombres.jpg)


**8.** En **Crear reglas**, aplicamos la regla por **Sufijo** y escribimos el nombre de nuestro dominio: `Neury0829.local`.

![Regla por sufijo para el dominio](images/b08-regla-sufijo.jpg)


**9.** Hacemos clic en **Examinar** para elegir la entidad de certificación.

![Selección del certificado de la entidad de certificación](images/b09-examinar-certificado.jpg)


**10.** Seleccionamos el certificado y hacemos clic en **Aceptar**.

![Entidad de certificación seleccionada](images/b10-entidad-seleccionada.jpg)


> [!NOTE]
> El campo **Entidad de certificación** es opcional. En este laboratorio se seleccionó el certificado disponible (*DigiCert Assured ID Root CA*). En un entorno con servicios de certificados propios (AD CS), se elegiría la CA interna.

**11.** Habilitamos DNSSEC en la regla marcando **Habilitar DNSSEC en esta regla** y activamos las dos opciones siguientes:

| Opción | Valor |
|---|---|
| Habilitar DNSSEC en esta regla | Marcada |
| Validación: requerir que el servidor DNS haya validado el nombre y la dirección | Marcada |
| IPsec: usar IPsec entre el cliente DNS y el servidor DNS | Marcada |
| Tipo de cifrado | Sin cifrado (solo integridad) |

![DNSSEC habilitado en la regla con validación e IPsec](images/b11-habilitar-dnssec.jpg)


**12.** Bajamos al final de la ventana, hacemos clic en **Crear** (para agregar la regla a la tabla) y luego en **Aplicar**.

![Botones Crear y Aplicar de la directiva de resolución de nombres](images/b12-aplicar.jpg)


> [!IMPORTANT]
> En esta captura la tabla *Directivas de resolución de nombres* aparece **vacía** en el momento de aplicar: la regla todavía no se había agregado. Para que la regla quede guardada en la GPO hay que hacer clic en **Crear** (la regla aparece como una fila en la tabla) y **después** en **Aplicar**.

**13.** Por último, ejecutamos el siguiente comando en el Símbolo del sistema para aplicar la configuración de manera forzada:

```cmd
gpupdate /force
```

![Ejecución de gpupdate /force](images/b13-gpupdate-force.jpg)


> [!TIP]
> Para que el cliente reciba la regla, también debe ejecutar `gpupdate /force` (o reiniciarse) y estar dentro de la unidad organizativa `Empresas`.

---

## Verificación final

Comandos opcionales para comprobar el resultado.

**En el servidor** (PowerShell como administrador):

```powershell
Get-DnsServerZone -Name "Neury0829.local" | Select-Object ZoneName, IsSigned
Get-DnsServerSigningKey -ZoneName "Neury0829.local"
```

Debe mostrar `IsSigned` en `True` y las claves KSK y ZSK con la longitud y la frecuencia de sustitución configuradas.

**En el cliente** (PowerShell como administrador):

```powershell
Get-DnsClientNrptPolicy
Resolve-DnsName -Name Neury0829.local -Type DNSKEY -Server 192.168.0.10 -DnssecOk
```

`Get-DnsClientNrptPolicy` debe listar la regla del sufijo `.Neury0829.local` con DNSSEC habilitado. La consulta debe devolver los registros `DNSKEY` y sus firmas `RRSIG`.

**GPO aplicada** (CMD en el cliente):

```cmd
gpresult /r /scope computer
```

## Solución de problemas frecuentes

| Problema | Causa probable | Solución |
|---|---|---|
| La opción DNSSEC aparece en gris | La vista aún no se actualizó | Hacer clic en **Actualizar** y repetir el procedimiento |
| El cliente no tiene la regla NRPT | La regla no se creó, o el equipo no está en la OU `Empresas` | Agregar la regla con **Crear** y **Aplicar**, mover el equipo a la OU y ejecutar `gpupdate /force` |
| El cliente deja de resolver nombres del dominio | La validación se exige pero la zona o el servidor no responden con datos validados | Revisar `Get-DnsServerZone` (`IsSigned`), la conectividad con el DNS y la regla NRPT |
| No se ven los registros DNSKEY/RRSIG | La vista no está actualizada | Clic derecho sobre el dominio y **Actualizar** |

---

[← Volver al laboratorio](../README.md)
