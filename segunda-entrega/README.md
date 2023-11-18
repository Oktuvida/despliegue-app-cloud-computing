# Segunda Entrega

Este documento detalla los pasos de implementación del WAF/Frontdoor/app-gateway.

## Pasos

### Inicializar Clúster

Siguiendo los pasos de la entrega anterior, se crea el clúster mediante la terminal de Azure. Los resultados de esta acción se presentan en la siguiente imagen.

![Inicializar Cluster](./screenshots/crear_cluster.jpg)

Para exponer la dirección IP pública de la aplicación frontend, se deben realizar los siguientes cambios en el archivo [./k8s-specifications/vote-service.yaml](/segunda-entrega/k8s-specifications/vote-service.yaml) y en el archivo [./k8s-specifications/result-service.yaml](/segunda-entrega/k8s-specifications/result-service.yaml):

- Modificar el `type` a `LoadBalancer`.
- Establecer el `port` en `80`.
- Eliminar el `targetPort`.

La imagen a continuación sirve como referencia para estos cambios. 

![Cambios Despliegue ](./screenshots/cambios_despliegue.png)

Es importante mencionar que estos pasos se deben realizar si se esta partiendo de la primera entrega, siendo necesario el redeploy de la aplicación.

```bash
# Para actualizar si ya esta creado
kubectl apply -f /k8s-specifications 
```

Si se parte desde la segunda entrega, con los nuevos archivos de la carpeta [./k8s-specifications/](/segunda-entrega/k8s-specifications/), se siguen los siguientes comandos:

```bash
namespace="vote"
kubectl delete namespace --ignore-not-found=true -n $namespace
kubectl create namespace $namespace
kubectl apply -f vote-specifications/ -n $namespace
```

Estos cambios permiten obtener la dirección IP pública, como se muestra en la siguiente imagen:

![Cambios IP](./screenshots/ip_despliegue.jpg)

La aplicación resultante puede ser visualizada a través de un navegador:

![Aplicación Pública](screenshots/resultados_despliegue.jpg)

