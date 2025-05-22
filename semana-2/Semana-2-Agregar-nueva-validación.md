
Para implementar la lógica que permite que solo el **creador de una oferta de empleo** pueda editarla o eliminarla, es necesario ajustar varios archivos, incluyendo los controladores, los archivos de validación (Request Forms), y tal vez el middleware o las políticas de autorización.

Haremos lo siguiente:

### **1. Agregar una Relación en el Modelo `Job`**

Primero, asegurarse de que el modelo `Job` tenga una relación con el usuario que lo creó (creador).

#### **Modificar el modelo `Job` para incluir la relación:**

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use App\Models\User;

class Job extends Model
{
    use HasFactory;

    protected $table = 'job_listings';

    protected $fillable = ['title', 'salary', 'employer_id', 'user_id']; // Asegúrate de incluir user_id

    // Relación con el empleador
    public function employer()
    {
        return $this->belongsTo(Employer::class);
    }

    // Relación con las etiquetas
    public function tags()
    {
        return $this->belongsToMany(Tag::class, 'job_tag', 'job_listing_id', 'tag_id');
    }

    // Nueva relación con el usuario creador
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

### **2. Modificar las Migraciones**

Si no lo has hecho ya, la tabla `job_listings` debe tener una columna `user_id` para registrar el usuario creador de cada oferta.

#### **Modificar la migración de `job_listings` para agregar `user_id`:**

```php
Schema::create('job_listings', function (Blueprint $table) {
    $table->id();
    $table->foreignId('employer_id')->constrained()->cascadeOnDelete();
    $table->string('title');
    $table->string('salary');
    $table->foreignId('user_id')->constrained()->cascadeOnDelete(); // Agregar user_id
    $table->timestamps();
});
```

**Nota:** Si ya tienes registros en la tabla, puedes crear una **nueva migración** para agregar la columna `user_id`.

#### **Crear una migración para agregar la columna `user_id` si ya existe la tabla:**

```bash
php artisan make:migration add_user_id_to_job_listings_table --table=job_listings
```

Luego, en la migración generada, agrega:

```php
public function up(): void  
{  
    Schema::table('job_listings', function (Blueprint $table) {  
        $table->foreignIdFor(\App\Models\User::class)->after('employer_id')->constrained()->cascadeOnDelete();  
    });  
}  
  
/**  
 * Reverse the migrations. */public function down(): void  
{  
    Schema::table('job_listings', function (Blueprint $table) {  
        // Primero elimina la clave foránea  
        $table->dropForeign(['user_id']);  
  
        // Luego elimina la columna user_id  
        $table->dropColumn('user_id');  
    });  
}
});
```

Ejecuta las migraciones:

```bash
php artisan migrate
```

### **3. Ajustar el `StoreJobRequest` y `UpdateJobRequest`**

En el **StoreJobRequest**, asegúrate de que el campo `user_id` sea asignado al usuario autenticado.

#### **Modifica el `StoreJobRequest` para asignar `user_id`:**

```php
use Illuminate\Auth\Access\AuthorizationException;

class StoreJobRequest extends FormRequest
{
    public function authorize(): bool
    {
        return auth()->check();
    }

    public function rules(): array
    {
        return [
            'title' => 'required|string|max:150',
            'salary' => 'required|string|max:25',
            'employer_id' => 'required|integer|exists:employers,id',
            'tags' => 'nullable|array',
            'tags.*' => 'integer|exists:tags,id',
        ];
    }

    public function messages(): array
    {
        return [
            'title.required' => 'El título es obligatorio.',
            'salary.required' => 'El salario es obligatorio.',
            'employer_id.required' => 'El empleador es obligatorio.',
            'employer_id.exists' => 'El empleador seleccionado no existe.',
            'tags.array' => 'Las etiquetas deben ser un arreglo.',
            'tags.*.exists' => 'Alguna de las etiquetas no existe.',
        ];
    }

    protected function prepareForValidation()
    {
        $this->merge([
            'user_id' => auth()->id(),
        ]);
    }

    protected function failedAuthorization()
    {
        throw new AuthorizationException('No estás autorizado para realizar esta acción.');
    }
}
```

### **4. Modificar el `JobController` para Editar y Eliminar solo si es el Creador**

Debes ajustar el controlador para verificar que el usuario autenticado sea el creador del trabajo antes de permitir la edición o eliminación.

#### **Modifica el `update` y el `destroy` en el `JobController`:**

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use App\Models\Job;
use App\Http\Requests\UpdateJobRequest;
use App\Http\Requests\StoreJobRequest;

class JobController extends Controller
{
    public function update(UpdateJobRequest $request, Job $job)
    {
        // Verificar si el usuario autenticado es el creador del job
        if (Auth::id() !== $job->user_id) {
            return response()->json(['message' => 'No estás autorizado para actualizar esta oferta.'], 403);
        }

        $job->update($request->validated());

        if ($request->has('tags')) {
            $job->tags()->sync($request->input('tags'));
        }

        return response()->json(['message' => 'Oferta actualizada correctamente.', 'job' => $job], 200);
    }

