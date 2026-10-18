<div align="center">

# Windows Server 2022 Security Lab

![Windows Server](https://img.shields.io/badge/WINDOWS%20SERVER-2022-c084fc?style=flat-square&logo=windows&logoColor=white)
![Active Directory](https://img.shields.io/badge/ACTIVE%20DIRECTORY-AD%20DS-f472b6?style=flat-square)
![Security](https://img.shields.io/badge/SECURITY-LAPS%20%7C%20NTLM%20%7C%20BITLOCKER-60a5fa?style=flat-square)
![Infrastructure](https://img.shields.io/badge/INFRASTRUCTURE-ENTERPRISE-8b5cf6?style=flat-square)
![Estado](https://img.shields.io/badge/ESTADO-EN%20PROGRESO-fbbf24?style=flat-square)

</div>

---

## Descripción general

**Windows Server 2022 Security Lab** es un laboratorio práctico de administración de sistemas, infraestructura y seguridad informática. El proyecto reúne la implementación, configuración y validación de servicios de Windows Server 2022, junto con prácticas de endurecimiento (*hardening*), protección de credenciales y cifrado de datos.

El laboratorio comienza con la implementación de un dominio mediante Active Directory Domain Services (AD DS) y la unión de un cliente Windows al dominio. A partir de esa base, evoluciona hacia controles de seguridad como DNSSEC, administración de contraseñas locales con LAPS, protección con Credential Guard, restricción de NTLM y cifrado de unidades con BitLocker.

Cada práctica se documenta en su propia carpeta con el procedimiento paso a paso, las capturas de evidencia y las conclusiones técnicas.

**Estado del proyecto: en progreso.** Las prácticas se van agregando a medida que se completan y documentan.

---

## Prácticas implementadas

| # | Práctica | Tecnologías y alcance |
|:--:|---|---|
| 01 | [Active Directory Domain Services](./01-active-directory) | Windows Server 2022, AD DS, DNS, controlador de dominio, bosque y unión de un cliente Windows 10 al dominio. |
| 02 | [DNSSEC](./02-dnssec) | Seguridad del servicio DNS mediante DNSSEC. |
| 03 | [LAPS y Visor de Eventos](./03-laps-event-viewer) | Administración de contraseñas del administrador local y revisión de eventos de seguridad. |
| 04 | [Credential Guard y bloqueo de USB](./04-credential-guard-block-usb) | Protección de credenciales y restricción de dispositivos de almacenamiento USB. |
| 05 | [Bloqueo de NTLM](./05-ntlm-blocking) | Restricción del protocolo de autenticación NTLM en el dominio. |
| 06 | [Cifrado con BitLocker](./06-bitlocker) | Cifrado de unidades en el servidor y en el equipo cliente. |

---

## Entorno del laboratorio

| Elemento | Valor |
|---|---|
| Hipervisor | VMware Workstation Pro 17 |
| Servidor | Windows Server 2022 Standard Evaluation (experiencia de escritorio) |
| Cliente | Windows 10 Pro |
| Dominio | `Neury0829.local` |

> [!NOTE]
> Todas las pruebas se realizaron en un entorno aislado de laboratorio, con fines educativos.

---

## Estructura del repositorio

```text
windows-server-2022-security-lab/
├── 01-active-directory/
├── 02-dnssec/
├── 03-laps-event-viewer/
├── 04-credential-guard-block-usb/
├── 05-ntlm-blocking/
├── 06-bitlocker/
├── LICENSE
└── README.md
```

Cada carpeta contiene un `README.md` con el objetivo, el entorno, el procedimiento, las evidencias y las conclusiones de la práctica.

---

## Conclusión

Windows Server 2022 Security Lab reúne el trabajo práctico realizado sobre administración de sistemas, servicios de infraestructura y seguridad informática, desde la creación de un dominio hasta la aplicación de controles de seguridad sobre identidades, credenciales y datos.

La documentación registra las configuraciones aplicadas, las herramientas utilizadas, las decisiones técnicas y las evidencias obtenidas. El resultado es un repositorio de referencia para revisar los procedimientos realizados y demostrar experiencia práctica con tecnologías de infraestructura Windows y seguridad de sistemas.


---

## 👨‍💻 Autor

**Neury Jorge Montero Mejía**

*Estudiante de Tecnólogo en Seguridad Informática*

[![LinkedIn](https://img.shields.io/badge/LINKEDIN-NEURY%20MONTERO-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/neuryjmm)
[![GitHub](https://img.shields.io/badge/GITHUB-NEURYJMM-111111?style=flat-square&logo=github&logoColor=white)](https://github.com/NeuryJMM)

---
