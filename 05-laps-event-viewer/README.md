# Práctica 5: Implementación de LAPS y Auditoría con Visor de Eventos

La implementación de Local Administrator Password Solution (LAPS) es un control de ciberseguridad fundamental para evitar ataques de movimiento lateral (Pass-the-Hash), garantizando que las cuentas de administrador local de cada equipo tengan contraseñas únicas, complejas y rotativas de forma automática.

## Fase 1: Extensión del Esquema de Active Directory
Antes de configurar las políticas, Active Directory necesita los campos para almacenar las contraseñas.
1. Abre **PowerShell** como Administrador en tu Controlador de Dominio.
2. Importa el módulo de administración: `Import-Module AdmPwd.PS`
3. Extiende el esquema ejecutando: `Update-AdmPwdADSchema`

![Extensión del esquema de AD](images/step_01.jpg)

## Fase 2: Configuración de Permisos
Los equipos necesitan autorización para escribir su propia contraseña en el esquema.
1. Otorga el permiso de autoescritura a los equipos apuntando a su Unidad Organizativa (OU):
   `Set-AdmPwdComputerSelfPermission -OrgUnit "OU=TusEquipos,DC=tudominio,DC=local"`
2. Otorga permisos de lectura únicamente a los administradores autorizados.

![Configuración de Permisos](images/fase2_permisos.png)

## Fase 3: Configuración de la GPO (Directiva de Grupo)
1. Abre la **Administración de directivas de grupo (gpmc.msc)**.
2. Crea una GPO llamada "Politica_LAPS" y vincúlala a la OU.
3. Edita la GPO en: `Configuración del equipo > Directivas > Plantillas administrativas > LAPS`.
4. Habilita **Enable local admin password management**.
5. Configura **Password Settings**.

![Configuración GPO](images/fase3_gpo.png)

## Fase 4: Despliegue en el Cliente y Auditoría en el Visor de Eventos
1. Instala el paquete cliente de LAPS en la máquina cliente.
2. Actualiza políticas: `gpupdate /force`
3. Abre el **Visor de Eventos** (`eventvwr.msc`).
4. Navega a `Registros de aplicaciones y servicios > LAPS`.

![Visor de Eventos LAPS](images/fase4_eventos.png)

## Verificación Final
Verifica el atributo `ms-Mcs-AdmPwd` en el Editor de atributos de las propiedades del equipo en AD.

![Atributo Contraseña LAPS](images/verificacion_final.png)
