# Guía KISS de validaciones `.editorconfig`

Esta guía recorre **línea por línea** la configuración de `ProyectoValidations.Api/.editorconfig`.
Para cada clave se explica qué vigila, cómo luce un error típico y cuál es la solución esperada.
Los ejemplos usan C# y muestran solo el fragmento relevante para mantener todo en formato KISS.

## Encabezado global

### `root = true`
- **Qué vigila:** el repositorio no hereda configuraciones padre; todos los proyectos deben obedecer este archivo.
- **Error:** un subproyecto añade otro `.editorconfig` con valores distintos que confunden el formato.
- **Solución:** eliminar configuraciones redundantes o mover los ajustes deseados a este archivo raíz.

### `[*.cs]`
- **Qué vigila:** todas las reglas siguientes aplican a cualquier archivo con extensión `.cs`.
- **Error:** creer que solo afecta a un subdirectorio y violar reglas en otro.
- **Solución:** validar que cualquier archivo C# respete lo documentado sin excepciones.

## Sangría y finales de línea

### `indent_size = 4`
- **Qué vigila:** cada nivel de sangría usa cuatro espacios.
- **Error:**
  ```csharp
  if (ok)
  {
   Console.WriteLine("mal");
  }
  ```
- **Solución:**
  ```csharp
  if (ok)
  {
      Console.WriteLine("bien");
  }
  ```

### `indent_style = space`
- **Qué vigila:** se prohíben tabs reales.
- **Error:** la línea contiene un tab: `\tConsole.WriteLine("tab");`.
- **Solución:** reemplazar tabs por espacios según la sangría de 4.

### `tab_width = 4`
- **Qué vigila:** cuando se pulsa tab, el editor inserta cuatro espacios.
- **Error:** editor configurado a ocho espacios produce desalineación.
- **Solución:** ajustar el editor a tabulación de cuatro.

### `end_of_line = crlf`
- **Qué vigila:** todas las líneas terminan con `\r\n`.
- **Error:** archivo guardado con `\n` muestra diffs innecesarios.
- **Solución:** reconfigurar el editor o ejecutar `dotnet format` para normalizar a `CRLF`.

### `insert_final_newline = false`
- **Qué vigila:** no se agrega nueva línea automática al final.
- **Error:** guardar con salto final genera ruido en cambios.
- **Solución:** quitar la línea vacía extra final cuando no es deseada.

## Acciones de código de .NET

### `dotnet_hide_advanced_members = false`
- **Qué vigila:** el explorador de objetos muestra miembros avanzados para facilitar depuración.
- **Error:** ocultar miembros avanzados y luego no respetar convenciones al reordenar.
- **Solución:** mantener visible la lista completa y ordenar según las reglas posteriores.

### `dotnet_member_insertion_location = with_other_members_of_the_same_kind`
- **Qué vigila:** generadores automáticos insertan miembros junto a otros del mismo tipo.
- **Error:** propiedad autogenerada aparece al final del archivo rompiendo el bloque de métodos.
- **Solución:** mover la propiedad junto a otras propiedades (o usar la acción rápida que la recoloca).

### `dotnet_property_generation_behavior = prefer_throwing_properties`
- **Qué vigila:** las propiedades generadas para interfaces lanzan `NotImplementedException` por defecto.
- **Error:**
  ```csharp
  public string Nombre { get; set; }
  ```
  generado sin cuerpo en una clase que implementa interfaz.
- **Solución:**
  ```csharp
  public string Nombre => throw new NotImplementedException();
  ```
  hasta proveer implementación real.

### `dotnet_search_reference_assemblies = true`
- **Qué vigila:** los análisis consultan ensamblados de referencia para sugerencias precisas.
- **Error:** ignorar advertencias porque el IDE no resolvió miembros externos.
- **Solución:** aceptar las sugerencias basadas en API externas ya que el analizador las detectará.

## Organización de `using`

### `dotnet_separate_import_directive_groups = false`
- **Qué vigila:** evita líneas en blanco separando grupos de `using`.
- **Error:**
  ```csharp
  using System;
  
  using ProyectoValidations.Api;
  ```
- **Solución:**
  ```csharp
  using System;
  using ProyectoValidations.Api;
  ```

### `dotnet_sort_system_directives_first = false`
- **Qué vigila:** no se obliga a que `System.*` vaya primero.
- **Error:** intentar mover manualmente `using System;` arriba rompiendo orden deseado por equipos.
- **Solución:** mantener el orden existente o dejar que el IDE ordene alfabéticamente sin prioridad.

### `file_header_template = unset`
- **Qué vigila:** no se espera encabezado fijo.
- **Error:** agregar plantilla corporativa que viola la directiva al ejecutarse `dotnet format`.
- **Solución:** omitir encabezados automáticos salvo que se acuerde actualizar la configuración.

## Preferencias de `this`/`Me`

### `dotnet_style_qualification_for_event = false`
- **Qué vigila:** evita prefijar eventos con `this.`.
- **Error:** `this.Cambio += Manejar;`
- **Solución:** `Cambio += Manejar;`

### `dotnet_style_qualification_for_field = false`
- **Qué vigila:** no usar `this.` para campos.
- **Error:** `this._id = id;`
- **Solución:** `_id = id;`

### `dotnet_style_qualification_for_method = false`
- **Qué vigila:** llamadas a métodos sin `this.`.
- **Error:** `this.Calcular();`
- **Solución:** `Calcular();`

### `dotnet_style_qualification_for_property = false`
- **Qué vigila:** propiedades sin prefijo `this.`.
- **Error:** `return this.Nombre;`
- **Solución:** `return Nombre;`

## Tipos predefinidos

### `dotnet_style_predefined_type_for_locals_parameters_members = true`
- **Qué vigila:** usar `int`, `string`, etc. en declaraciones.
- **Error:** `System.Int32 total = 0;`
- **Solución:** `int total = 0;`

### `dotnet_style_predefined_type_for_member_access = true`
- **Qué vigila:** usar métodos de tipos predefinidos con alias.
- **Error:** `System.Int32.Parse(texto);`
- **Solución:** `int.Parse(texto);`

## Paréntesis

### `dotnet_style_parentheses_in_arithmetic_binary_operators = always_for_clarity`
- **Qué vigila:** mantiene paréntesis en operaciones aritméticas complejas.
- **Error:** `var total = a + b * c;`
- **Solución:** `var total = a + (b * c);`

### `dotnet_style_parentheses_in_other_binary_operators = always_for_clarity`
- **Qué vigila:** usa paréntesis en combinaciones lógicas.
- **Error:** `if (a && b || c)`
- **Solución:** `if ((a && b) || c)`

### `dotnet_style_parentheses_in_other_operators = never_if_unnecessary`
- **Qué vigila:** evita paréntesis superfluos.
- **Error:** `return (valor);`
- **Solución:** `return valor;`

