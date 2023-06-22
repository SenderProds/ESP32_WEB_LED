# ESP32_WEB_LED


````
#include <WiFi.h>
#include <ESPAsyncWebSrv.h>

// Cambia estos valores con la información de tu red Wi-Fi
const char* ssid = "NOMBRE_WIFI";
const char* password = "CLAVE_WIFI";

// Pin del LED
const int ledPin = 26;


// Crea una instancia del servidor web
AsyncWebServer server(80);

void setup() {
  Serial.begin(115200);


  // Inicializa el pin del LED como salida
  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, LOW);

  // Conéctate a la red Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Conectando a Wi-Fi...");
  }
  Serial.println("Conexión exitosa");

  // Imprime la dirección IP asignada al Arduino
  Serial.print("Dirección IP: ");
  Serial.println(WiFi.localIP());

  // Configura la página principal
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request) {
    String htmlContent = "<html><body>";
    htmlContent += "<h1>Control de LED</h1>";
    htmlContent += "<button onclick=\"sendRequest('/on');\">Encender</button>";
    htmlContent += "<button onclick=\"sendRequest('/off');\">Apagar</button>";
    htmlContent += "<script>function sendRequest(url) {";
    htmlContent += "var xhr = new XMLHttpRequest();";
    htmlContent += "xhr.open('GET', url, true);";
    htmlContent += "xhr.send();";
    htmlContent += "}</script>";
    htmlContent += "</body></html>";
    request->send(200, "text/html", htmlContent);
  });

  // Manejador para encender el LED
  server.on("/on", HTTP_GET, [](AsyncWebServerRequest *request) {
    digitalWrite(ledPin, HIGH);
    request->send(200, "text/plain", "LED encendido");
  });

  // Manejador para apagar el LED
  server.on("/off", HTTP_GET, [](AsyncWebServerRequest *request) {
    digitalWrite(ledPin, LOW);
    request->send(200, "text/plain", "LED apagado");
  });

  // Inicia el servidor
  server.begin();
}

void loop() {
  
}
````
