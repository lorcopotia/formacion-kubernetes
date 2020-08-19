### Diferencia entre ENTRYPOINT y CMD

En Docker, podemos construir imágenes a partir de archivos de texto plano conocidos como Dockerfiles.

Un Dockerfile nos permite definir un comando a ejecutar por defecto, para cuando se inicien contenedores a partir de nuestra imagen.

Tenemos 2 instrucciones para este propósito: ENTRYPOINT y CMD.
¿Qué ocurre al definir un CMD sin definir un ENTRYPOINT?

Si sólo especificas un `CMD`, entonces Docker ejecutará dicho comando usando el `ENTRYPOINT` por defecto, que es `/bin/sh -c`.

Respecto al "entrypoint" (punto de entrada) y "cmd" (comando), puedes sobreescribir ninguno, ambos, o sólo uno de ellos.

Si especificas ambos, entonces el `ENTRYPOINT` especifica el ejecutable que usará el contenedor, y `CMD` se corresponde con los parámetros a usar con dicho ejecutable.

Por ejemplo, si tu Dockerfile contiene:

    FROM ubuntu:16.04
    CMD ["/bin/date"]

Entonces estás usando el `ENTRYPOINT` por defecto (que es `/bin/sh -c`), y ejecutando `/bin/date` sobre dicho punto de entrada.

El proceso del contenedor se iniciará con la ejecución de `/bin/sh -c /bin/date`.

Y al ejecutar esta imagen, el contenedor imprimirá por defecto la fecha actual:

    $ docker build -t test .
    $ docker run test
    Tue Dec 19 10:37:43 UTC 2016

Es posible sobreescribir el comando CMD a usar por defecto, desde la misma línea de comandos (en tal caso se ejecutará el comando indicado):

    $ docker run test /bin/hostname
    bf0274ec8820

Si usas la instrucción `ENTRYPOINT`, entonces Docker usará el ejecutable que le indiques, y la instrucción `CMD` te permitirá definir un parámetro por defecto.

Por ejemplo, si tu Dockerfile contiene:

    FROM ubuntu:16.04
    ENTRYPOINT ["/bin/echo"]
    CMD ["Hello"]

Entonces producirá como resultado:

    $ docker build -t test .
    $ docker run test
    Hello

También puedes especificar un valor diferente para `CMD` al iniciar un contenedor, y se considerará como parámetro para el ejecutable `/bin/echo` (en lugar del que viene por defecto):

    $ docker run test Hi
    Hi

Si lo deseas, también puedes sobreescribir el valor del `ENTRYPOINT` definido en el Dockerfile.

Esto es menos común, pero también es posible.

Por ejemplo, en este caso, te permitiría usar un ejecutable distinto a `/bin/echo` como punto de entrada:

    $ docker run --entrypoint=/bin/hostname test
    b2c70e74df18

#### Sintaxis del Dockerfile

Se pueden usar de 2 formatos en la sintaxis admitida por un Dockerfile, para definir tanto `CMD` como `ENTRYPOINT`.

De hechos ambas sintaxis están disponibles también para la instrucción RUN.

Las 2 alternativas que tenemos son: "shell form" y "exec form".

Veamos brevemente en qué consisten, para que ambas maneras te resulten familiares en adelante.

###### Shell form

    <instruction> <command>

Ejemplos:

    RUN apt-get install python3
    CMD echo "Hello world"
    ENTRYPOINT echo "Hello world"

Cuando las instrucciones se ejecutan en "shell form", el comando correspondiente se ejecuta de esta manera: `/bin/sh -c <command>`.

Por ejemplo, en el siguiente Dockerfile:

    ENV name John Dow
    ENTRYPOINT echo "Hello, $name"

cuando se inicia un contenedor con docker run -it <image> se produce el siguiente resultado:

    Hello, John Dow

Nótese que la variable "name" es reemplazada con su valor correspondiente.

###### Exec form

Esta es la forma preferida para las instrucciones CMD y ENTRYPOINT.

    <instruction> ["executable", "param1", "param2", ...]

Ejemplos:

    RUN ["apt-get", "install", "python3"]
    CMD ["/bin/echo", "Hello world"]
    ENTRYPOINT ["/bin/echo", "Hello world"]