    public function destroy(Job $job)
    {
        // Verificar si el usuario autenticado es el creador del job
        if (Auth::id() !== $job->user_id) {
            return response()->json(['message' => 'No estás autorizado para eliminar esta oferta.'], 403);
        }

        $job->tags()->detach(); // Desasociar las etiquetas antes de eliminar
        $job->delete();

        return response()->json(['message' => 'Oferta eliminada correctamente.'], 200);
    }
}
```

### **5. Ajustar el `UpdateJobRequest` para Verificar el Creador**

En el archivo de validación `UpdateJobRequest`, asegúrate de autorizar solo si el usuario es el creador:

```php
<?php

namespace App\Http\Requests;

use Illuminate\Auth\Access\AuthorizationException;
use Illuminate\Foundation\Http\FormRequest;
use App\Models\Job;

class UpdateJobRequest extends FormRequest
{
    public function authorize(): bool
    {
        // Permitir solo si el usuario está autenticado y es el creador del job
        $job = $this->route('job'); // Obtiene la oferta desde la ruta

        return auth()->check() && auth()->id() === $job->user_id;
    }

    public function rules(): array
    {
        return [
            'title' => 'sometimes|required|string|max:150',
            'salary' => 'sometimes|required|string|max:25',
            'employer_id' => 'sometimes|required|integer|exists:employers,id',
            'tags' => 'nullable|array',
            'tags.*' => 'integer|exists:tags,id',
        ];
    }

    public function messages(): array
    {
        return [
            // Mensajes para el campo 'title'
            'title.required' => 'El título es obligatorio cuando se proporciona.',
            'title.string' => 'El título debe ser un texto válido.',
            'title.max' => 'El título no debe exceder los 150 caracteres.',

            // Mensajes para el campo 'salary'
            'salary.required' => 'El salario es obligatorio cuando se proporciona.',
            'salary.string' => 'El salario debe ser un texto válido.',
            'salary.max' => 'El salario no debe exceder los 25 caracteres.',

            // Mensajes para el campo 'employer_id'
            'employer_id.required' => 'El empleador es obligatorio cuando se proporciona.',
            'employer_id.integer' => 'El ID del empleador debe ser un número entero.',
            'employer_id.exists' => 'El empleador seleccionado no existe.',

            // Mensajes para el campo 'tags'
            'tags.array' => 'Las etiquetas deben ser un arreglo.',
            'tags.*.integer' => 'Cada etiqueta debe ser un número entero válido.',
            'tags.*.exists' => 'Alguna de las etiquetas no existe.',

            // Mensaje para el user_id (nuevo)
            'user_id' => 'No estás autorizado para actualizar esta oferta de empleo.',
        ];
    }

    protected function failedAuthorization()
    {
        throw new AuthorizationException('No estás autorizado para actualizar esta oferta de empleo.');
    }
}

```