### `dotnet_style_parentheses_in_relational_binary_operators = always_for_clarity`
- **Qué vigila:** agrupa comparaciones múltiples.
- **Error:** `if (a > b == condicion)`
- **Solución:** `if ((a > b) == condicion)`

## Modificadores y accesibilidad

### `dotnet_style_require_accessibility_modifiers = for_non_interface_members`
- **Qué vigila:** miembros fuera de interfaces deben indicar `public`, `private`, etc.
- **Error:**
  ```csharp
  class Cliente
  {
      string Nombre { get; set; }
  }
  ```
- **Solución:**
  ```csharp
  class Cliente
  {
      public string Nombre { get; set; }
  }
  ```

## Preferencias de nivel de expresión (.NET)

### `dotnet_prefer_system_hash_code = true`
- **Qué vigila:** usar `HashCode.Combine` para `GetHashCode`.
- **Error:**
  ```csharp
  public override int GetHashCode() => Id.GetHashCode() ^ Nombre.GetHashCode();
  ```
- **Solución:**
  ```csharp
  public override int GetHashCode() => HashCode.Combine(Id, Nombre);
  ```

### `dotnet_style_coalesce_expression = true`
- **Qué vigila:** usar `??` en lugar de `if` redundantes.
- **Error:**
  ```csharp
  string nombre = texto == null ? "" : texto;
  ```
- **Solución:** `string nombre = texto ?? "";`

### `dotnet_style_collection_initializer = true`
- **Qué vigila:** inicializadores de colección.
- **Error:**
  ```csharp
  var lista = new List<int>();
  lista.Add(1);
  lista.Add(2);
  ```
- **Solución:**
  ```csharp
  var lista = new List<int> { 1, 2 };
  ```

### `dotnet_style_explicit_tuple_names = true`
- **Qué vigila:** nombra los elementos de tuplas.
- **Error:** `return (1, "Ana");`
- **Solución:** `return (Id: 1, Nombre: "Ana");`

### `dotnet_style_namespace_match_folder = true`
- **Qué vigila:** el `namespace` coincide con la carpeta.
- **Error:** archivo en `Services/` usa `namespace Proyecto.Controllers;`
- **Solución:** `namespace Proyecto.Services;`

### `dotnet_style_null_propagation = true`
- **Qué vigila:** usa `?.` para evitar `if` redundante.
- **Error:**
  ```csharp
  if (cliente != null)
  {
      cliente.Pagar();
  }
  ```
- **Solución:** `cliente?.Pagar();`

### `dotnet_style_object_initializer = true`
- **Qué vigila:** inicializadores de objetos.
- **Error:**
  ```csharp
  var cliente = new Cliente();
  cliente.Nombre = "Ada";
  ```
- **Solución:**
  ```csharp
  var cliente = new Cliente { Nombre = "Ada" };
  ```

### `dotnet_style_operator_placement_when_wrapping = beginning_of_line`
- **Qué vigila:** cuando se rompe línea, el operador queda al inicio siguiente.
- **Error:**
  ```csharp
  var suma = a +
      b;
  ```
- **Solución:**
  ```csharp
  var suma = a
      + b;
  ```

### `dotnet_style_prefer_auto_properties = true`
- **Qué vigila:** preferir propiedades automáticas en vez de campos expuestos.
- **Error:**
  ```csharp
  public int Id;
  ```
- **Solución:**
  ```csharp
  public int Id { get; set; }
  ```

### `dotnet_style_prefer_collection_expression = when_types_loosely_match`
- **Qué vigila:** usar `[ ... ]` en colecciones compatibles.
- **Error:** `var lista = new List<int> { 1, 2 };`
- **Solución:** `List<int> lista = [1, 2];` cuando el tipo lo permite.

### `dotnet_style_prefer_compound_assignment = true`
- **Qué vigila:** usar `+=`, `-=`, etc.
- **Error:** `total = total + 5;`
- **Solución:** `total += 5;`

### `dotnet_style_prefer_conditional_expression_over_assignment = true`
- **Qué vigila:** usar operador ternario en asignaciones simples.
- **Error:**
  ```csharp
  string texto;
  if (ok)
  {
      texto = "sí";
  }
  else
  {
      texto = "no";
  }
  ```
- **Solución:** `string texto = ok ? "sí" : "no";`

### `dotnet_style_prefer_conditional_expression_over_return = true`
- **Qué vigila:** usar ternario en `return` sencillos.
- **Error:**
  ```csharp
  if (ok)
  {
      return "sí";
  }
  return "no";
  ```
- **Solución:** `return ok ? "sí" : "no";`

### `dotnet_style_prefer_foreach_explicit_cast_in_source = when_strongly_typed`
- **Qué vigila:** aplicar cast en la declaración del `foreach` cuando es necesario.
- **Error:**
  ```csharp
  foreach (var item in objetos)
  {
      var cliente = (Cliente)item;
  }
  ```
- **Solución:**
  ```csharp
  foreach (Cliente cliente in objetos)
  {
  }
  ```

### `dotnet_style_prefer_inferred_anonymous_type_member_names = true`
- **Qué vigila:** evitar repetir nombres al crear tipos anónimos.
- **Error:** `var dato = new { Nombre = nombre };`
- **Solución:** `var dato = new { nombre };`

### `dotnet_style_prefer_inferred_tuple_names = true`
- **Qué vigila:** evita renombrar elementos que ya tienen nombre.
- **Error:** `var tupla = (Id: id, Nombre: nombre);`
- **Solución:** `var tupla = (id, nombre);`

### `dotnet_style_prefer_is_null_check_over_reference_equality_method = true`
- **Qué vigila:** usar `is null` en vez de `ReferenceEquals`.
- **Error:** `if (ReferenceEquals(obj, null))`
- **Solución:** `if (obj is null)`

### `dotnet_style_prefer_simplified_boolean_expressions = true`
- **Qué vigila:** simplificar condiciones booleanas.
- **Error:** `return condicion ? true : false;`
- **Solución:** `return condicion;`

### `dotnet_style_prefer_simplified_interpolation = true`
- **Qué vigila:** evitar interpolaciones redundantes.
- **Error:** `$"{valor.ToString()}"`
- **Solución:** `$"{valor}"`

## Campos, parámetros y supresiones

### `dotnet_style_readonly_field = true`
- **Qué vigila:** marca campos como `readonly` si no cambian.
- **Error:** `private int _limite = 10;` jamás modificado.
- **Solución:** `private readonly int _limite = 10;`

### `dotnet_code_quality_unused_parameters = all`
- **Qué vigila:** parámetros sin usar generan advertencia.
- **Error:**
  ```csharp
  public void Procesar(int id)
  {
      Console.WriteLine("hola");
  }
  ```
- **Solución:** usar el parámetro o renombrarlo `_` para indicar descarte.

### `dotnet_remove_unnecessary_suppression_exclusions = none`
- **Qué vigila:** ninguna regla se excluye de la limpieza de supresiones.
- **Error:** dejar `#pragma warning disable` innecesario.
- **Solución:** eliminar las supresiones cuando el código ya cumple la regla.

