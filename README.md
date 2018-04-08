# ESP8266 � SPIFFS + JSON
Todos los ESP8266 tienen un �disco r�gido� interno, llamado SPIFFS (SPI Flash File System), en el cual podemos leer y grabar archivos desde el c�digo de Arduino o de forma manual. Justamente en este tutorial de IOT (internet de las cosas) vamos a ver c�mo acceder al SPIFFS parar leer y guardar datos en un archivo Json (JavaScript Object Notation).
First attempt at a library. Lots more changes and fixes to do. Contributions are welcome.

## Video
ver este video: https://youtu.be/qRiVBgZENwY


### Codigo de ejemplo
- sketch
```cpp
// Ejemplo: almacenamiento del archivo de configuraci�n JSON en el sistema de archivos flash
//
// Utiliza la biblioteca ArduinoJson de Benoit Blanchon.
// https://github.com/bblanchon/ArduinoJson
//
// Este c�digo de ejemplo est� en el dominio p�blico.

#include <ArduinoJson.h>
#include "FS.h"

bool loadConfig() {
  File configFile = SPIFFS.open("/config.json", "r");
  if (!configFile) {
    Serial.println("Failed to open config file");
    return false;
  }

  size_t size = configFile.size();
  if (size > 1024) {
    Serial.println("Config file size is too large");
    return false;
  }

  // Asigne un buffer para almacenar el contenido del archivo.
  std::unique_ptr<char[]> buf(new char[size]);

   // No usamos String aqu� porque la biblioteca ArduinoJson requiere la entrada
�� // buffer para ser mutable Si no usas ArduinoJson, tambi�n puedes
�� // use configFile.readString en su lugar.
  configFile.readBytes(buf.get(), size);

  StaticJsonBuffer<200> jsonBuffer;
  JsonObject& json = jsonBuffer.parseObject(buf.get());

  if (!json.success()) {
    Serial.println("Error al analizar el archivo de configuraci�n");
    return false;
  }

  const char* serverName = json["serverName"];
  const char* accessToken = json["accessToken"];

   // La aplicaci�n del mundo real almacenar�a estos valores en algunas variables para
�� // uso posterior.

  Serial.print("Loaded serverName: ");
  Serial.println(serverName);
  Serial.print("Loaded accessToken: ");
  Serial.println(accessToken);
  return true;
}

bool saveConfig() {
  StaticJsonBuffer<200> jsonBuffer;
  JsonObject& json = jsonBuffer.createObject();
  json["serverName"] = "api.example.com";
  json["accessToken"] = "128du9as8du12eoue8da98h123ueh9h98";

  File configFile = SPIFFS.open("/config.json", "w");
  if (!configFile) {
    Serial.println("Error al abrir el archivo de configuraci�n para escribir");
    return false;
  }

  json.printTo(configFile);
  return true;
}

void setup() {
  Serial.begin(115200);
  Serial.println("");
  delay(1000);
  Serial.println("Montaje FS...");

  if (!SPIFFS.begin()) {
    Serial.println("Error al montar el sistema de archivos");
    return;
  }

  // envia la configuracion a la memoria flash. descomentar si es necesario.
  //if (!saveConfig()) {
  //  Serial.println("Error al guardar la configuraci�n");
  //} else {
  //  Serial.println("Configuracion guardada");
  //}

  if (!loadConfig()) {
    Serial.println("Error al cargar la configuraci�n");
  } else {
    Serial.println("Configuraci�n cargada");
  }
}

void loop() {
}
```

- links para descarga de archivos
```cpp
https://github.com/esp8266/arduino-esp8266fs-plugin

http://esp8266.github.io/Arduino/versions/2.0.0/doc/filesystem.html
```
