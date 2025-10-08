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
