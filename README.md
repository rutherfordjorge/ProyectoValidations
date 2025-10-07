# ProyectoValidations.Api

## Análisis estático y linting

Este proyecto incluye analizadores Roslyn (StyleCop y Roslynator) configurados mediante el archivo `.editorconfig` del proyecto API. Para aprovechar al máximo los diagnósticos durante la edición y añadir SonarLint, sigue estas recomendaciones según tu IDE.

### Visual Studio Code

1. Instala las extensiones recomendadas cuando abras la carpeta:
   * **C#** (`ms-dotnettools.csharp`) para OmniSharp.
   * **SonarLint** (`sonarsource.sonarlint-vscode`) para diagnósticos adicionales.
2. OmniSharp se abrirá con Roslyn Analyzers en modo en vivo y con soporte para `.editorconfig` gracias a la configuración incluida en `.vscode/settings.json`.
3. SonarLint funcionará en modo local para esta solución y mostrará diagnósticos en tiempo real mientras editas los archivos C#.

### Visual Studio

1. Activa *Enable full solution analysis* en **Tools > Options > Text Editor > C# > Advanced** para obtener diagnósticos en tiempo real de StyleCop y Roslynator.
2. Instala la extensión **SonarLint for Visual Studio** y, si usas SonarQube/SonarCloud, conecta la solución a tu proyecto para sincronizar reglas.

Con estas configuraciones tendrás StyleCop, Roslynator y SonarLint ejecutándose durante la edición y la compilación.
