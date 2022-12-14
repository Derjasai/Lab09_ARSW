### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW

### Integrantes
* Daniel Ramos
* Nicole Montaña

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

### Dependencias
* Cree una cuenta gratuita dentro de Azure. Para hacerlo puede guiarse de esta [documentación](https://azure.microsoft.com/es-es/free/students/). Al hacerlo usted contará con $100 USD para gastar durante 12 meses.

### Parte 0 - Entendiendo el escenario de calidad

Adjunto a este laboratorio usted podrá encontrar una aplicación totalmente desarrollada que tiene como objetivo calcular el enésimo valor de la secuencia de Fibonnaci.

**Escalabilidad**
Cuando un conjunto de usuarios consulta un enésimo número (superior a 1000000) de la secuencia de Fibonacci de forma concurrente y el sistema se encuentra bajo condiciones normales de operación, todas las peticiones deben ser respondidas y el consumo de CPU del sistema no puede superar el 70%.

### Parte 1 - Escalabilidad vertical

1. Diríjase a el [Portal de Azure](https://portal.azure.com/) y a continuación cree una maquina virtual con las características básicas descritas en la imágen 1 y que corresponden a las siguientes:
    * Resource Group = SCALABILITY_LAB
    * Virtual machine name = VERTICAL-SCALABILITY
    * Image = Ubuntu Server 
    * Size = Standard B1ls
    * Username = scalability_lab
    * SSH publi key = Su llave ssh publica

![Imágen 1](images/part1/part1-vm-basic-config.png)

2. Para conectarse a la VM use el siguiente comando, donde las `x` las debe remplazar por la IP de su propia VM (Revise la sección "Connect" de la virtual machine creada para tener una guía más detallada).

    `ssh scalability_lab@xxx.xxx.xxx.xxx`

3. Instale node, para ello siga la sección *Installing Node.js and npm using NVM* que encontrará en este [enlace](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/).
4. Para instalar la aplicación adjunta al Laboratorio, suba la carpeta `FibonacciApp` a un repositorio al cual tenga acceso y ejecute estos comandos dentro de la VM:

    `git clone <your_repo>`

    `cd <your_repo>/FibonacciApp`

    `npm install`

5. Para ejecutar la aplicación puede usar el comando `npm FibinacciApp.js`, sin embargo una vez pierda la conexión ssh la aplicación dejará de funcionar. Para evitar ese compartamiento usaremos *forever*. Ejecute los siguientes comando dentro de la VM.

    ` node FibonacciApp.js`

6. Antes de verificar si el endpoint funciona, en Azure vaya a la sección de *Networking* y cree una *Inbound port rule* tal como se muestra en la imágen. Para verificar que la aplicación funciona, use un browser y user el endpoint `http://xxx.xxx.xxx.xxx:3000/fibonacci/6`. La respuesta debe ser `The answer is 8`.

![](images/part1/part1-vm-3000InboudRule.png)

7. La función que calcula en enésimo número de la secuencia de Fibonacci está muy mal construido y consume bastante CPU para obtener la respuesta. Usando la consola del Browser documente los tiempos de respuesta para dicho endpoint usando los siguintes valores:
    * 1000000
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200964277-66a0159c-4995-44d0-8595-545afe9eb994.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200973415-28726546-12a1-4dbb-87eb-d32ceb9cff9c.png)
    
    * 1010000
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200964397-684fdc56-b5cb-46fc-ae7a-370a8dfd9a3f.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200974547-acb6f8b3-05ee-477c-8a42-60f82e953b0d.png)
    
    * 1020000
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200964528-94743c0a-82b0-409c-9649-3ac3070349a2.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200974769-b6c5ca8f-383f-4f30-9069-5bb3154a936e.png)
    
    * 1030000
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200964944-da19a2ec-2bc7-4506-9386-a94af8332036.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200974875-b06ed3ec-608b-4b21-9289-75d85ef8b08e.png)
    
    * 1040000
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200965376-79ce53bc-103e-4844-a7c8-d215d9a9fd19.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200974974-30870877-f716-4ff9-a518-7526b1e37d95.png)
    
    * 1050000
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200965468-94ce3c6c-f0cf-4b18-b040-1a74ddaaa1ac.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200975033-94abaa1b-a7cf-4553-9889-39b3ed55aff9.png)
    
    * 1060000
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200965521-a5c7229d-6275-4678-a370-305aba8d8d81.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200975101-b6981b36-9e20-40d6-807f-18a09608175c.png)
    
    * 1070000
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200965995-7e5b23ec-378f-4c18-875e-6233ad1dbd17.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200975166-bab5bfb0-03eb-4dd7-9d0e-c13ab2283690.png)
    
    * 1080000
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200966112-41df825a-19bb-474e-ad7e-b2d9e8cbd552.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200975248-e5307fd6-dc29-4b3d-ad48-5b90a0ecf08b.png)
    
    * 1090000    
    
    1vcpu, 0.5GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200966225-30810ddc-741c-4e10-9400-f86d0a778c97.png)
    
    2vcpu, 8GiB
    
    ![image](https://user-images.githubusercontent.com/66084536/200975305-0e515cd1-7809-43ad-a04f-b8e6af3ded28.png)


8. Dírijase ahora a Azure y verifique el consumo de CPU para la VM. (Los resultados pueden tardar 5 minutos en aparecer).

![Imágen 2](images/part1/part1-vm-cpu.png)

1vcpu, 0.5GiB

![image](https://user-images.githubusercontent.com/66084536/200966325-48c397d2-5ec4-4c82-aafd-9ca09087c9bf.png)

2vcpu, 8GiB

![image](https://user-images.githubusercontent.com/66084536/200979052-483e0d5b-cdca-4e82-be25-69724212ebfc.png)



9. Ahora usaremos Postman para simular una carga concurrente a nuestro sistema. Siga estos pasos.
    * Instale newman con el comando `npm install newman -g`. Para conocer más de Newman consulte el siguiente [enlace](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/).
    * Diríjase hasta la ruta `FibonacciApp/postman` en una maquina diferente a la VM.
    * Para el archivo `[ARSW_LOAD-BALANCING_AZURE].postman_environment.json` cambie el valor del parámetro `VM1` para que coincida con la IP de su VM.
    * Ejecute el siguiente comando.

    ```
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
    ```
    
1vcpu, 0.5GiB

![image](https://user-images.githubusercontent.com/66084536/200972492-e7850ed8-a3f3-422f-a236-eb3b19c0b575.png)
![image](https://user-images.githubusercontent.com/66084536/200972597-afb05fdf-10d5-49a2-a74e-47dc3ca18423.png)

2vcpu, 8GiB

![image](https://user-images.githubusercontent.com/66084536/200979875-77900d17-9d60-457e-9d63-f758199a9bb3.png)
![image](https://user-images.githubusercontent.com/66084536/200979924-c3fd1749-b8c2-448b-875e-ac21adae7348.png)


10. La cantidad de CPU consumida es bastante grande y un conjunto considerable de peticiones concurrentes pueden hacer fallar nuestro servicio. Para solucionarlo usaremos una estrategia de Escalamiento Vertical. En Azure diríjase a la sección *size* y a continuación seleccione el tamaño `B2ms`.

![Imágen 3](images/part1/part1-vm-resize.png)

11. Una vez el cambio se vea reflejado, repita el paso 7, 8 y 9.
12. Evalue el escenario de calidad asociado al requerimiento no funcional de escalabilidad y concluya si usando este modelo de escalabilidad logramos cumplirlo.
13. Vuelva a dejar la VM en el tamaño inicial para evitar cobros adicionales.

**Solución a las Preguntas**

1.	¿Cuántos y cuáles recursos crea Azure junto con la VM?

 ![image](https://user-images.githubusercontent.com/79550161/201004598-6d8b5ab8-2872-4f2a-8252-de55df0fa2e4.png)

2.	¿Brevemente describa para qué sirve cada recurso?

El primero es el grupo de recursos necesarios para el funcionamiento correcto, como la red virtual, la interfaz de red, la dirección IP pública, el grupo de seguridad de red, entre otros.
El segundo hace referencia a la máquina virtual creada.

3.	¿Al cerrar la conexión ssh con la VM, por qué se cae la aplicación que ejecutamos con el comando `npm FibonacciApp.js`? ¿Por qué debemos crear un *Inbound port rule* antes de acceder al servicio?

La aplicación se cae ya que es necesario cerrar la conexión SSH para detener la ejecución.
Debemos crear un inbound port rule antes de acceder al servicio ya que antes de esto el puerto se encuentra cerrado y no se permite realizar una consulta.

4.	Adjunte tabla de tiempos e interprete por qué la función tarda tanto tiempo.
 
![image](https://user-images.githubusercontent.com/79550161/201004634-504bb57e-6e02-4bea-838e-9f59ef85aa4a.png)

La función tarda tanto tiempo ya que no se está implementando una forma de guardar valores anteriores, por lo cual, para cada una de las consultas debe calcular todos los valores desde cero, haciendo que el consumo de la CPU sea muy grande.

5.	Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.
 
 ![image](https://user-images.githubusercontent.com/79550161/201004658-b7b9c3b0-5971-475d-9004-0d8a15dbda1a.png)
 
La función consume esa gran cantidad de CPU porque no se está realizando una búsqueda recursiva.

6.	Adjunte la imagen del resumen de la ejecución de Postman. Interprete:
 
 ![image](https://user-images.githubusercontent.com/79550161/201004678-070360ba-d33b-4cdf-8ded-b25e8f0586f9.png)
 
* Tiempos de ejecución de cada petición.

Ya que el tiempo de ejecución total de todas las consultas es de 3m 6.4s, en promedio cada una de las ejecuciones tuvo una duración de 18.6s.

* Si hubo fallos documéntelos y explique.

No se obtuvo ningún fallo en las ejecuciones.

7.	¿Cuál es la diferencia entre los tamaños `B2ms` y `B1ls` (no solo busque especificaciones de infraestructura)?

La diferencia es que el tamaño B2ms puede ser cargado sobre diversos sistemas operativos, pero el tamaño B1ls solo puede ser cargado sobre el sistema operativo Linux.

8.	¿Aumentar el tamaño de la VM es una buena solución en este escenario?, ¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?

No es una buena solución de este escenario aumentar el tamaño, ya que se observa una disminución en el tiempo de ejecución, pero no es un gran cambio, ya que sigue tardando demasiado.

9.	¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM? ¿Qué efectos negativos implica?

Al cambiar el tamaño se genera un mayor costo de pago y también se demorará la activación de la máquina, por lo que no estará disponible el servicio con este nuevo cambio.

10.	¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?

Si se encontró una mejora en el consumo de CPU, ya que el consumo promedio disminuyo considerablemente si lo comparamos con el consumo registrado inicialmente.

11.	Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?

Al aumentar la cantidad de ejecuciones paralelas podemos observar que el comportamiento del sistema es mucho mejor porcentualmente que el mostrado al inicio.


### Parte 2 - Escalabilidad horizontal

#### Crear el Balanceador de Carga

Antes de continuar puede eliminar el grupo de recursos anterior para evitar gastos adicionales y realizar la actividad en un grupo de recursos totalmente limpio.

1. El Balanceador de Carga es un recurso fundamental para habilitar la escalabilidad horizontal de nuestro sistema, por eso en este paso cree un balanceador de carga dentro de Azure tal cual como se muestra en la imágen adjunta.

![](images/part2/part2-lb-create.png)

2. A continuación cree un *Backend Pool*, guiese con la siguiente imágen.

![](images/part2/part2-lb-bp-create.png)

3. A continuación cree un *Health Probe*, guiese con la siguiente imágen.

![](images/part2/part2-lb-hp-create.png)

4. A continuación cree un *Load Balancing Rule*, guiese con la siguiente imágen.

![](images/part2/part2-lb-lbr-create.png)

5. Cree una *Virtual Network* dentro del grupo de recursos, guiese con la siguiente imágen.

![](images/part2/part2-vn-create.png)

#### Crear las maquinas virtuales (Nodos)

Ahora vamos a crear 3 VMs (VM1, VM2 y VM3) con direcciones IP públicas standar en 3 diferentes zonas de disponibilidad. Después las agregaremos al balanceador de carga.

1. En la configuración básica de la VM guíese por la siguiente imágen. Es importante que se fije en la "Avaiability Zone", donde la VM1 será 1, la VM2 será 2 y la VM3 será 3.

![](images/part2/part2-vm-create1.png)

2. En la configuración de networking, verifique que se ha seleccionado la *Virtual Network*  y la *Subnet* creadas anteriormente. Adicionalmente asigne una IP pública y no olvide habilitar la redundancia de zona.

![](images/part2/part2-vm-create2.png)

3. Para el Network Security Group seleccione "avanzado" y realice la siguiente configuración. No olvide crear un *Inbound Rule*, en el cual habilite el tráfico por el puerto 3000. Cuando cree la VM2 y la VM3, no necesita volver a crear el *Network Security Group*, sino que puede seleccionar el anteriormente creado.

![](images/part2/part2-vm-create3.png)

4. Ahora asignaremos esta VM a nuestro balanceador de carga, para ello siga la configuración de la siguiente imágen.

![](images/part2/part2-vm-create4.png)

5. Finalmente debemos instalar la aplicación de Fibonacci en la VM. para ello puede ejecutar el conjunto de los siguientes comandos, cambiando el nombre de la VM por el correcto

```
git clone https://github.com/daprieto1/ARSW_LOAD-BALANCING_AZURE.git

curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
source /home/vm1/.bashrc
nvm install node

cd ARSW_LOAD-BALANCING_AZURE/FibonacciApp
npm install

npm install forever -g
forever start FibonacciApp.js
```

Realice este proceso para las 3 VMs, por ahora lo haremos a mano una por una, sin embargo es importante que usted sepa que existen herramientas para aumatizar este proceso, entre ellas encontramos Azure Resource Manager, OsDisk Images, Terraform con Vagrant y Paker, Puppet, Ansible entre otras.

#### Probar el resultado final de nuestra infraestructura

1. Porsupuesto el endpoint de acceso a nuestro sistema será la IP pública del balanceador de carga, primero verifiquemos que los servicios básicos están funcionando, consuma los siguientes recursos:

```
http://52.155.223.248/
http://52.155.223.248/fibonacci/1
```

2. Realice las pruebas de carga con `newman` que se realizaron en la parte 1 y haga un informe comparativo donde contraste: tiempos de respuesta, cantidad de peticiones respondidas con éxito, costos de las 2 infraestrucruras, es decir, la que desarrollamos con balanceo de carga horizontal y la que se hizo con una maquina virtual escalada.

3. Agregue una 4 maquina virtual y realice las pruebas de newman, pero esta vez no lance 2 peticiones en paralelo, sino que incrementelo a 4. Haga un informe donde presente el comportamiento de la CPU de las 4 VM y explique porque la tasa de éxito de las peticiones aumento con este estilo de escalabilidad.

```
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
```

**Solución a las preguntas**

•	¿Cuáles son los tipos de balanceadores de carga en Azure y en qué se diferencian?, ¿Qué es SKU, qué tipos hay y en qué se diferencian?, ¿Por qué el balanceador de carga necesita una IP pública?

Los tipos de balanceadores de carga en Azure son el equilibrador de carga público y el equilibrador de carga interno (o privado). El público proporciona conexiones de salida para máquinas virtuales dentro de la red virtual, estas se realizan mediante la traducción de sus direcciones IP privadas a direcciones IP públicas, sus instancias se usan para equilibrar la carda del tráfico de internet en las máquinas virtuales. El privado se usa cuando se necesitan direcciones IP privadas solo en el front-end, se usa para equilibrar la carga del tráfico dentro de una red virtual.
SKU: Conocido como la unidad de mantenimiento de existencias (Stock Keeping Unit), es un código único que está compuesto habitualmente de letras y números, que identifica a un producto o servicio concreto dentro de Azure, este representa la oportunidad de poder adquirirlos.
Tipos de SKU:
-	Básico: Es un punto de entrada optimizado para los costos para que los desarrolladores aprendan sobre Azure Container Registry. Los registros tienen las mismas funciones de programación que los demás, sin embrago, el almacenamiento incluido y el rendimiento de las imágenes son más adecuadas para escenarios de uso inferior.
-	Estándar: Los registros ofrecen las mismas funcionalidades que los básicos, pero con más almacenamiento y un mayor rendimiento de las imágenes. Estos deberían satisfacer las necesidades de la mayoría de los escenarios de producción.
-	Premium: Los registros proporcionan la mayor cantidad de almacenamiento incluido y operaciones simultáneas, permite trabajar con escenarios de mayor volumen. Posee también la mayor capacidad de rendimiento de imágenes, agrega también nuevas características.
El balanceador de carga necesita una IP pública para que pueda ser accesible desde la red.

•	¿Cuál es el propósito del Backend Pool?

El grupo de back-end es un componente crítico del equilibrador de carga. Define el grupo de recursos que atenderán el tráfico para una regla de equilibrio de carga determinada.

•	¿Cuál es el propósito del Health Probe?

El propósito del Health Probe es detectar el estado del punto de conexión, las respuestas del sondeo determinan qué instancias del grupo de back-end recibirán nuevas conexiones. También detectan el error de una aplicación, con esta se controla el flujo para administrar la carga o el tiempo de inactividad planificado. Cuando falla un sondeo, el balanceador de carga dejará de enviar nuevas conexiones a la respectiva instancia en mal estado.

•	¿Cuál es el propósito de la Load Balancing Rule? ¿Qué tipos de sesión persistente existen, por qué esto es importante y cómo puede afectar la escalabilidad del sistema?.

El propósito es definir como se distribuye el tráfico entrante a todas las instancias dentro del grupo de back-end, asigna una configuración de IP y un puerto front-end determinados a varias direcciones IP y puertos de back-end.
La sesión persistente es importante ya que es un método utilizado para dirigir todas las solicitudes que se originan desde un cliente lógico independiente a un servidor web de backend independiente, los servidores backend se beneficia de esta. Permite mejorar el rendimiento de nuestra aplicación. Pueden afectar la escalabilidad ya que al estar conectados a una sesión y al nodo del backend, si este llega a un nivel de carga limite, el balanceador de carga no podrá redirigir la sesión a otro nodo del backend.

•	¿Qué es una Virtual Network? ¿Qué es una Subnet? ¿Para qué sirven los address space y address range?

Vitual Network es el bloque de creación fundamental de una res privada. Permite muchos tipos de recursos de Azure para comunicarse de forma segura entre usuarios, con internet y con las redes sociales. Es similar a una red tradicional que funcionaria en su propio centro de datos, pero aporta ventajas adicionales de la infraestructura de Azure, como la disponibilidad y el aislamiento.
Subnet es un rango de direcciones IP en la red virtual, puede dividir una red virtual en varias subredes para la organización y la seguridad.
Los address space sirven para separa una red virtual en uno o más intervalos de direcciones que no se superponen, se puede acceder a través de una única conexión, por un protocolo. El address range es la que define el rango de los intervalos de direcciones.

•	¿Qué son las Availability Zone y por qué seleccionamos 3 diferentes zonas?. ¿Qué significa que una IP sea zone-redundant?

Las Availability Zone son ubicaciones físicas separadas dentro de cada región de Azure que son tolerantes a los fallos locales, están diseñadas para ayudar a lograr la confiabilidad de las cargas de trabajo críticas para el negocio. Estas definen la recuperación ante desastres y los limites de residencia de datos. Se seleccionan tres zonas diferentes para garantizar la resiliencia, es decir, si una zona se ve afectada no se vean afectados todos los nodos y se garantice la disponibilidad.
Que una IP sea zone-redundant significa que puede ser replicada automáticamente en otra zona.

•	¿Cuál es el propósito del Network Security Group?

El propósito del Network Security Group es filtrar el tráfico de red entre los recursos de Azure en una red virtual de la misma. Este contiene reglas de seguridad que permiten o deniegan el tráfico de red entrante o saliente desde varios tipos de recursos de Azure.

•	Presente el Diagrama de Despliegue de la solución.

 ![Lab09 DiagramaDesplieque](https://user-images.githubusercontent.com/79550161/200997700-4edeac90-aad0-4619-a678-71a03e945981.png)




