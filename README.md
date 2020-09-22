# UsefulSnippets
A collection of Unity / Unity3d code snippets that I found useful. Perhaps others will as well.

## Color
 
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


## UIElements
 
#### UIElements - Draw IMGUI Reorderable Lists (2020.2)

![](https://i.imgur.com/4IbSmKv.png)

 <details>
 <summary>SerializedProperty Extension to determine if property is a List/Array</summary>
 
```cs
public static class PropertyExtensions
{
    public static bool IsReallyArray(this SerializedProperty property)
    {
        return property.isArray && property.propertyType != SerializedPropertyType.String;
    }
}
```

 </details>

 <details>
 <summary>Draw IMGUI Reorderable Lists Code</summary>
  
 ```cs
[CustomEditor(typeof(Object), true, isFallback = true)]
[CanEditMultipleObjects]
public class DefaultEditorDrawer : Editor
{
    public bool showScript;
    public List<string> excludedFields = new List<string>();
    string m_IMGUIPropNeedsRelayout;
    ScrollView m_ScrollView;

    public override VisualElement CreateInspectorGUI()
    {
        var root = new VisualElement();
        var property = serializedObject.GetIterator();
        m_ScrollView = new ScrollView();
        root.Add(m_ScrollView);
        if (property.NextVisible(true)) // Expand first child.
        {
            do
            {
                var field = new PropertyField(property) {name = "PropertyField:" + property.propertyPath};
                if (property.propertyPath == "m_Script" && serializedObject.targetObject != null)
                {
                    if (showScript) field.SetEnabled(false);
                    else continue;
                }

                if (property.IsReallyArray() && serializedObject.targetObject != null)
                {
                    var copiedProperty = property.Copy();
                    var imDefaultProperty = new IMGUIContainer(() =>
                    {
                        DoDrawDefaultIMGUIProperty(serializedObject, copiedProperty);
                    }) {name = property.propertyPath};
                    
                    m_ScrollView.Add(imDefaultProperty);
                    continue;
                }

                if (excludedFields.Contains(property.propertyPath) && serializedObject.targetObject != null) { continue; }

                root.Add(field);
            } while (property.NextVisible(false));
        }

        foreach (var foldout in m_ScrollView.Query<Foldout>().ToList())
        {
            foldout.RegisterValueChangedCallback(e =>
            {
                var fd = e.target as Foldout;
                if (fd == null) return;
                var path = fd.bindingPath;
                var container = m_ScrollView.Q<IMGUIContainer>(name: path);
                RecomputeSize(container);
            });
        }

        return root;
    }

    public void RecomputeSize(IMGUIContainer container)
    {
        if (container == null) return;
        var parent = container.parent;
        container.RemoveFromHierarchy();
        parent.Add(container);
    }

    public void DoDrawDefaultIMGUIProperty(SerializedObject serializedObject, SerializedProperty property)
    {
        EditorGUI.BeginChangeCheck();
        serializedObject.Update();
        bool wasExpanded = property.isExpanded;
        EditorGUILayout.PropertyField(property, true);
        if (property.isExpanded != wasExpanded) m_IMGUIPropNeedsRelayout = property.propertyPath;
        serializedObject.ApplyModifiedProperties();
        EditorGUI.EndChangeCheck();
    }
}
 ```
 </details>
