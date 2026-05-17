# Taller de DevOps con un Monorepositorio - AWS

Este repositorio tiene como proposito mostrar el despliegue de una aplicación *FullStack* en la nube de AWS mediante flujos de CI/CD con GitHub Actions, construyendo la imágen del *Backend* y *Frontend* en un repositorio de AWS Container Registry y el despliegue con Code Deploy y S3 en una instancia de EC2 utilizando el protocolo OIDC para la *Autenticación* y *Autorización* con AWS y GitHub.

## Arquitectura y flujo de construcción

![Flujo de despliegue y construcción](./images/monorepo-aws.drawio.svg)

## Guía de Configuración

### IAM

- **Paso 1 - Crear el proveedor OIDC de GitHub en AWS IAM**

    Esto le dice a AWS: _"confío en los tokens que emite GitHub Actions"_.
    1. Ve a la consola de AWS → **IAM** → en el menú izquierdo, **Proveedores de identidad** → botón **Agregar proveedor**.
    2. Selecciona **OpenID Connect**.
    3. En **Nombre del proveedor** escribe exactamente:
        ```
        https://token.actions.githubusercontent.com
        ```
    4. En **Público** escribe:
        ```
        sts.amazonaws.com
        ```
    6. Haz clic en **Agregar proveedor**.
    Verás el proveedor creado en la lista, de la siguiente forma:
        ```
        token.actions.githubusercontent.com
        ```

- **Paso 2 - Crear el rol IAM que GitHub Actions va a asumir**

    Este rol define qué puede hacer GitHub Actions dentro de tu cuenta AWS.
    1. En IAM → **Roles** → **Crear rol**.
    2. En **Tipo de entidad de confianza** selecciona **Identidad Web**.
    3. En **Proveedor de identidad** selecciona el proveedor que acabas de crear:
        ```
        token.actions.githubusercontent.com
        ```
    4. En **Audience** selecciona `sts.amazonaws.com`.
    5. En **GitHub Organization** coloca el nombre de tu organización o tu usuario de GitHub
    6. Adicionalmente puedes especificar el repositorio y la rama para tener un control más granular.

    #### Agregar permisos al rol
    En el paso de permisos, añade estas políticas (puedes buscarlas por nombre):
    - `AmazonEC2ContainerRegistryPullOnly`
    - `AmazonEC2ContainerRegistryReadOnly`
    - `AWSCodeDeployRole`
    - 
    - `AmazonS3FullAccess` — para subir el artefacto al bucket.

# Prueba de despliegue