### `dotnet_style_allow_multiple_blank_lines_experimental = true`
- **Qué vigila:** se permiten múltiples líneas en blanco.
- **Error:**
  ```csharp
  Console.WriteLine("uno");
  Console.WriteLine("dos");
  ```
  se compacta para eliminar la separación deseada.
- **Solución:**
  ```csharp
  Console.WriteLine("uno");


  Console.WriteLine("dos");
  ```
  mantener o limpiar según necesidad; no habrá diagnóstico por varias líneas vacías.

### `dotnet_style_allow_statement_immediately_after_block_experimental = true`
- **Qué vigila:** permite colocar una instrucción inmediatamente tras un bloque sin línea en blanco.
- **Error:**
  ```csharp
  if (ok)
  {
      Ejecutar();
  }

  Registrar();
  ```
  se obliga a insertar una línea en blanco.
- **Solución:**
  ```csharp
  if (ok)
  {
      Ejecutar();
  }
  Registrar();
  ```
  aceptar que la siguiente instrucción vaya pegada al bloque previo.

## Preferencias `var`

### `csharp_style_var_elsewhere = false`
- **Qué vigila:** fuera de tipos intrínsecos o evidentes se exige tipo explícito.
- **Error:** `var servicio = ObtenerServicio();`
- **Solución:** `IServicio servicio = ObtenerServicio();`

### `csharp_style_var_for_built_in_types = false`
- **Qué vigila:** incluso con tipos primitivos se pide tipo explícito.
- **Error:** `var contador = 0;`
- **Solución:** `int contador = 0;`

### `csharp_style_var_when_type_is_apparent = false`
- **Qué vigila:** aunque el constructor muestre el tipo se debe escribirlo.
- **Error:** `var pedido = new Pedido();`
- **Solución:** `Pedido pedido = new Pedido();`

## Miembros de cuerpo de expresión

### `csharp_style_expression_bodied_accessors = true:silent`
- **Qué vigila:** permite `=>` en getters/setters simples.
- **Error:**
  ```csharp
  public int Id
  {
      get { return _id; }
  }
  ```
- **Solución:**
  ```csharp
  public int Id => _id;
  ```

### `csharp_style_expression_bodied_constructors = false:silent`
- **Qué vigila:** evita constructores con `=>`.
- **Error:** `public Cliente() => Inicializar();`
- **Solución:**
  ```csharp
  public Cliente()
  {
      Inicializar();
  }
  ```

### `csharp_style_expression_bodied_indexers = true:silent`
- **Qué vigila:** permite indexadores con `=>`.
- **Error:**
  ```csharp
  public string this[int index]
  {
      get { return _datos[index]; }
  }
  ```
- **Solución:** `public string this[int index] => _datos[index];`

### `csharp_style_expression_bodied_lambdas = true:silent`
- **Qué vigila:** favorece lambdas de expresión.
- **Error:** `Func<int, int> doble = x => { return x * 2; };`
- **Solución:** `Func<int, int> doble = x => x * 2;`

### `csharp_style_expression_bodied_local_functions = false:silent`
- **Qué vigila:** funciones locales deben usar bloque.
- **Error:**
  ```csharp
  int Sumar(int a, int b) => a + b;
  ```
- **Solución:**
  ```csharp
  int Sumar(int a, int b)
  {
      return a + b;
  }
  ```

### `csharp_style_expression_bodied_methods = false:silent`
- **Qué vigila:** métodos normales usan bloque.
- **Error:** `public int Sumar(int a, int b) => a + b;`
- **Solución:** usar bloque con `return`.

### `csharp_style_expression_bodied_operators = false:silent`
- **Qué vigila:** operadores sobrecargados usan bloque tradicional.
- **Error:** `public static Vector operator +(Vector a, Vector b) => ...;`
- **Solución:** implementar usando llaves y `return`.

### `csharp_style_expression_bodied_properties = true:silent`
- **Qué vigila:** propiedades calculadas pueden usar `=>`.
- **Error:**
  ```csharp
  public string NombreCompleto
  {
      get { return $"{Nombre} {Apellido}"; }
  }
  ```
- **Solución:** `public string NombreCompleto => $"{Nombre} {Apellido}";`

## Coincidencia de patrones

### `csharp_style_pattern_matching_over_as_with_null_check = true`
- **Qué vigila:** usar patrón `is` con declaración.
- **Error:**
  ```csharp
  var cliente = objeto as Cliente;
  if (cliente != null)
  {
      Procesar(cliente);
  }
  ```
- **Solución:**
  ```csharp
  if (objeto is Cliente cliente)
  {
      Procesar(cliente);
  }
  ```

### `csharp_style_pattern_matching_over_is_with_cast_check = true`
- **Qué vigila:** reemplaza doble comprobación `is` + cast.
- **Error:**
  ```csharp
  if (objeto is Cliente)
  {
      var cliente = (Cliente)objeto;
  }
  ```
- **Solución:** usar `if (objeto is Cliente cliente)`.

### `csharp_style_prefer_extended_property_pattern = true`
- **Qué vigila:** usar patrones extendidos en coincidencias.
- **Error:** `if (persona is { Direccion: var direccion })` sin detallar subpropiedades.
- **Solución:** `if (persona is { Direccion.Ciudad: "Madrid" })`
  cuando aplica.

### `csharp_style_prefer_not_pattern = true`
- **Qué vigila:** usar `is not` en lugar de negaciones complejas.
- **Error:** `if (!(obj is Cliente))`
- **Solución:** `if (obj is not Cliente)`

### `csharp_style_prefer_pattern_matching = true`
- **Qué vigila:** generaliza preferencia por patrones.
- **Error:** usar `switch` tradicional con `case` y casts manuales.
- **Solución:** usar patrones con `switch` moderno o `is`.

### `csharp_style_prefer_switch_expression = true`
- **Qué vigila:** favorece `switch` expresión.
- **Error:**
  ```csharp
  switch (estado)
  {
      case Estado.Activo:
          resultado = 1;
          break;
      default:
          resultado = 0;
          break;
  }
  ```
- **Solución:** `int resultado = estado switch { Estado.Activo => 1, _ => 0 };`

### `csharp_style_conditional_delegate_call = true`
- **Qué vigila:** usa `?.Invoke` en eventos.
- **Error:**
  ```csharp
  if (Evento != null)
  {
      Evento(this, EventArgs.Empty);
  }
  ```
- **Solución:** `Evento?.Invoke(this, EventArgs.Empty);`

## Modificadores C#

### `csharp_prefer_static_anonymous_function = true`
- **Qué vigila:** declarar lambdas `static` cuando no capturan estado.
- **Error:** `Func<int, int> doble = x => x * 2;`
- **Solución:** `Func<int, int> doble = static x => x * 2;`

