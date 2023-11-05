# Despliegue App Cloud Computing

## Integrantes

- Dafne Valeria Castellanos Rosas
- Diryon Yonith Mora Romero
- Laura Valentina Gonzalez Rodriguez

## ¿Qué aplicación se seleccionó?

Se tomó la aplicación [Example Voting App](https://github.com/dockersamples/example-voting-app), que consta de 4 microservicios, así como una aplicación frontend.

Cada uno de estos servicios tiene su propio archivo Dockerfile y su imagen correspondiente. Además, el archivo `docker-compose.yaml` define cómo se conectan estos servicios.

Por otra parte, el proyecto incluye la carpeta `k8s-specifications`, donde se definen las especificaciones YAML de los servicios para ejecutarlos en Kubernetes.
