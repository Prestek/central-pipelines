# Central-Pipelines

Central pipelines para trabajar con Spring Boot, SonarCloud y AWS.

Este repositorio contiene pipelines reutilizables que facilitan la integración y despliegue de tus proyectos. Actualmente se incluyen:

- **Pipeline para SonarCloud (release 0.1.1):** Integración de SonarCloud en tus proyectos.
- **Pipeline para despliegue automático en EC2 (release 0.2.2):** Automatiza el despliegue de tu aplicación en una instancia EC2.

---

## Despliegue Automático en EC2

Para utilizar el pipeline de despliegue automático, necesitas configurar los siguientes elementos:

### Requisitos

1. **Cuenta AWS:** Necesitas una cuenta AWS activa.
2. **Secretos en AWS Secrets Manager:** Configura los secretos que tu aplicación necesite.
3. **Instancia EC2:** Despliega una instancia EC2 con permisos IAM adecuados para leer los secretos.
4. **Repositorio en GitHub:** El pipeline se ejecuta desde GitHub.

### Configuración del Workflow

#### Variables del Workflow

Estas variables deben configurarse en el workflow (inputs):

- **`PROJECT_NAME`**  
  *Descripción:* Nombre de la carpeta/proyecto en la instancia EC2.  
  *Requerido:* Sí

- **`IMAGE_NAME`**  
  *Descripción:* Nombre de la imagen Docker.  
  *Requerido:* Sí

- **`CONTAINER_NAME`**  
  *Descripción:* Nombre del contenedor Docker.  
  *Requerido:* Sí

- **`PORT`**  
  *Descripción:* Puerto en el que se expondrá el contenedor.  
  *Requerido:* Sí

- **`REPO_URL`**  
  *Descripción:* URL del repositorio a clonar.  
  *Requerido:* Sí

- **`BRANCH`**  
  *Descripción:* Rama de Git a desplegar.  
  *Requerido:* Sí

#### Variables Secretas (GitHub Secrets)

Configura en tu repositorio los siguientes secretos:

- **`EC2_SSH_KEY`**: Clave privada para la conexión SSH a la instancia EC2.
- **`EC2_HOST`**: Dirección o hostname de la instancia EC2.
- **`EC2_USER`**: Usuario para la conexión SSH a la instancia EC2.
- **`AWS_SECRET_ARN`**: ARN del secreto en AWS Secrets Manager (para obtener los secretos necesarios).

---

## Ejemplo de Uso

A continuación, se muestra un ejemplo de archivo YAML que utiliza el pipeline reutilizable para el despliegue:

```yaml
name: Deploy Release

on:
  push:
    branches:
      - main # Reemplazar por la rama que deseas desplegar

jobs:
  deploy:
    uses: PigBallARSW/central-pipelines/.github/workflows/deploy.yml@V0.2.3
    with:
      PROJECT_NAME: "Mi-Proyecto-EC2"       # Nombre de la carpeta/proyecto en la instancia EC2
      IMAGE_NAME: "my-app"                  # Nombre de la imagen Docker
      CONTAINER_NAME: "my-app"              # Nombre del contenedor Docker
      PORT: "8080"                        # Puerto en el que se expondrá el contenedor
      REPO_URL: "https://github.com/usuario/proyecto.git"  # URL del repositorio a desplegar
      BRANCH: "main"                      # Rama a desplegar (reemplaza si es necesario)
    secrets:
      EC2_SSH_KEY: ${{ secrets.EC2_SSH_KEY }}
      EC2_HOST: ${{ secrets.EC2_HOST }}
      EC2_USER: ${{ secrets.EC2_USER }}
      AWS_SECRET_ARN: ${{ secrets.AWS_SECRET_ARN }}
```

Notas Adicionales:

Secretos: Asegúrate de configurar correctamente los secretos en GitHub y que la instancia EC2 tenga los permisos necesarios para acceder a AWS Secrets Manager.

Adaptación del Pipeline: Si tu entorno o sistema operativo es distinto (por ejemplo, si usas Ubuntu en lugar de Amazon Linux), ajusta los comandos de instalación en el workflow.

Pipeline SonarCloud: Revisa el pipeline específico para SonarCloud en este repositorio si necesitas integrar análisis de calidad de código.