### `csharp_prefer_static_local_function = true`
- **Qué vigila:** funciones locales `static` si no capturan.
- **Error:**
  ```csharp
  int Sumar(int a, int b)
  {
      int Local(int x, int y) => x + y;
      return Local(a, b);
  }
  ```
- **Solución:** declarar `static int Local(int x, int y) => x + y;`

### `csharp_preferred_modifier_order = public,private,protected,internal,file,static,extern,new,virtual,abstract,sealed,override,readonly,unsafe,required,volatile,async`
- **Qué vigila:** orden específico de modificadores.
- **Error:** `static public class Util { }`
- **Solución:** `public static class Util { }`

### `csharp_style_prefer_readonly_struct = true`
- **Qué vigila:** estructuras inmutables marcadas `readonly`.
- **Error:**
  ```csharp
  public struct Punto
  {
      public int X { get; }
      public int Y { get; }
  }
  ```
- **Solución:** `public readonly struct Punto { public int X { get; } public int Y { get; } }`

### `csharp_style_prefer_readonly_struct_member = true`
- **Qué vigila:** miembros de struct inmutable deben ser `readonly`.
- **Error:**
  ```csharp
  public readonly struct Punto
  {
      public int Magnitud()
      {
          return (int)Math.Sqrt(X * X + Y * Y);
      }
  }
  ```
  (sin `readonly` en método).
- **Solución:** `public readonly int Magnitud() => ...;`

## Bloques y nivel de expresión C#

### `csharp_prefer_braces = true`
- **Qué vigila:** siempre usar llaves en estructuras de control.
- **Error:** `if (ok) Console.WriteLine("hola");`
- **Solución:** envolver en llaves.

### `csharp_prefer_simple_using_statement = true:suggestion`
- **Qué vigila:** usar `using var` cuando sea simple.
- **Error:**
  ```csharp
  using (var recurso = Crear())
  {
      recurso.Hacer();
  }
  ```
- **Solución:**
  ```csharp
  using var recurso = Crear();
  recurso.Hacer();
  ```

### `csharp_prefer_system_threading_lock = true`
- **Qué vigila:** usar `lock` de C# sobre `Monitor` manual.
- **Error:**
  ```csharp
  Monitor.Enter(obj);
  try
  {
      Trabajo();
  }
  finally
  {
      Monitor.Exit(obj);
  }
  ```
- **Solución:** `lock (obj) { Trabajo(); }`

### `csharp_style_namespace_declarations = block_scoped`
- **Qué vigila:** namespaces con llaves (`block-scoped`).
- **Error:** `namespace Proyecto;`
- **Solución:**
  ```csharp
  namespace Proyecto
  {
  }
  ```

### `csharp_style_prefer_method_group_conversion = true`
- **Qué vigila:** usar grupos de métodos en lugar de lambdas triviales.
- **Error:** `lista.ForEach(x => Procesar(x));`
- **Solución:** `lista.ForEach(Procesar);`

### `csharp_style_prefer_primary_constructors = true`
- **Qué vigila:** usar constructores primarios cuando sea viable.
- **Error:**
  ```csharp
  class Pedido
  {
      public Pedido(int id)
      {
          Id = id;
      }
      public int Id { get; }
  }
  ```
- **Solución:** `class Pedido(int id) { public int Id { get; } = id; }`

### `csharp_style_prefer_top_level_statements = true`
- **Qué vigila:** aplicaciones simples usan instrucciones de nivel superior.
- **Error:** crear `Program` con `Main` innecesario en ejemplos.
- **Solución:** usar top-level statements cuando el proyecto lo permite.

### `csharp_prefer_simple_default_expression = true`
- **Qué vigila:** usar `default` en vez de tipo explícito.
- **Error:** `return default(string);`
- **Solución:** `return default;`

### `csharp_style_deconstructed_variable_declaration = true`
- **Qué vigila:** usar deconstrucción en tuplas.
- **Error:**
  ```csharp
  var tupla = Obtener();
  var a = tupla.Item1;
  var b = tupla.Item2;
  ```
- **Solución:** `var (a, b) = Obtener();`

### `csharp_style_implicit_object_creation_when_type_is_apparent = true`
- **Qué vigila:** usar `new()` cuando el tipo es evidente.
- **Error:** `var lista = new List<int>();`
- **Solución:** `List<int> lista = new();`

### `csharp_style_inlined_variable_declaration = true`
- **Qué vigila:** declarar variables directamente en `out`.
- **Error:**
  ```csharp
  int valor;
  if (int.TryParse(texto, out valor))
  {
  }
  ```
- **Solución:** `if (int.TryParse(texto, out var valor))`

### `csharp_style_prefer_implicitly_typed_lambda_expression = true`
- **Qué vigila:** parámetros de lambda sin tipo explícito.
- **Error:** `Func<int, int> doble = (int x) => x * 2;`
- **Solución:** `Func<int, int> doble = x => x * 2;`

### `csharp_style_prefer_index_operator = true`
- **Qué vigila:** usar el operador `^` y `..` para índices.
- **Error:** `var ultimo = lista[lista.Count - 1];`
- **Solución:** `var ultimo = lista[^1];`

### `csharp_style_prefer_local_over_anonymous_function = true`
- **Qué vigila:** preferir función local nombrada.
- **Error:** crear `delegate` anónimo complejo.
- **Solución:** mover la lógica a una función local con nombre.

### `csharp_style_prefer_null_check_over_type_check = true`
- **Qué vigila:** comprobar null directamente.
- **Error:** `if (!(obj is Cliente)) return;`
- **Solución:** `if (obj is null) return;`

### `csharp_style_prefer_range_operator = true`
- **Qué vigila:** usar `..` para rangos.
- **Error:** `var sub = texto.Substring(0, 5);`
- **Solución:** `var sub = texto[..5];`

### `csharp_style_prefer_tuple_swap = true`
- **Qué vigila:** usar `(a, b) = (b, a);` para intercambios.
- **Error:**
  ```csharp
  var temp = a;
  a = b;
  b = temp;
  ```
- **Solución:** `(a, b) = (b, a);`

### `csharp_style_prefer_unbound_generic_type_in_nameof = true`
- **Qué vigila:** usar `nameof(List<>)` sin argumentos.
- **Error:** `nameof(List<int>)`
- **Solución:** `nameof(List<>)`

### `csharp_style_prefer_utf8_string_literals = true`
- **Qué vigila:** usar `"texto"u8` cuando se requieran `ReadOnlySpan<byte>`.
- **Error:** `ReadOnlySpan<byte> bytes = Encoding.UTF8.GetBytes("hola");`
- **Solución:** `ReadOnlySpan<byte> bytes = "hola"u8;`

### `csharp_style_throw_expression = true`
- **Qué vigila:** usar `throw` expresivo.
- **Error:**
  ```csharp
  if (valor is null)
  {
      throw new ArgumentNullException(nameof(valor));
  }
  ```
- **Solución:** `return valor ?? throw new ArgumentNullException(nameof(valor));`

