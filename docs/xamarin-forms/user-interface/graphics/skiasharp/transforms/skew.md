---
title: Transformación de sesgo
description: En este artículo se explica cómo la transformación de sesgo puede crear objetos gráficos superpuestos de SkiaSharp y esto se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: a0c65ab2d319e39b236799cd453874142206b467
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058891"
---
# <a name="the-skew-transform"></a>Transformación de sesgo

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Vea cómo la transformación de sesgo puede crear objetos gráficos superpuestos de SkiaSharp_

En SkiaSharp, la transformación de sesgo inclina a objetos gráficos, como la sombra en esta imagen:

![](skew-images/skewexample.png "Un ejemplo de sesgado del programa sesgar sombra del texto")

El sesgo convierte un rectángulo en un paralelogramo, pero una elipse sesgada sigue siendo una elipse.

Aunque Xamarin.Forms define las propiedades de traslación, escala y rotaciones, no hay ninguna propiedad correspondiente en Xamarin.Forms para skew.

El [ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) método `SKCanvas` acepta dos argumentos de sesgo horizontal y vertical sesgar:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Un segundo [ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) método combina estos argumentos en una sola `SKPoint` valor:

```csharp
public void Skew (SKPoint skew)
```

Sin embargo, no es probable que se usará uno de estos dos métodos de forma aislada.

El **sesgar experimentar** página permite experimentar con asimetría de valores comprendidos entre -10 y 10. Una cadena de texto se coloca en la esquina superior izquierda de la página, con valores de sesgo obtenidos dos `Slider` elementos. Este es el `PaintSurface` controlador en el [ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) clase:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

Los valores de la `xSkew` argumento desplazar la parte inferior del texto adecuado para los valores positivos o izquierda para los valores negativos. Los valores de `ySkew` desplazar a la derecha del texto hacia abajo para los valores positivos o suscribirse a los valores negativos:

[![](skew-images/skewexperiment-small.png "Captura de pantalla triple de la página del experimento sesgar")](skew-images/skewexperiment-large.png#lightbox "Triple captura de pantalla de la página del experimento sesgar")

Si el `xSkew` valor es el negativo de la `ySkew` valor, el resultado es la rotación, pero también escalar un poco como indica la presentación UWP.

Las fórmulas de transformación son los siguientes:

x' = x + xSkew (en inglés) y

y' = ySkew (en inglés) x + y

Por ejemplo, para un positivo `xSkew` valor transformado `x'` valor aumenta a medida que `y` aumenta. Eso es lo que hace que la inclinación.

Si un ancho de píxeles de triángulo 200 y 100 píxeles de alto se coloca con su esquina superior izquierda en el punto (0, 0) y se representa con un `xSkew` valor de 1.5, los resultados del paralelogramo siguientes:

![](skew-images/skeweffect.png "El efecto de la transformación de sesgo en un rectángulo")

Las coordenadas del borde inferior tienen `y` valores de 100, por lo que es desplazado a 150 píxeles a la derecha.

Para los valores distintos de cero de `xSkew` o `ySkew`, solo el punto (0, 0) sigue siendo el mismo. Ese punto se puede considerar el centro de sesgado. Si necesita el centro del sesgo para que sea algo más (que suele ser el caso), no hay ningún `Skew` método que proporciona que. Deberá combinar explícitamente `Translate` llama con el `Skew` llamar. Para centrar el sesgo en `px` y `py`, realizar las llamadas siguientes:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

Las fórmulas de la transformación compuesta son:

x' = x + xSkew (en inglés) (y – py)

y' = ySkew (en inglés) (x – px) + y

Si `ySkew` es cero, el `px` no se utiliza el valor. El valor es irrelevante y de forma similar para `ySkew` y `py`.

Se podría siente más cómodo especificando sesgo como un ángulo de inclinación, como el ángulo α en este diagrama:

![](skew-images/skewangleeffect.png "Indica el efecto de la transformación de sesgo en un rectángulo con un ángulo de sesgo")

La relación entre el turno de 150 píxeles a la vertical 100 píxeles es la tangente del ángulo que, en este ejemplo 56.3 grados.

El archivo XAML de la **experimento de ángulo de sesgo** página es similar a la **ángulo de sesgo** página salvo que el `Slider` elementos oscilar entre-90 grados y 90 grados. El [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) centra el texto en la página de archivo de código subyacente y usa `Translate` para establecer un centro de sesgo en el centro de la página. Short `SkewDegrees` método en la parte inferior del código convierte ángulos para sesgar valores:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

Como aproxima a un ángulo positivos o negativos de 90 grados, infinito aproxima a la tangente, pero son utilizables ángulos hasta aproximadamente 80 grados o algo así:

[![](skew-images/skewangleexperiment-small.png "Captura de pantalla triple de la página del experimento de ángulo de sesgo")](skew-images/skewangleexperiment-large.png#lightbox "Triple captura de pantalla de la página del experimento de ángulo de sesgo")

Un sesgo horizontal negativo pequeño puede imitar oblicua o cursiva texto, como el **texto oblicua** muestra la página. El [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) clase muestra cómo hacerlo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

El `TextAlign` propiedad de `SKPaint` está establecido en `Center`. Sin las transformaciones, el `DrawText` llamar con las coordenadas de (0, 0) pudiera ubicar el texto con el centro horizontal de la línea base en la esquina superior izquierda. El `SkewDegrees` sesga el texto horizontalmente 20 grados con respecto a la línea base. El `Translate` llamada mueve al centro horizontal de la línea base del texto en el centro del lienzo:

[![](skew-images/obliquetext-small.png "Captura de pantalla de la página de texto oblicua triple")](skew-images/obliquetext-large.png#lightbox "Triple captura de pantalla de la página de texto oblicua")

El **sesgar el texto con sombra** página muestra cómo usar una combinación de una escala de sesgo y vertical de 45 grados para realizar una sombra de texto que se inclina hacia fuera del texto. Aquí es la parte pertinente de la `PaintSurface` controlador:

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

La sombra es muestra primero y, a continuación, el texto:

[![](skew-images/skewshadowtext1-small.png "Captura de pantalla de la página de texto con sombra sesgar triple")](skew-images/skewshadowtext1-large.png#lightbox "Triple captura de pantalla de la página sesgar sombra del texto")

La coordenada vertical se pasa a la `DrawText` método indica la posición del texto en relación con la línea base. Que es la misma coordenada vertical utilizada para el centro de sesgado. Esta técnica no funcionará si la cadena de texto contiene los trazos descendentes. Por ejemplo, sustituya la palabra "extraña" para "Sombra" y este es el resultado:

[![](skew-images/skewshadowtext2-small.png "Captura de pantalla de la página sesgar sombra del texto con una palabra alternativa con trazos descendentes triple")](skew-images/skewshadowtext2-large.png#lightbox "Triple captura de pantalla de la página sesgar sombra del texto con una palabra alternativa con trazos descendentes")

Todavía se alinean las instantáneas y el texto en la línea base, pero el efecto simplemente no es correcto. Para corregirlo, deberá obtener los límites del texto:

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

El `Translate` llamadas deben ser ajustado por el alto de los trazos descendentes:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Ahora la sombra se extiende desde la parte inferior de los trazos descendentes:

[![](skew-images/skewshadowtext3-small.png "Captura de pantalla de la página sesgar sombra del texto con los ajustes para los trazos descendentes triple")](skew-images/skewshadowtext3-large.png#lightbox "Triple captura de pantalla de la página sesgar sombra del texto con los ajustes para los trazos descendentes")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
