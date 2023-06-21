# 1er-Parcial-SPD-2023
# Proyecto

<img src="https://github.com/MatiasDia210102/2do-Parcial-SPD-2023/blob/313e4837d546282e89042f8b7b6a8c4b53406451/Imagenes/2do%20Parcial%20SPD%202023.PNG?raw=true" width="800"/>
<img src="https://github.com/MatiasDia210102/2do-Parcial-SPD-2023/blob/313e4837d546282e89042f8b7b6a8c4b53406451/Imagenes/Esquema_SPD.PNG?raw=true" width="800"/>

## Descripcion Programa
AL presionar los botones el montacargas pasara por cada piso o frenara, encendiendo su respectivo led y marcandolo en el display

# Defines
### Descripcion 
Agrego los indices del display, leds y botones a cada pin del Arduino
~~~C 

// C++ code
// Matias Diaz DIV J TT

#define LEDROJO 10 
#define LEDVERDE 9
#define B 8
#define A 7
#define F 6
#define G 5
#define E 4
#define D 3
#define C 2

#define BOTONPARA A0
#define BOTOBAJA A1
#define BOTONSUBE A2
~~~
# Funciones
### Descripcion 
Declaro cada prototipo de cada funcion
~~~C 
void mostrar_mensaje_led(int led, char* mensaje);
void encender_apagar_led(int led, int led2);
void encender_segmentos(int estado0, int estado1, int estado2, int estado3, int estado4, int estado5, int estado6);

void encender_display(int numero);
void imprimir_mensaje(char* mensaje, int contador);

void mostrar_posicion_montacargas(char* mensaje, int contador, int led_high, int led_low);
int recorrer_pisos(char* condicion, int contador, int numero, int led_high, int led_low);

void ejecutar_programa(int estado_boton);
int sumar_o_restar(char* condicion, int contador, int numero);
~~~

# Variables Globales
~~~C 
int contador = 0;
int estado_boton = 0;
int paro;
~~~
# Setup
### Descripcion 
Configuro cada componente como salida o entrada digital
~~~C 
void setup()
{
  for(int i = 2; i <= 10; i++){
    pinMode(i, OUTPUT);
  }
  pinMode(BOTONPARA, INPUT);
  pinMode(BOTOBAJA, INPUT_PULLUP);
  pinMode(BOTONSUBE, INPUT_PULLUP);
  Serial.begin(9600);
}
~~~

# Principal
### Descripcion 
Leo el estado del boton de parar que se encarga de la ejecucion del programa
~~~C 
void loop()
{
  if(digitalRead(BOTONPARA) == HIGH){
    estado_boton = !estado_boton;
    delay(300);
  } 
  ejecutar_programa(estado_boton);
}
~~~
# LEDS
## Descripcion
Imprime el mensaje recibido del led que esta encendido
~~~C 
void mostrar_mensaje_led(int led, char* mensaje){
  
  if(digitalRead(led) == HIGH){Serial.println(mensaje);}
}
~~~
## Descripcion
Enciende un led y apaga otro
~~~C 
void encender_apagar_led(int led, int led2){
  
  digitalWrite(led, HIGH);
  digitalWrite(led2, LOW);
}
~~~

# DISPLAY
## Descripcion
Enciende o apaga los segmentos del display
~~~C
void encender_segmentos(int estado0, int estado1, int estado2, int estado3, int estado4, int estado5, int estado6){
  digitalWrite(A, estado0);
  digitalWrite(B, estado1);
  digitalWrite(C, estado2);
  digitalWrite(D, estado3);
  digitalWrite(E, estado4);
  digitalWrite(F, estado5);
  digitalWrite(G, estado6);
}
~~~

