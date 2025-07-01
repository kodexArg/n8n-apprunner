# n8n-apprunner

## Proyecto local

1. Instala las dependencias:

```bash
npm install
```

2. Arranca la instancia de n8n en modo desarrollo:

```bash
npm start
```

El servicio escuchará en http://localhost:5678

## Despliegue en AWS App Runner

App Runner detectará automáticamente el fichero `apprunner.yaml` y aplicará la siguiente configuración:

- Runtime gestionado Node.js 20
- Comandos de instalación `npm ci | npm install`
- Ejecución `npm start`
- Puerto expuesto 5678

Pasos:

```bash
# CREA o conecta tu repositorio en GitHub
# Sube el código y crea un nuevo servicio en App Runner indicando:
#   – Source type: GitHub
#   – Runtime: Use configuration file
#   – Branch: main
#   – (Opcional) Define los secrets de producción en el apartado Environment variables / Secrets
```

Con esto tendrás n8n desplegado sin Docker e integrado con los ciclos de CI/CD de GitHub.

---

## 🇬🇧 Quick AWS setup

*Purpose*: deploy a fully managed n8n service on AWS App Runner using Secrets Manager for sensitive data.

1. **Create three secrets in Secrets Manager**
   * `kdx-n8n-encryption-key` – plain-text encryption key used by n8n.
   * `kdx-generic-password` – plain-text value for Basic Auth password.
   * *(optional)* `rds!db-…` – JSON object with `username` and `password` for a Postgres database.
2. **Provision (optional) Amazon RDS Postgres** if you need a persistent external database.
3. **IAM instance role** for the App Runner service containing the following minimal policy:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [{
       "Effect": "Allow",
       "Action": "secretsmanager:GetSecretValue",
       "Resource": [
         "arn:aws:secretsmanager:us-east-1:789650504128:secret:kdx-n8n-encryption-key-*",
         "arn:aws:secretsmanager:us-east-1:789650504128:secret:kdx-generic-password-*",
         "arn:aws:secretsmanager:us-east-1:789650504128:secret:rds!*"  
       ]
     }]
   }
   ```
4. Deploy the service from GitHub and select *Use configuration file*.

---

## 🇪🇸 Configuración rápida en AWS

*Objetivo*: desplegar un servicio n8n en AWS App Runner gestionando las credenciales con Secrets Manager.

1. **Crea tres secretos en Secrets Manager**
   * `kdx-n8n-encryption-key` – clave de cifrado en texto plano utilizada por n8n.
   * `kdx-generic-password` – contraseña en texto plano para la autenticación básica.
   * *(opcional)* `rds!db-…` – objeto JSON con `username` y `password` para PostgreSQL.
2. **Provisiona (opcional) una instancia Amazon RDS Postgres** si necesitas base de datos externa persistente.
3. **Rol IAM de instancia** para el servicio App Runner con la política mínima anterior que concede `secretsmanager:GetSecretValue` a los ARNs indicados.
4. Despliega el repositorio desde GitHub seleccionando *Use configuration file* en App Runner.