Cuando se usa "exec form", el ejecutable se llama directamente, sin "shell processing" como en el caso anterior.

Por ejemplo, para el siguiente Dockerfile:

    ENV name John Dow
    ENTRYPOINT ["/bin/echo", "Hello, $name"]

cuando se inicia un contenedor con docker run -it <image> se produce el siguiente resultado:

    Hello, $name

Y la variable no es sustituida en este caso.

#### Ejemplo usando sólo CMD

Veamos ahora un ejemplo ilustrativo usando CMD.

Para esta demo vamos a definir un Dockerfile que instala las dependencias "cowsay" y "screenfetch", a fin de tener ambos comandos disponibles y usarlos en la explicación.

    FROM debian:jessie-slim

    RUN apt-get update                                      && \
        apt-get install -y --no-install-recommends             \
            cowsay                                             \
            screenfetch                                     && \
        rm -rf /var/lib/apt/lists/*

    ENV PATH "$PATH:/usr/games"
    CMD ["cowsay", "Yo, CMD !!"]

Puedes construir una imagen llamada "demo" usando `docker build -t demo .`, y ejecutar un contenedor con `docker run demo`.

Esto ejecutará el comando por defecto indicado en la instrucción `CMD`, produciendo el siguiente resultado:

    ____________
    < Yo, CMD !! >
    ------------
           \   ^__^
            \  (oo)\_______
               (__)\       )\/\
                   ||----w |
                   ||     ||


Ejemplo usando `CMD` con su valor por defecto

Y tal como mencionamos antes, puedes sobreescribir el valor de `CMD` fácilmente. Por ejemplo, `docker run demo screenfetch -E` produce el siguiente resultado:

           _,met$$$$$gg.           @23404ebc7fe4
        ,g$$$$$$$$$$$$$$$P.        OS: Debian
      ,g$$P""       """Y$$.".      Kernel: x86_64 Linux 5.4.0-42-generic
     ,$$P'              `$$$.      Uptime: 7h 18m
    ',$$P       ,ggs.     `$$b:    Packages: 106
    `d$$'     ,$P"'   .    $$$     Shell:
     $$P      d$'     ,    $$P     CPU: Intel Core i7-8550U CPU @ 1.992GHz
     $$:      $$.   -    ,d$$'     RAM: 2031MB / 3935MB
     $$\;      Y$b._   _,d$P'     
     Y$$.    `.`"Y$$$$P"'         
     `$$b      "-.__              
      `Y$$                        
       `Y$$.                      
        `$$b.                    
         `Y$$b.                 
          `"Y$b._             
           `""""           


#### Ejemplo usando ENTRYPOINT

Si definimos un `ENTRYPOINT`, lo que se defina como `CMD` será tomado como argumento para el ejecutable del `ENTRYPOINT`.

    FROM debian:jessie-slim

    RUN apt-get update                                      && \
        apt-get install -y --no-install-recommends          \
            cowsay                                          \
            screenfetch                             &&      \
        rm -rf /var/lib/apt/lists/*

    ENV PATH "$PATH:/usr/games"

    CMD ["Yo, CMD!!"]
    ENTRYPOINT ["cowsay", "Yo, Entrypoint!!"]

Si construimos una imagen a partir de este último Dockerfile, y ejecutamos un contenedor usando `docker run demo-dos`, el resultado es:

    ____________________________
    < Yo, Entrypoint!! Yo, CMD!! >
    ----------------------------
           \   ^__^
            \  (oo)\_______
               (__)\       )\/\
                   ||----w |
                   ||     ||


#### Ejemplo usando `ENTRYPOINT` y un `CMD` por defecto

De igual manera que antes, podemos sobreescribir el valor de CMD fácilmente. Por ejemplo, si usamos `docker run demo-dos Overriding arg passed in cmd`, el resultado será:

    ________________________________________
    / Yo, Entrypoint!! Overriding arg passed \
    \ in cmd                                 /
    ----------------------------------------
           \   ^__^
            \  (oo)\_______
               (__)\       )\/\
                   ||----w |
                   ||     ||


#### Resumen:

Generalmente se usa la instrucción ENTRYPOINT para apuntar a la aplicación principal que se desea ejecutar, y CMD para definir unos parámetros por defecto.