### `csharp_style_unused_value_assignment_preference = discard_variable`
- **Qué vigila:** asignaciones ignoradas deben usar descartes.
- **Error:**
  ```csharp
  resultado = Calcular();
  resultado = 0; // sin usar
  ```
- **Solución:** `_ = Calcular();`

### `csharp_style_unused_value_expression_statement_preference = discard_variable`
- **Qué vigila:** expresiones cuyo valor se descarta deben asignarse a `_`.
- **Error:** `Calcular(); // retorno ignorado`
- **Solución:** `_ = Calcular();`

## Directivas `using`

### `csharp_using_directive_placement = outside_namespace`
- **Qué vigila:** `using` antes del namespace.
- **Error:**
  ```csharp
  namespace Proyecto
  {
      using System;
  }
  ```
- **Solución:**
  ```csharp
  using System;
  namespace Proyecto
  {
  }
  ```

## Preferencias de línea experimentales

### `csharp_style_allow_blank_line_after_colon_in_constructor_initializer_experimental = true:silent`
- **Qué vigila:** permite línea en blanco después de `: base(...)`.
- **Error:**
  ```csharp
  public Servicio()
      : base()
  {
      Inicializar();
  }
  ```
  se comprime eliminando la separación.
- **Solución:**
  ```csharp
  public Servicio()
      : base()

  {
      Inicializar();
  }
  ```
  dejar o quitar según claridad; no habrá advertencia.

### `csharp_style_allow_blank_line_after_token_in_arrow_expression_clause_experimental = true:silent`
- **Qué vigila:** acepta línea en blanco tras `=>`.
- **Error:**
  ```csharp
  public string Nombre =>
  Calcular();
  ```
  se compacta perdiendo el respiro visual.
- **Solución:**
  ```csharp
  public string Nombre =>

      Calcular();
  ```
  mantener el salto si mejora lectura.

### `csharp_style_allow_blank_line_after_token_in_conditional_expression_experimental = true:silent`
- **Qué vigila:** permite líneas en blanco dentro de operadores ternarios.
- **Error:**
  ```csharp
  var resultado = condicion ?
  valorPositivo : valorNegativo;
  ```
  sin espacio para alinear.
- **Solución:**
  ```csharp
  var resultado = condicion ?

      valorPositivo :
      valorNegativo;
  ```
  conservar la organización elegida.

### `csharp_style_allow_blank_lines_between_consecutive_braces_experimental = true:silent`
- **Qué vigila:** deja espacio entre llaves consecutivas.
- **Error:**
  ```csharp
  if (ok)
  {
      Accion();
  }
  else
  {
      Alterna();
  }
  ```
  se obliga a juntar las llaves.
- **Solución:**
  ```csharp
  if (ok)
  {
      Accion();
  }


  else
  {
      Alterna();
  }
  ```
  respetar el espacio extra si comunica bloques.

### `csharp_style_allow_embedded_statements_on_same_line_experimental = true:silent`
- **Qué vigila:** permite instrucciones embebidas en la misma línea cuando sea claro.
- **Error:**
  ```csharp
  if (ok)
  {
      Ejecutar();
  }
  ```
  se fuerza bloque multilínea.
- **Solución:** `if (ok) Ejecutar();` mantener la versión legible acordada.

## Reglas de formato de C#

### `csharp_new_line_before_catch = true`
- **Qué vigila:** `catch` inicia en línea nueva.
- **Error:** `try { ... } catch (Exception ex) { ... }`
- **Solución:** colocar `catch` en línea aparte.

### `csharp_new_line_before_else = true`
- **Qué vigila:** `else` en línea nueva.
- **Error:** `if (ok) { } else { }` en misma línea.
- **Solución:**
  ```csharp
  if (ok)
  {
  }
  else
  {
  }
  ```

### `csharp_new_line_before_finally = true`
- **Qué vigila:** `finally` en línea nueva.
- **Error:** `} finally {` en la misma línea.
- **Solución:** mover `finally` abajo.

### `csharp_new_line_before_members_in_anonymous_types = true`
- **Qué vigila:** cada miembro de tipo anónimo en línea nueva.
- **Error:** `var dto = new { Id = 1, Nombre = "Ana" };`
- **Solución:**
  ```csharp
  var dto = new
  {
      Id = 1,
      Nombre = "Ana",
  };
  ```

### `csharp_new_line_before_members_in_object_initializers = true`
- **Qué vigila:** cada propiedad inicializada va en su línea.
- **Error:** `var pedido = new Pedido { Id = 1, Total = 100 };`
- **Solución:** colocar cada asignación en línea independiente.

### `csharp_new_line_before_open_brace = all`
- **Qué vigila:** las llaves de apertura se ubican en línea aparte.
- **Error:** `class Pedido {` en misma línea.
- **Solución:**
  ```csharp
  class Pedido
  {
  }
  ```

### `csharp_new_line_between_query_expression_clauses = true`
- **Qué vigila:** en LINQ cada cláusula en línea nueva.
- **Error:** `var q = from x in xs where x > 0 select x;`
- **Solución:**
  ```csharp
  var q =
      from x in xs
      where x > 0
      select x;
  ```

### `csharp_indent_block_contents = true`
- **Qué vigila:** contenido de bloques se indenta.
- **Error:**
  ```csharp
  if (ok)
  {
  Console.WriteLine();
  }
  ```
- **Solución:** indentar con cuatro espacios.

### `csharp_indent_braces = false`
- **Qué vigila:** llaves alineadas con la instrucción.
- **Error:**
  ```csharp
      {
  ```
  indentada.
- **Solución:** llaves alineadas sin espacios extra.

### `csharp_indent_case_contents = true`
- **Qué vigila:** el contenido del `case` se indenta.
- **Error:**
  ```csharp
  switch (estado)
  {
      case Estado.Activo:
      resultado = 1;
          break;
  }
  ```
- **Solución:** alinear `resultado` con tabulación.

### `csharp_indent_case_contents_when_block = true`
- **Qué vigila:** si el `case` usa `{ }` se indenta igual.
- **Error:** bloques sin indentación dentro del `case`.
- **Solución:** aplicar cuatro espacios al bloque interno.

### `csharp_indent_labels = one_less_than_current`
- **Qué vigila:** etiquetas se colocan una sangría menos.
- **Error:**
  ```csharp
      etiqueta:
  ```
- **Solución:**
  ```csharp
  etiqueta:
      Instruccion();
  ```

### `csharp_indent_switch_labels = true`
- **Qué vigila:** `case` indenta respecto al `switch`.
- **Error:** `case` al mismo nivel que `switch`.
- **Solución:** agregar sangría.

### `csharp_space_after_cast = false`
- **Qué vigila:** no hay espacio tras un cast.
- **Error:** `(Cliente) objeto`
- **Solución:** `(Cliente)objeto`

### `csharp_space_after_colon_in_inheritance_clause = true`
- **Qué vigila:** un espacio tras `:` de herencia.
- **Error:** `class Pedido:Base`
- **Solución:** `class Pedido : Base`

