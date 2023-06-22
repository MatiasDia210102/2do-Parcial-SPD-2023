# 2do-Parcial-SPD-2023
# Proyecto
<img src="https://github.com/MatiasDia210102/2do-Parcial-SPD-2023/blob/313e4837d546282e89042f8b7b6a8c4b53406451/Imagenes/2do%20Parcial%20SPD%202023.PNG?raw=true" width="800"/>

# Esquema
<img src="https://github.com/MatiasDia210102/2do-Parcial-SPD-2023/blob/313e4837d546282e89042f8b7b6a8c4b53406451/Imagenes/Esquema_SPD.PNG?raw=true" width="800"/>

# Descripcion Programa
Detectar cambios de temperatura y activar un servomotor en caso de detectar un incendio

# Importes
Importo las bibliotecas para usar los componentes
~~~C
// C++ code
// Matias Diaz DIV J TT
#include <LiquidCrystal.h>
#include <IRremote.h>
#include <Servo.h>
~~~

# Defines
Agrego los componentes a los pines del arduino
~~~C 
#define D7 13
#define D6 12
#define D5 11
#define D4 10
#define RS 8
#define E A1
#define SENSORTEMP A0
#define LEDVERDE 4
#define LEDROJO 3 
#define IR 2
~~~

# Prototipos
Declaro cada prototipo de cada funcion
~~~C 
void rotar_servo(int numero);
void encender_o_apagar_led(int led, int led2, int high_low);
void escribir_en_lcd(LiquidCrystal lcd, int columna, int fila, String texto, String contenido);
void iniciar_sistema(LiquidCrystal lcd, char* estacion, int temperatura);
void determinar_estacion(int temperatura, LiquidCrystal lcd);
void encender_apagar_sistema(int estado_boton, LiquidCrystal lcd);
~~~

# Variables Globales
Declaro las variables globales que usare en el programa
~~~C 
int estado_boton = 0;
IRrecv sensor_ir(IR);
LiquidCrystal lcd(RS,E,D4,D5,D6,D7);
Servo myServo;
~~~
# Setup
Inicializo cada componente para usarlos
~~~C 
void setup()
{
  myServo.attach(9, 500, 2500);
  pinMode(LEDVERDE, OUTPUT);
  pinMode(LEDROJO, OUTPUT);
  sensor_ir.begin(IR);
  lcd.begin(16,2);
}
~~~

# Principal
Cambio el estado de boton para encender o apagar el programa
~~~C 
void loop()
{
   if(sensor_ir.decode() == 1){
    if(sensor_ir.decodedIRData.command == 0){
      
      estado_boton = !estado_boton;
      delay(200);
    }
    sensor_ir.resume(); 
  }
  activar_desactivar_sistema(estado_boton, lcd);
}
~~~
# Funciones
## rotar_servo
Gira el servo segun el numero recibido
~~~C 
void rotar_servo(int numero){
  myServo.write(numero);
  delay(200);
}
~~~

## encender_o_apagar_led
Enciende o apaga el primer led recibido y apaga el segundo
~~~C 
void encender_o_apagar_led(int led, int led2, int high_low){
  
  digitalWrite(led, high_low);
  digitalWrite(led2, LOW);
}
~~~

## escribir_en_lcd
Escribe un mensaje en el la pantalla en la posicion recibida
~~~C
void escribir_en_lcd(LiquidCrystal lcd, int columna, int fila, String texto, String contenido){
  
  lcd.print("          ");
  lcd.setCursor(columna, fila);
  lcd.print(texto);
  lcd.print(contenido);
}
~~~

## iniciar_sistema
Inicia el sistema de incendios
~~~C
void iniciar_sistema(LiquidCrystal lcd, char* estacion, int temperatura){
  
  char contenido[10] = " ";
  sprintf(contenido, "%d", temperatura);
  
  rotar_servo(90);
  encender_o_apagar_led(LEDVERDE, LEDROJO, HIGH);
  escribir_en_lcd(lcd, 0, 0, "Temperatura: ", contenido);
  escribir_en_lcd(lcd, 0, 1, estacion, "");
}
~~~
## determinar_estacion
Determina la estacion del año segun el rango de temperatura
~~~C
void determinar_estacion(int temperatura, LiquidCrystal lcd){
  
  if(temperatura >= 24  && temperatura <= 40){
    
    escribir_rotar_por_estacion(lcd, "Verano", temperatura);
  } else if(temperatura >= 16 && temperatura <= 23){
    
    escribir_rotar_por_estacion(lcd, "Primavera", temperatura);
  } else if(temperatura >= 11 && temperatura <= 15){
    
    escribir_rotar_por_estacion(lcd, "Otonio", temperatura);
  } else if(temperatura >= 2 && temperatura <= 10){
    
    escribir_rotar_por_estacion(lcd, "Invierno", temperatura);
  } else {
    
    rotar_servo(0);
    escribir_en_lcd(lcd, 0, 0, " !!!PELIGRO!!!", "");
    escribir_en_lcd(lcd, 0, 1, "!HUMO DETECTADO!", "");
    encender_o_apagar_led(LEDROJO, LEDVERDE, HIGH);
  }
}
~~~
## encender_apagar_sistema
Enciende o apaga el sistema de incendios segun el estado del boton
~~~C
void encender_apagar_sistema(int estado_boton, LiquidCrystal lcd){
  
  int lectura = analogRead(SENSORTEMP);
  int temperatura = map(lectura, 20 , 358, 2 , 65);
  
  if(estado_boton == 1){

    determinar_estacion(temperatura, lcd);
  } else {
    
    encender_o_apagar_led(LEDROJO, LEDVERDE, LOW);
    rotar_servo(0);
    lcd.clear();
  }
}
~~~
## Links
-[ThinkerCad](https://www.tinkercad.com/things/gDc8l5pkKPX?sharecode=n40Y2fHl80ieNYe6nDtJyMAy1xl3gTsb8hnIfr_DyMs)
