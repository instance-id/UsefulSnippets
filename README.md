# UsefulSnippets
A collection of Unity / Unity3d code snippets that I found useful. Perhaps others will as well.

## Color
---
#### GetColor() Extension
> Usage: 
> ```cs 
> // -- Standard Color
> Color color = "#555555".GetColor()
> 
> // -- StyleColor
> StyleColor styleColor = new StyleColor("#555555".GetColor());
> ```
> 
> <details>
> <summary>Color Extension Code</summary>
>  
> ```cs
> using UnityEngine;
> 
> namespace instance.id.ColorExtensions
> {
>     public static class ColorExtensions
>     {
>         public static Color GetColor(this string color)
>         {
>             ColorUtility.TryParseHtmlString(color, out var outColor);
>             return outColor;
>         }
>     }
> }
> ```
> </details>