### `csharp_space_after_comma = true`
- **Qué vigila:** espacio después de comas.
- **Error:** `Metodo(a,b)`
- **Solución:** `Metodo(a, b)`

### `csharp_space_after_dot = false`
- **Qué vigila:** nada de espacios tras `.`.
- **Error:** `obj. Metodo();`
- **Solución:** `obj.Metodo();`

### `csharp_space_after_keywords_in_control_flow_statements = true`
- **Qué vigila:** espacio entre palabra clave y paréntesis.
- **Error:** `if(true)`
- **Solución:** `if (true)`

### `csharp_space_after_semicolon_in_for_statement = true`
- **Qué vigila:** espacio tras `;` en `for`.
- **Error:** `for (var i = 0;i < 10;i++)`
- **Solución:** `for (var i = 0; i < 10; i++)`

### `csharp_space_around_binary_operators = before_and_after`
- **Qué vigila:** espacios alrededor de `+`, `==`, etc.
- **Error:** `a+b`
- **Solución:** `a + b`

### `csharp_space_around_declaration_statements = false`
- **Qué vigila:** evita espacio extra alrededor de declaraciones.
- **Error:** `int  valor = 0;`
- **Solución:** `int valor = 0;`

### `csharp_space_before_colon_in_inheritance_clause = true`
- **Qué vigila:** espacio antes del `:` en herencia.
- **Error:** `class Pedido: Base`
- **Solución:** `class Pedido : Base`

### `csharp_space_before_comma = false`
- **Qué vigila:** no hay espacios antes de comas.
- **Error:** `Metodo(a , b)`
- **Solución:** `Metodo(a, b)`

### `csharp_space_before_dot = false`
- **Qué vigila:** no hay espacio antes del punto.
- **Error:** `obj .Metodo()`
- **Solución:** `obj.Metodo()`

### `csharp_space_before_open_square_brackets = false`
- **Qué vigila:** sin espacio antes de `[`.
- **Error:** `lista [0]`
- **Solución:** `lista[0]`

### `csharp_space_before_semicolon_in_for_statement = false`
- **Qué vigila:** sin espacio antes de `;` en `for`.
- **Error:** `for (var i = 0 ; i < 10 ; i++)`
- **Solución:** `for (var i = 0; i < 10; i++)`

### `csharp_space_between_empty_square_brackets = false`
- **Qué vigila:** `[]` sin espacio.
- **Error:** `new int[ ]`
- **Solución:** `new int[]`

### `csharp_space_between_method_call_empty_parameter_list_parentheses = false`
- **Qué vigila:** `()` sin espacios en llamadas vacías.
- **Error:** `Metodo( )`
- **Solución:** `Metodo()`

### `csharp_space_between_method_call_name_and_opening_parenthesis = false`
- **Qué vigila:** ningún espacio entre nombre y `(`.
- **Error:** `Metodo ()`
- **Solución:** `Metodo()`

### `csharp_space_between_method_call_parameter_list_parentheses = false`
- **Qué vigila:** sin espacio interior en paréntesis.
- **Error:** `Metodo( valor )`
- **Solución:** `Metodo(valor)`

### `csharp_space_between_method_declaration_empty_parameter_list_parentheses = false`
- **Qué vigila:** declaraciones sin espacio en `()` vacío.
- **Error:** `void Ejecutar( )`
- **Solución:** `void Ejecutar()`

### `csharp_space_between_method_declaration_name_and_open_parenthesis = false`
- **Qué vigila:** declaración sin espacio antes de `(`.
- **Error:** `void Ejecutar ()`
- **Solución:** `void Ejecutar()`

### `csharp_space_between_method_declaration_parameter_list_parentheses = false`
- **Qué vigila:** sin espacios interiores.
- **Error:** `void Ejecutar( int valor )`
- **Solución:** `void Ejecutar(int valor)`

### `csharp_space_between_parentheses = false`
- **Qué vigila:** sin espacios internos en `()`.
- **Error:** `( valor )`
- **Solución:** `(valor)`

### `csharp_space_between_square_brackets = false`
- **Qué vigila:** sin espacios en `[]`.
- **Error:** `[ valor ]`
- **Solución:** `[valor]`

### `csharp_preserve_single_line_blocks = true`
- **Qué vigila:** bloques de una línea se conservan si ya existen.
- **Error:** forzar salto innecesario en `{ }` cortos.
- **Solución:** dejar bloques en una sola línea cuando sean triviales.

### `csharp_preserve_single_line_statements = true`
- **Qué vigila:** mantiene sentencias de una línea.
- **Error:** dividir una instrucción simple en múltiples líneas sin motivo.
- **Solución:** conservar la versión compacta si era clara.

## Reglas de nomenclatura

### `dotnet_naming_rule.interface_should_be_begins_with_i.severity = suggestion`
- **Qué vigila:** severidad sugerida para prefijo `I`.
- **Error:** ignorar la sugerencia y mezclar estilos.
- **Solución:** respetar la convención para evitar advertencias.

### `dotnet_naming_rule.interface_should_be_begins_with_i.symbols = interface`
- **Qué vigila:** la regla afecta únicamente a interfaces.
- **Error:** asumir que aplica a clases.
- **Solución:** aplicar otras reglas para tipos distintos.

### `dotnet_naming_rule.interface_should_be_begins_with_i.style = begins_with_i`
- **Qué vigila:** nombre de interfaz inicia con `I` y resto en PascalCase.
- **Error:** `public interface Servicio`
- **Solución:** `public interface IServicio`

### `dotnet_naming_rule.types_should_be_pascal_case.severity = suggestion`
- **Qué vigila:** severidad sugerida para que los tipos usen PascalCase.
- **Error:** dejar `class clienteServicio`.
- **Solución:** `class ClienteServicio`

### `dotnet_naming_rule.types_should_be_pascal_case.symbols = types`
- **Qué vigila:** aplica a clases, structs, interfaces y enums.
- **Error:** olvidar renombrar un `enum` en PascalCase.
- **Solución:** `enum EstadoPedido { Activo }`

### `dotnet_naming_rule.types_should_be_pascal_case.style = pascal_case`
- **Qué vigila:** formato PascalCase.
- **Error:** `struct punto2d`
- **Solución:** `struct Punto2D`

### `dotnet_naming_rule.non_field_members_should_be_pascal_case.severity = suggestion`
- **Qué vigila:** miembros no campo en PascalCase.
- **Error:** `void procesarEvento()`
- **Solución:** `void ProcesarEvento()`

### `dotnet_naming_rule.non_field_members_should_be_pascal_case.symbols = non_field_members`
- **Qué vigila:** se dirige a propiedades, eventos y métodos.
- **Error:** creer que cubre campos.
- **Solución:** crear regla adicional si se requiere para campos.

