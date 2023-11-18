# Segunda Entrega

Este documento detalla los pasos de implementación del WAF/Frontdoor/app-gateway.

## Pasos

### Crear el Resource Group

Primero que nada, se debe establecer un Resource Group donde se realizará todo el trabajo, este paso es el más sencillo, pues solo se debe asisgnar un nombre, en nuestro caso, se utilizó `MC_cloud-computing_clu-vote_eastus`. Vease la siguiente imagen como referencia.

Luego se debe establecer un Grupo de Recursos donde se llevará a cabo todo el trabajo. Este grupo proporciona un entorno organizado para administrar y agrupar recursos relacionados, lo que facilita la gestión y el mantenimiento durante el desarrollo del proyecto.

Esta fase es bastante sencilla, ya que solo implica asignar un nombre. En nuestro caso, se utilizó el nombre `MC_cloud-computing_clu-vote_eastus`. Puedes ver la imagen a continuación como referencia.

![Resource Group](screenshots/resource_group.png)

### Crear la Virtual Network

Para configurar el Application Gateway, es necesario emplear una Virtual Network. El primer paso es nombrar esta red, en este caso, se la denominó `AKS-vnet-16379065`, como se muestra en la imagen a continuación: 

![Name Virtual Network](screenshots/virtual_network_1.png)

Posteriormente, se debe definir el `Address space` para este proyecto. En este escenario específico, se optó por `10.224.0.0/12`, aunque es fundamental disponer de un espacio lo suficientemente amplio para futuras expansiones. Además, se deben crear dos subredes. La primera, `aks-subnet`, se configuró como `10.224.0.0/24`. La segunda, llamada `ingress-appgateway-subnet`, se definió como `10.225.0.0/24`. Se recomienda que estas subredes finalicen en `/24` para limitar a 255 direcciones, evitando un exceso de direcciones no utilizadas. Véase las siguientes imagenes como referencia:

![Primera Subnet](screenshots/virtual_network_2.png)

![Segunda Subnet](screenshots/virtual_network_3.png)

### Crear y configurar el Kubernet

El siguiente paso es la creación y configuración del clúster de Kubernetes en el que trabajaremos. Esta tarea es sencilla, solo se debe asignar un nombre al clúster, en este caso, se le llamó `Clue-vote`, y agregarlo al mismo Grupo de Recursos creado anteriormente, como se muestra en la imagen a continuación:

![Crear Kubernet](screenshots/crear_kubernet.png)

Posteriormente, siguiendo los pasos de la entrega anterior, se inicia la configuración del clúster a través de la terminal de Azure. Los resultados de esta acción se pueden apreciar en la siguiente imagen:

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

![Aplicación Votos](screenshots/resultados_despliegue.jpg)

![Recuento Resultados](screenshots/resultados_despliegue2.png)

### Configuración de la Política para el Web Application Firewall

Para el correcto funcionamiento del WAF en el Application Gateway, es necesario crear una política específica. En primer lugar, se asigna un nombre a esta política, en este caso, se utilizó el nombre `ag-aks`, y se configura en modo de detección. Luego, se debe asociar esta política a todos los recursos previamente establecidos. Puedes observar la imagen a continuación como referencia:

![Nombre de la Política](screenshots/waf_1.png)

Posteriormente, se procede a seleccionar las Managed Rules conforme a los diferentes niveles de seguridad requeridos. En este proyecto, que se considera relativamente sencillo y con bajos riesgos, se optó por el conjunto de reglas OWASP 3.0. No obstante, se recomienda encarecidamente revisar y ajustar estas reglas según la complejidad y los riesgos particulares de cada proyecto. La imagen a continuación muestra la configuración de las reglas:

![Configuración de Reglas](screenshots/waf_2.png)

