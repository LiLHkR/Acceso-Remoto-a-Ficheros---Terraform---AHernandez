# Acceso-Remoto-a-Ficheros---Terraform---AHernandez
# Proyecto de clase con dos servidores uno DB y otro FTP de 2ºASIR
En este proyecto vamos a utilizar Terraform para desplegar una infraestructura completa en AWS compuesta por dos instancias EC2 interconectadas. Todo el proceso de instalación y configuración de servicios es completamente automático mediante scripts(user_data), sin necesidad de intervención manual por SSH.
# Arquitectura del Despliegue
# La infraestructura consta de:
Servidor de Base de Datos (MariaDB): Aloja los usuarios virtuales del servicio FTP.
Servidor FTP (Vsftpd): Configurado con SSL/TLS explícito, modo pasivo y usuarios enjaulados (chroot), autenticando contra la base de datos remota.
# Requisitos Previos:
Antes de ejecutar el despliegue, asegúrate de tener en tu equipo:
Terraform instalado.
AWS CLI configurado con credenciales válidas.
Una Key Pair (.pem) creada en tu consola de AWS (Región us-east-1 por defecto).
# CONFIGURACION: 
El código es genérico, pero requiere una adaptación a tu cuenta de AWS. 
Es necesario editar únicamente el archivo main.tf.
Localiza en main.tf las líneas que definen acces_key, key_name, secret_key, token. Debes cambiar el valor por el nombre exacto de las claves que tienes creada en tu consola de AWS. 
Nota: KEY_NAME se encuentra un poco mas abajo que el resto. No es necesario editar los scripts setup_ftp.sh ni setup_db.sh.
# INSTRUCCIONES DE DESPLIEGUE
Sigue estos pasos en tu terminal:
Inicializar Terraform:
Descarga los plugins necesarios del proveedor AWS.
$terraform init
Planificar la infraestructura:
Verifica la sintaxis y revisa los recursos que se van a crear.
$terraform plan
Lanzar el despliegue:
Crea las instancias y ejecuta los scripts de instalación.
$terraform apply -auto-approve
# TIEMPO DE ESPERA (Importante):
Una vez Terraform finalice y muestre el mensaje "Apply complete!", las máquinas estarán encendidas pero el proceso de instalación continuará ejecutándose. Espera entre 3 y 5 minutos antes de intentar conectar para asegurar que todos los servicios han arrancado correctamente.
# COMO CONECTAR:
Al finalizar el despliegue, Terraform mostrará en la terminal la IP Pública del servidor FTP (ip_publica_ftp). Utiliza dicha IP para configurar tu cliente FTP (en mi caso Filezilla). Yo lo hice asi:
Servidor = IP pública proporcionada por Terraform
Protocolo = FTP Protocolo de Transferencia de Archivos
Cifrado = Requerir FTP explícito sobre TLS (Obligatorio)
Modo de acceso = Normal
Usuario = alumno
Contraseña = 1234
# Notas sobre la conexión:
Si aparece una advertencia sobre el certificado SSL, debe aceptarse (es un certificado autofirmado generado automáticamente durante el despliegue). El servidor incluye configuraciones específicas para evitar errores de cierre de conexión (ECONNABORTED) y compatibilidad con GnuTLS.
# Limpieza del Entorno:
Para destruir la infraestructura y evitar costes adicionales en AWS, ejecuta el siguiente comando al finalizar las pruebas
$terraform destroy -auto-approve
