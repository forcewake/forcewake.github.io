---
categories:
- code
comments: true
date: "2022-02-17T02:17:00Z"
description: How to remove fields from JSON dynamically in dotnet using Json.Net
tags:
- english
- dotnet
- json.net
- C#
title: 'How to remove fields from JSON dynamically in dotnet using Json.Net'
---


TL;DR Just parse JSON into a JToken, somehow try to match property names. Wherever there's a match, you can remove the property from its parent object.

# RemoveFromLowestPossibleParent

Use method `RemoveFromLowestPossibleParent` from nice `JsonExtensions` class from Brian Rogers [stackoverflow answer](https://stackoverflow.com/a/40118119)

## Code

{{< highlight csharp >}}
public static partial class JsonExtensions
{
    public static TJToken RemoveFromLowestPossibleParent<TJToken>(this TJToken node) where TJToken : JToken
    {
        if (node == null)
            return null;
        JToken toRemove;
        var property = node.Parent as JProperty;
        if (property != null)
        {
            // Also detach the node from its immediate containing property -- Remove() does not do this even though it seems like it should
            toRemove = property;
            property.Value = null;
        }
        else
        {
            toRemove = node;
        }
        if (toRemove.Parent != null)
            toRemove.Remove();
        return node;
    }

    public static IEnumerable<TJToken> RemoveFromLowestPossibleParents<TJToken>(this IEnumerable<TJToken> nodes) where TJToken : JToken
    {
        var list = nodes.ToList();
        foreach (var node in list)
            node.RemoveFromLowestPossibleParent();
        return list;
    }
}
{{< / highlight >}}

# Json field name from lambda expression
Using `PropertyHelper<YourClass>.GetJsonName(c => c.Id)` on property with `JsonPropertyAttribute` attribute will return you json field name for this property.
  
## Code
  
{{< highlight csharp >}}
public static partial class JsonExtensions
{
    public static string GetJsonName<TValue>(Expression<Func<T, TValue>> selector)
    {
        PropertyInfo propInfo = null;
        
        Expression body = selector;
        if (body is LambdaExpression)
        {
            body = ((LambdaExpression)body).Body;
        }
        switch (body.NodeType)
        {
            case ExpressionType.MemberAccess:
                propInfo = (PropertyInfo)((MemberExpression)body).Member;
                break;
            default:
              throw new InvalidOperationException();
        }

        if (propInfo != null)
        {
            var attribute = propInfo.GetCustomAttributes(typeof(JsonPropertyAttribute), true)
              .Cast<JsonPropertyAttribute>()
              .FirstOrDefault();

            if (attribute != null)
            {
                return attribute.PropertyName;
            }
        }

        return null;
    }
}
{{< / highlight >}}

# Combine all
Both methods will allows you to combine in one method and call like: `CleanUpBeforeCompare(meta, m => m.Id)`

## Code

{{< highlight csharp >}}
public static partial class JsonExtensions
{
    public static string CleanUpBeforeCompare<T, V>(IEnumerable<T> sourceMeta, params Expression<Func<T, V>>[] nodesToRemove)
    {
        var names = nodesToRemove
            .Select(PropertyHelper<T>.GetJsonName)
            .ToList();

        var root = (JContainer)JToken.FromObject(sourceMeta);

        var query = root
            .DescendantsAndSelf()
            .OfType<JProperty>()
            .Where(v => v.Type == JTokenType.Property && names.Contains(v.Name));

        query.RemoveFromLowestPossibleParents();

        var json = root.ToString();

        return json;
    }
}
{{< / highlight >}}