## Descripcion
Enciende un grupo de segmentos segun el numero recibido
~~~C
void encender_display(int numero){
  
  switch(numero) {
    case 0:
       encender_segmentos(HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, LOW);
       break;
    case 1:
       encender_segmentos(LOW, HIGH, HIGH, LOW, LOW, LOW, LOW);
       break;
    case 2:
      encender_segmentos(HIGH, HIGH, LOW, HIGH, HIGH, LOW, HIGH);
      break;
    case 3:
      encender_segmentos(HIGH, HIGH, HIGH, HIGH, LOW, LOW, HIGH);
      break;
    case 4:
      encender_segmentos(LOW, HIGH, HIGH, LOW, LOW, HIGH, HIGH);
      break;
    case 5:
      encender_segmentos(HIGH, LOW, HIGH, HIGH, LOW, HIGH, HIGH);
      break;
    case 6:
      encender_segmentos(HIGH, LOW, HIGH, HIGH, HIGH, HIGH, HIGH);
      break;
    case 7:
      encender_segmentos(HIGH, HIGH, HIGH, LOW, LOW, LOW, LOW);
      break;
    case 8:
      encender_segmentos(HIGH, HIGH, HIGH, HIGH, HIGH, HIGH, HIGH);
      break;
    case 9:
      encender_segmentos(HIGH, HIGH, HIGH, LOW, LOW, HIGH, HIGH);
      break;
  }
}
~~~
# MENSAJE
## Descripcion
Imprime el mensaje recibido junto a un contador
~~~C
void imprimir_mensaje(char* mensaje, int contador){
  
  Serial.print(mensaje);
  Serial.println(contador);
}
~~~
# CONTADOR
## Descripcion
Le suma o resta un numero al contador recibido segun la condicion recibida
~~~C
int sumar_o_restar(char* condicion, int contador, int numero){
  
  if(condicion == "Menor"){
    
    if(contador < numero){
      
      contador++;
    }
  } else if(condicion == "Mayor"){
    
    if(contador > numero){
      
      contador--;
    }
  }
  return contador;
}
~~~
# MONTACARGAS
## Descripcion
Informa en el display y monitor en que piso se encuentra el montacargas
~~~C
void mostrar_posicion_montacargas(char* mensaje, int contador, int led_high, int led_low){
  
  encender_display(contador); 
  encender_apagar_led(led_high, led_low);
  mostrar_mensaje_led(led_high, mensaje);
  imprimir_mensaje("Se encuentra en el piso ", contador);
}
~~~
# PISOS
## Descripcion
Informa si el montacargas esta en movimiento o no y si llego a su destino
~~~C
int recorrer_pisos(char* condicion, int contador, int numero, int led_high, int led_low){
  //en que piso esta
  mostrar_posicion_montacargas("\nMontacargas en movimiento",contador, led_high, led_low);
  contador = sumar_o_restar(condicion, contador, numero);
  delay(3000); // tiempo entre pisos
  
  //a que piso llego
  mostrar_posicion_montacargas("Montacargas en espera",contador, led_low, led_high);
  return contador;
}
~~~
# BOTONES
## Descripcion
Recorre los pisos segun que boton se presione o para el montacargas
~~~C
void ejecutar_programa(int estado_boton){
  
  if(estado_boton == LOW){
    if(paro == 0){

       Serial.println("\nMontacargas en espera. Presione un boton ");
       paro = 1;
    }
   
    if(digitalRead(BOTONSUBE) == LOW){
      if(contador < 9){
        
        contador = recorrer_pisos(estado_boton, "Menor", contador, 9, LEDVERDE, LEDROJO);
      } else {
        mostrar_posicion_montacargas("No puede subir", contador, LEDROJO, LEDVERDE);
      	delay(300);
      }
    }
    
    if(digitalRead(BOTOBAJA) == LOW){
      
      if(contador > 0){
      	contador = recorrer_pisos(estado_boton, "Mayor", contador, 0, LEDVERDE, LEDROJO);
      } else {
		mostrar_posicion_montacargas("No puede bajar",contador, LEDROJO, LEDVERDE);
        delay(300);
      }
    }
  } else if(estado_boton == HIGH && paro == 1){
    
    mostrar_posicion_montacargas("\nMontacargas detenido",contador, LEDROJO, LEDVERDE);
    paro = 0;
  }
}
~~~
## Links
-[ThinkerCad](https://www.tinkercad.com/things/4338Omh0dDd)