### `dotnet_naming_rule.non_field_members_should_be_pascal_case.style = pascal_case`
- **Qué vigila:** usa PascalCase para los miembros alcanzados.
- **Error:** `event EventHandler cambiado;`
- **Solución:** `event EventHandler Cambiado;`

### `dotnet_naming_symbols.interface.applicable_kinds = interface`
- **Qué vigila:** la regla de interfaces solo afecta a `interface`.
- **Error:** esperar aplicación en clases.
- **Solución:** definir reglas extra si se necesitan.

### `dotnet_naming_symbols.interface.applicable_accessibilities = public, internal, private, protected, protected_internal, private_protected`
- **Qué vigila:** todas las visibilidades.
- **Error:** dejar `interface` privada sin `I`.
- **Solución:** siempre usar prefijo `I`.

### `dotnet_naming_symbols.interface.required_modifiers =`
- **Qué vigila:** no hay modificadores obligatorios.
- **Error:** pensar que solo aplica a interfaces `public`.
- **Solución:** aplicar convención sin importar modificadores.

### `dotnet_naming_symbols.types.applicable_kinds = class, struct, interface, enum`
- **Qué vigila:** la regla de tipos abarca estas categorías.
- **Error:** olvidar un `enum` en snake_case.
- **Solución:** renombrar a PascalCase.

### `dotnet_naming_symbols.types.applicable_accessibilities = public, internal, private, protected, protected_internal, private_protected`
- **Qué vigila:** cualquier visibilidad.
- **Error:** creer que clases internas pueden romper la regla.
- **Solución:** cumplir PascalCase siempre.

### `dotnet_naming_symbols.types.required_modifiers =`
- **Qué vigila:** sin modificadores obligatorios.
- **Error:** interpretar que solo aplica a clases `public`.
- **Solución:** aplicar a todos los tipos.

### `dotnet_naming_symbols.non_field_members.applicable_kinds = property, event, method`
- **Qué vigila:** qué miembros cubre la regla.
- **Error:** incluir campos pensando que están cubiertos.
- **Solución:** aplicar la convención solo a estos miembros.

### `dotnet_naming_symbols.non_field_members.applicable_accessibilities = public, internal, private, protected, protected_internal, private_protected`
- **Qué vigila:** miembros con cualquier visibilidad.
- **Error:** dejar métodos privados en camelCase.
- **Solución:** renombrar en PascalCase.

### `dotnet_naming_symbols.non_field_members.required_modifiers =`
- **Qué vigila:** no exige modificadores concretos.
- **Error:** asumir que solo aplica a métodos `public`.
- **Solución:** seguir la convención para todos los miembros listados.

### `dotnet_naming_style.pascal_case.required_prefix =`
- **Qué vigila:** no añade prefijo.
- **Error:** anteponer `_` a clases.
- **Solución:** dejar el nombre limpio en PascalCase.

### `dotnet_naming_style.pascal_case.required_suffix =`
- **Qué vigila:** no exige sufijo.
- **Error:** `ClienteServiceClass`
- **Solución:** `ClienteService`

### `dotnet_naming_style.pascal_case.word_separator =`
- **Qué vigila:** sin separadores.
- **Error:** `Cliente_Service`
- **Solución:** `ClienteService`

### `dotnet_naming_style.pascal_case.capitalization = pascal_case`
- **Qué vigila:** iniciales de cada palabra en mayúscula.
- **Error:** `procesarEvento`
- **Solución:** `ProcesarEvento`

### `dotnet_naming_style.begins_with_i.required_prefix = I`
- **Qué vigila:** interfaces empiezan con `I`.
- **Error:** `interface Servicio`
- **Solución:** `interface IServicio`

### `dotnet_naming_style.begins_with_i.required_suffix =`
- **Qué vigila:** no requiere sufijos.
- **Error:** `IServicioInterface`
- **Solución:** `IServicio`

### `dotnet_naming_style.begins_with_i.word_separator =`
- **Qué vigila:** sin separadores.
- **Error:** `I_Servicio`
- **Solución:** `IServicio`

### `dotnet_naming_style.begins_with_i.capitalization = pascal_case`
- **Qué vigila:** resto del nombre en PascalCase.
- **Error:** `Iservicio`
- **Solución:** `IServicio`

## Sección `[*.{cs,vb}]`

### `[*.{cs,vb}]`
- **Qué vigila:** la sección final aplica a archivos C# y VB.
- **Error:** asumir que solo C# recibe estos ajustes.
- **Solución:** aplicar igualmente si se agrega VB.

### `end_of_line = crlf`
- **Qué vigila:** también obliga a `CRLF` para VB si existiera.
- **Error:** archivo VB con `LF`.
- **Solución:** normalizar con `CRLF`.

### `dotnet_style_allow_multiple_blank_lines_experimental = true:silent`
- **Qué vigila:** repite la tolerancia a múltiples líneas en blanco pero como sugerencia silenciosa.
- **Error:**
  ```csharp
  Console.WriteLine("A");
  Console.WriteLine("B");
  ```
  se compacta sin dejar respiración visual.
- **Solución:**
  ```csharp
  Console.WriteLine("A");


  Console.WriteLine("B");
  ```
  mantener el formato claro sin temor a advertencias.

### `dotnet_style_allow_statement_immediately_after_block_experimental = true:silent`
- **Qué vigila:** permite colocar instrucciones tras un bloque en C# o VB.
- **Error:**
  ```csharp
  if (ok)
  {
      Procesar();
  }

  Auditar();
  ```
  se inserta salto obligatorio.
- **Solución:**
  ```csharp
  if (ok)
  {
      Procesar();
  }
  Auditar();
  ```
  conservar o quitar la línea según claridad.

## Configuración personalizada de diagnósticos

### `dotnet_diagnostic.SA1201.severity = none`
- **Qué vigila:** desactiva la regla StyleCop SA1201 (orden de elementos).
- **Error:** asumir que la regla sigue activa y reorganizar innecesariamente.
- **Solución:** respetar el orden existente salvo otras reglas.

### `dotnet_diagnostic.IDE0005.severity = error`
- **Qué vigila:** `using` innecesarios son errores.
- **Error:**
  ```csharp
  using System.Threading;
  ```
  sin usar.
- **Solución:** eliminar el `using`.

### `dotnet_diagnostic.CS8019.severity = error`
- **Qué vigila:** el compilador también marca `using` innecesarios como error.
- **Error:** mantener `using` sin uso esperando solo advertencia.
- **Solución:** eliminar las directivas redundantes.

### `dotnet_remove_unnecessary_imports = true`
- **Qué vigila:** `dotnet format` eliminará `using` sobrantes automáticamente.
- **Error:** dejar `using` esperando limpiarlos manualmente.
- **Solución:** ejecutar `dotnet format` o aplicar la corrección sugerida.

=======
# Validaciones de estilo y calidad de código

