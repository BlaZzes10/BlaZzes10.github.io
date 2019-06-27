---
layout: post
title: Whatsapp Gateway
---

# Introduction
Many companies would like to have an application to send whatsapp messages since its the messaging service par excellence and almost everyone uses it, so they would reach more people.
Today I come to introduce you a small script made in java that allows you to send a whatsapp message to a client through a phone line without the necessity of connecting to whatsapp.
# Basic concepts
To start programming first we need to understand some concepts.
First, everyone have a whatsapp ID. the whatsapp ID is our telephone number with the prefix of our country.
They are different methods of getting a customer ID and its secret key, one of them is WAPI (The API of Whatsapp). In the network there are different web pages that explain how to install it in environments like ubuntu, debian..
URL_GATEWAY is our host url that sends the message.
```java
  private static final String ID_CLIENTE = ""; // TU ID DE CLIENTE
  private static final String CLIENT_SECRET = ""; //TU CLAVE SECRETA DE CLIENTE WHATSAPP
  private static final String URL_GATEWAY = ""; // TU HOST QUE HARA COMO GATEWAY
  ```
# Rest of code
```java
 public static void main(String[] args) throws Exception {
    String numero = "1321425";  // Número telefónico del destinatario, junto con su código de pais
    String mensaje = "Hola"; // Mensaje deseado
    //LLAMADA AL MÉTODO ENCARGADO DE ENVIAR EL MENSAJE
    envio(numero, mensaje);
  }
  
  public static void envio(String numero, String mensaje) {

    String payload="{ Número: " + numero + " | Mensaje: " + mensaje;

    try {
	    URL url = new URL(URL_GATEWAY);
	    HttpURLConnection conectividad= (HttpURLConnection) url.openConnection();
	    HttpURLConnection conex = conectividad;
	    Post(conex);
	    OutputStream output = conex.getOutputStream();
	    output.write(payload.getBytes());
	    output.flush();
	    output.close();
	
	    int CodigoEstado = conex.getResponseCode();
	    System.out.println("Respuesta del Gateway: \n");
	    System.out.println("Codigo de estado: " + CodigoEstado);
	    InputStreamReader Escritura;
	    if(CodigoEstado==200) {
	    	Escritura=new InputStreamReader(conex.getInputStream());
	    }else {
	    	Escritura=new InputStreamReader(conex.getErrorStream());
	    }
	    BufferedReader br = new BufferedReader(Escritura);
	    String outputS;
	    while ((outputS = br.readLine()) != null) {
	        System.out.println(outputS);
	    }
	    conex.disconnect();
    }catch(Exception e) {
      	System.out.println("No se pudo enviar el mensaje");
    }
  }
  public static void Post(HttpURLConnection conex) throws ProtocolException {
	  conex.setDoOutput(true);
	  conex.setRequestMethod("POST");
	  conex.setRequestProperty("ID", ID_CLIENTE);
	  conex.setRequestProperty("CLIENT_SECRET", CLIENT_SECRET);
	  conex.setRequestProperty("Content-Type", "application/json");
  }
```
The following code will try to send a whatsapp message to the destination number.
Its worth mentioning that all this code could be integrated into a graphical interface (swing). 
In subsequent posts I will bring this code in an graphical interface.
