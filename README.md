<div align="center">

# Windows Server 2022 Security Lab

![Windows Server](https://img.shields.io/badge/WINDOWS%20SERVER-2022-c084fc?style=flat-square&logo=windows&logoColor=white)
![Active Directory](https://img.shields.io/badge/ACTIVE%20DIRECTORY-AD%20DS-f472b6?style=flat-square)
![Security](https://img.shields.io/badge/SECURITY-NTLM%20%7C%20DNSSEC%20%7C%20LAPS%20%7C%20NESSUS-60a5fa?style=flat-square)
![Infrastructure](https://img.shields.io/badge/INFRASTRUCTURE-ENTERPRISE-8b5cf6?style=flat-square)
![Estado](https://img.shields.io/badge/ESTADO-EN%20PROGRESO-fbbf24?style=flat-square)

</div>

---

## Descripción general

**Windows Server 2022 Security Lab** es un laboratorio práctico de administración de sistemas, infraestructura y seguridad informática. El proyecto reúne la implementación, configuración y validación de servicios de Windows Server 2022, junto con prácticas de endurecimiento (*hardening*), protección de credenciales y cifrado de datos.

El laboratorio comienza con la implementación de un dominio mediante Active Directory Domain Services (AD DS) y la unión de un cliente Windows al dominio. A partir de esa base, evoluciona hacia controles de seguridad como la restricción de NTLM, la protección de credenciales con Credential Guard, DNSSEC y la administración de contraseñas locales con LAPS, y cierra con una evaluación de vulnerabilidades con Nessus.

Cada práctica se documenta en su propia carpeta con el procedimiento paso a paso, las capturas de evidencia y las conclusiones técnicas.


---

## Prácticas implementadas

| # | Práctica | Tecnologías y alcance |
|:--:|---|---|
| 01 | [Active Directory Domain Services](./01-active-directory) | Windows Server 2022, AD DS, DNS, controlador de dominio, bosque y unión de un cliente Windows 10 al dominio. |
| 02 | [Bloqueo de NTLM](./02-ntlm-blocking) | Restricción del protocolo de autenticación NTLM en el dominio. |
| 03 | [Credential Guard y bloqueo de USB](./03-credential-guard-block-usb) | Protección de credenciales y restricción de dispositivos de almacenamiento USB. |
| 04 | [DNSSEC](./04-dnssec) | Seguridad del servicio DNS mediante DNSSEC. |
| 05 | [LAPS y Visor de Eventos](./05-laps-event-viewer) | Administración de contraseñas del administrador local y revisión de eventos de seguridad. |
| 06 | [Evaluación de vulnerabilidades con Nessus](./06-nessus) | Escaneo de vulnerabilidades con Nessus y análisis de hallazgos. |

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
├── 02-ntlm-blocking/
├── 03-credential-guard-block-usb/
├── 04-dnssec/
├── 05-laps-event-viewer/
├── 06-nessus/
├── LICENSE
└── README.md
```

Cada carpeta contiene un `README.md` con el objetivo, el entorno, el procedimiento, las evidencias y las conclusiones de la práctica.

---

## Conclusión

Windows Server 2022 Security Lab reúne el trabajo práctico realizado sobre administración de sistemas, servicios de infraestructura y seguridad informática, desde la creación de un dominio hasta la aplicación de controles de seguridad sobre identidades y credenciales, y la evaluación de vulnerabilidades.

La documentación registra las configuraciones aplicadas, las herramientas utilizadas, las decisiones técnicas y las evidencias obtenidas. El resultado es un repositorio de referencia para revisar los procedimientos realizados y demostrar experiencia práctica con tecnologías de infraestructura Windows y seguridad de sistemas.


---

## 👨‍💻 Autor

**Neury Jorge Montero Mejía**
*Estudiante de Tecnólogo en Seguridad Informática*

[![LinkedIn](https://img.shields.io/badge/LINKEDIN-NEURY%20MONTERO-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/neuryjmm)
[![GitHub](https://img.shields.io/badge/GITHUB-NEURYJMM-111111?style=flat-square&logo=github&logoColor=white)](https://github.com/NeuryJMM)

---
