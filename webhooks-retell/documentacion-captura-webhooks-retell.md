
### **Documentación del Flujo de Trabajo de Webhooks**

#### **1. Recepción del Webhook**
- **Endpoint**: Las solicitudes POST se dirigen a `/api/retell-webhook`, definido en `routes/api.php`.
  ```php
  Route::post('/retell-webhook', WebhookController::class)
      ->middleware([VerifyRetellSignatureMiddleware::class]);
  ```
- **Middleware**: `VerifyRetellSignatureMiddleware` valida la firma HMAC-SHA256 usando la API Key de Retell.AI.
  - **Archivo**: `app/Http/Middleware/VerifyRetellSignatureMiddleware.php`.
  - **Función**:  
    ```php
    public function handle(Request $request, Closure $next) {
        $signature = $request->header('x-retell-signature');
        $payload = $request->getContent();
        $apiKey = config('services.retell.api_key');
        // Verifica la firma
        if (!RetellWebhookVerifier::verify($payload, $apiKey, $signature)) {
            abort(401, 'Unauthorized');
        }
        return $next($request);
    }
    ```

---

#### **2. Enrutamiento al Controlador**
- **Controlador**: `WebhookController` procesa el payload del webhook.
  - **Archivo**: `app/Http/Controllers/WebhookController.php`.
  - **Función**:
    ```php
    public function __invoke(Request $request) {
        $payload = $request->json()->all();
        $eventType = $payload['event'];
        $eventData = $payload['call'];
        // Selecciona el handler correspondiente
        $handler = EventHandlerFactory::create($eventType);
        $handler->handle($eventData);
        return response()->noContent();
    }
    ```

---

#### **3. Creación del Handler Específico**
- **Factory**: `EventHandlerFactory` determina el handler basado en el tipo de evento.
  - **Archivo**: `app/Factories/EventHandlerFactory.php`.
  - **Función**:
    ```php
    public static function create(string $eventType) {
        return match ($eventType) {
            'call_started' => new CallStartedHandler(),
            'call_ended' => new CallEndedHandler(),
            'call_analyzed' => new CallAnalyzedHandler(),
            default => throw new InvalidArgumentException("Evento no soportado"),
        };
    }
    ```

---

#### **4. Procesamiento del Evento**
- **Handler**: Ejemplo para `call_started` (`CallStartedHandler`).
  - **Archivo**: `app/Handlers/CallStartedHandler.php`.
  - **Función**:
    ```php
    public function handle(array $data): void {
        $dto = CallStartedData::from($data);
        ProcessCallStarted::dispatch($dto);
    }
    ```
- **DTO**: `CallStartedData` estructura los datos del evento.
  - **Archivo**: `app/DTO/CallStartedData.php`.
  - **Definición**:
    ```php
    class CallStartedData extends Data {
        public function __construct(
            public string $call_id,
            public string $agent_id,
            // ... otros campos ...
        ) {}
    }
    ```

---

#### **5. Ejecución Asincrónica del Job**
- **Job**: `ProcessCallStarted` maneja el procesamiento en segundo plano.
  - **Archivo**: `app/Jobs/ProcessCallStarted.php`.
  - **Función**:
    ```php
    public function handle() {
        logger()->info("Call started: {$this->data->call_id}");
        // Lógica adicional (ej: guardar en base de datos)
    }
    ```
- **Colas**: Requiere un worker activo para procesar jobs:
  ```bash
  php artisan queue:work
  ```

---

#### **6. Almacenamiento de Logs**
- **Registro**: Los logs se escriben en `storage/logs/laravel.log`.
  - **Ejemplo**:
    ```log
    [2025-02-24 12:00:00] local.INFO: Call started: call_2951aa9b8d1e6d1e7b0fd882c35
    ```

---

### **Otros**
1. **Consistencia entre Eventos**:  
   Los eventos `call_ended` y `call_analyzed` siguen el mismo flujo, utilizando sus respectivos handlers (`CallEndedHandler`, `CallAnalyzedHandler`), DTOs (`CallEndedData`, `CallAnalyzedData`), y jobs (`ProcessCallEnded`, `ProcessCallAnalyzed`).

2. **Escalabilidad**:  
   La estructura basada en Factory y Jobs permite agregar nuevos eventos sin modificar el código existente.

3. **Seguridad**:  
   La verificación de firma garantiza que solo los webhooks auténticos de Retell.AI sean procesados.

---

### **Flujo**
```
Webhook → Middleware (Firma) → Controlador → Factory → Handler → DTO → Job → Logs/DB
```