Este repositorio aplica analizadores Roslyn para mantener un estilo de código coherente y seguro. Los paquetes **StyleCop.Analyzers**, **Roslynator.CodeAnalysis.Analyzers** y los analizadores de código de .NET se activan mediante la configuración de `ProyectoValidations.Api.csproj` y las reglas declaradas en `.editorconfig`. A continuación se describen las validaciones principales junto con ejemplos de incumplimiento y la forma correcta de resolverlos.

## Analizadores habilitados

- **StyleCop.Analyzers**: aplica convenciones de estilo clásicas de C# (orden de elementos, documentación XML, espaciado coherente, etc.).
- **Roslynator**: refuerza patrones recomendados por el equipo .NET y detecta prácticas susceptibles de errores.
- **Analizadores del SDK (.NET)**: respetan las preferencias definidas en `.editorconfig`, convirtiendo varias sugerencias en advertencias o errores de compilación (por ejemplo, IDE0005 para `using` innecesarios).

## Formato y espaciado

- **Indentación y tabs**: siempre se usan espacios con un ancho de cuatro columnas (`indent_style = space`, `indent_size = 4`).
- **Final de línea**: se mantiene `CRLF` en todos los archivos `.cs` para evitar diffs innecesarios entre plataformas.
- **Espacios obligatorios**: se exige espacio alrededor de operadores binarios y después de comas y palabras clave de control (`csharp_space_around_binary_operators = before_and_after`, `csharp_space_after_comma = true`).
- **Sin líneas en blanco múltiples**: aunque se toleran en la edición, el formato estándar favorece bloques compactos y consistentes.

**Ejemplo (espaciado incorrecto):**

```csharp
public int Sumar (int a,int b){
return a+b;
}
```

**Solución:**

```csharp
public int Sumar(int a, int b)
{
    return a + b;
}
```

La corrección reemplaza tabs por espacios, elimina espacios innecesarios y añade los requeridos alrededor de operadores y comas.

## Palabras clave y tipos

- **Tipos predefinidos**: se prefieren `int`, `string`, `bool`, etc. sobre los nombres del BCL tanto en declaraciones como en accesos (`dotnet_style_predefined_type_* = true`).
- **Evitar `var` implícito**: el proyecto favorece la claridad usando el tipo explícito en lugar de `var` (`csharp_style_var_* = false`).
- **Preferencias modernas**: se incentivan expresiones condicionales, operadores de fusión nula y propagación de nulos cuando aportan claridad (`dotnet_style_coalesce_expression = true`, `dotnet_style_null_propagation = true`).

**Ejemplo (uso indebido de `var` y tipos del BCL):**

```csharp
var total = System.Int32.Parse(valor);
```

**Solución:**

```csharp
int total = int.Parse(valor);
```

## Organización de `using`

- Los `using` deben mantenerse limpios; los innecesarios provocan el error IDE0005/CS8019 y detienen la compilación.
- Las directivas `using` se ubican fuera del `namespace` (`csharp_using_directive_placement = outside_namespace`).

**Ejemplo (directiva innecesaria):**

```csharp
using System.Threading; // ❌ No se utiliza

namespace Demo;
```

**Solución:**

```csharp
namespace Demo;
```

Eliminar los `using` sin uso evita errores y mantiene el encabezado del archivo conciso.

## Nomenclatura

- **Interfaces**: deben iniciar con `I` seguido de PascalCase (`dotnet_naming_rule.interface_should_be_begins_with_i`).
- **Tipos y miembros**: clases, estructuras, métodos, propiedades y eventos utilizan PascalCase (`dotnet_naming_rule.types_should_be_pascal_case`, `dotnet_naming_rule.non_field_members_should_be_pascal_case`).

**Ejemplo (nombres incorrectos):**

```csharp
public interface ServicioLogger
{
    void registrar_evento();
}
```

**Solución:**

```csharp
public interface IServicioLogger
{
    void RegistrarEvento();
}
```

## Modificadores y visibilidad

- Se exige declarar los modificadores de acceso explícitamente en miembros no pertenecientes a interfaces (`dotnet_style_require_accessibility_modifiers = for_non_interface_members`).
- El orden de modificadores debe seguir la secuencia recomendada (`csharp_preferred_modifier_order = public, private, protected, internal, ...`).
- Se favorecen campos `readonly` cuando el valor no cambia después de la construcción (`dotnet_style_readonly_field = true`).

**Ejemplo (orden y ausencia de modificadores):**

```csharp
static public class Util
{
    readonly static int limite = 10;
}
```

**Solución:**

```csharp
public static class Util
{
    private static readonly int Limite = 10;
}
```

Además de ordenar los modificadores, se aplica PascalCase y se expone la visibilidad deseada.

## Patrones y expresiones modernas

- Se prefiere el uso de patrones sobre comprobaciones clásicas (`csharp_style_pattern_matching_over_is_with_cast_check = true`).
- Se incentiva `switch` expresiones, coincidencias extendidas y la negación con `is not` cuando simplifican el código (`csharp_style_prefer_switch_expression = true`, `csharp_style_prefer_not_pattern = true`).
- Para comparaciones con `null`, se prefiere `is null`/`is not null` (`dotnet_style_prefer_is_null_check_over_reference_equality_method = true`).

**Ejemplo (patrón clásico):**

```csharp
if (obj != null && obj is Cliente)
{
    var cliente = (Cliente)obj;
    Procesar(cliente);
}
```

**Solución:**

```csharp
if (obj is Cliente cliente)
{
    Procesar(cliente);
}
```

## Inicializadores y colecciones

- Se favorecen inicializadores de objetos y colecciones para mejorar la legibilidad (`dotnet_style_object_initializer = true`, `dotnet_style_collection_initializer = true`).
- Cuando es viable, se puede utilizar la sintaxis de expresiones de colección (`dotnet_style_prefer_collection_expression = when_types_loosely_match`).
- Las tuplas y tipos anónimos deben usar nombres inferidos (`dotnet_style_prefer_inferred_tuple_names = true`, `dotnet_style_prefer_inferred_anonymous_type_member_names = true`).

**Ejemplo (sin inicializadores):**

```csharp
var cliente = new Cliente();
cliente.Nombre = "Ada";
cliente.Edad = 35;
```

**Solución:**

```csharp
var cliente = new Cliente
{
    Nombre = "Ada",
    Edad = 35,
};
```

## Organización de archivos y namespaces

- Los espacios de nombres deben alinearse con la estructura de carpetas (`dotnet_style_namespace_match_folder = true`).
- Se mantiene una sola declaración por línea para mejorar la trazabilidad en revisiones (`csharp_preserve_single_line_blocks = true`).
- Se generan archivos con documentación XML cuando corresponda (`GenerateDocumentationFile = true`), lo que obliga a mantener comentarios de resumen actualizados.

---

Al seguir estas reglas se garantiza que las aportaciones al proyecto cumplan las convenciones de estilo, reduzcan errores comunes y mantengan un código homogéneo para todo el equipo. Cada ejemplo mostrado refleja errores típicos que los analizadores detectarán y cómo corregirlos de inmediato.
