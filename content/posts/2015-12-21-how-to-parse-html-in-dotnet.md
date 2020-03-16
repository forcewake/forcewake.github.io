---
comments: true
date: "2015-12-21T00:00:00Z"
description: Small post about different parsers in .NET with can help you to work
  with HTML
tags:
- english
- dotnet
- html
- parser
- CsQuery
- HtmlAgilityPack
- Fizzler
- AngleSharp
- Regex
title: How to parse HTML in .NET
url: "how-to-parse-html-in-dotnet"
---

Small post about different parsers in .NET with can help you to work with HTML files.

## [HtmlAgilityPack](https://htmlagilitypack.codeplex.com/)

[HtmlAgilityPack](https://htmlagilitypack.codeplex.com/) is one of the most famous HTML parser in .NET world.
This is an agile HTML parser that builds a read/write DOM and supports plain XPATH or XSLT (you actually don't HAVE to understand XPATH nor XSLT to use it, don't worry...). It is a .NET code library that allows you to parse "out of the web" HTML files. The parser is very tolerant with "real world" malformed HTML. The object model is very similar to what proposes System.Xml, but for HTML documents (or streams). 

This parser will be useful if you need to parse something small and pretty (also it will be very fast):

{{< highlight csharp >}}
public class HtmlParser
{
    /// <summary>
    /// Extract all anchor tags using HtmlAgilityPack
    /// </summary>
    /// <param name="htmlSnippet"></param>
    /// <returns>List of URLs</returns>
    public List<string> ExtractAllAHrefTags(HtmlDocument htmlSnippet)
    {
        List<string> hrefTags = new List<string>();
    
        foreach (HtmlNode link in htmlSnippet.DocumentNode.SelectNodes("//a[@href]"))
        {
            HtmlAttribute att = link.Attributes["href"];
            hrefTags.Add(att.Value);
        }
    
        return hrefTags;
    }
}
{{< / highlight >}}

But when you will try to use something more interesting, you will see something unreadable and hard for understanding:

{{< highlight csharp >}}
public class HtmlParser
{
    public IEnumerable<string> ExtractAllHrefTags(string html)
    {
        HtmlDocument hap = new HtmlDocument();
        hap.LoadHtml(html);
        HtmlNodeCollection nodes = hap.DocumentNode.SelectNodes(
            "//h3[contains(concat(' ', @class, ' '), ' r ')]/a");
        if (nodes != null)
            foreach (HtmlNode node in nodes)
                yield return node.GetAttributeValue("href", null);
    }
}
{{< / highlight >}}

## [Fizzler](https://code.google.com/p/fizzler/)

A .NET library to select items from a node tree based on a CSS selector. The default implementation is based on HTMLAgilityPack and selects from HTML documents.

{{< highlight csharp >}}
public class HtmlParser
{
    private void ShowFizzlerFeatures()
    {
        // Load the document using HTMLAgilityPack as normal
        var html = new HtmlDocument();
        html.LoadHtml(@"
        <html>
            <head></head>
            <body>
                <div>
                <p class='content'>Fizzler</p>
                <p>CSS Selector Engine</p></div>
            </body>
        </html>");

        // Fizzler for HtmlAgilityPack is implemented as the 
        // QuerySelectorAll extension method on HtmlNode

        var document = html.DocumentNode;

        // yields: [<p class="content">Fizzler</p>]
        document.QuerySelectorAll(".content"); 

        // yields: [<p class="content">Fizzler</p>,<p>CSS Selector Engine</p>]
        document.QuerySelectorAll("p");

        // yields empty sequence
        document.QuerySelectorAll("body>p");

        // yields [<p class="content">Fizzler</p>,<p>CSS Selector Engine</p>]
        document.QuerySelectorAll("body p");

        // yields [<p class="content">Fizzler</p>]
        document.QuerySelectorAll("p:first-child");
    }
}
{{< / highlight >}}

## [CsQuery](https://github.com/jamietre/CsQuery)

CsQuery is a jQuery port for .NET 4. It implements all CSS2 & CSS3 selectors, all the DOM manipulation methods of jQuery, and some of the utility methods. The majority of the jQuery test suite (as of 1.6.2) has been ported to C#.

### Why CsQuery?

CSS selectors and jQuery make it really easy to access and manipulate HTML on the client. There's no reason it should be any more difficult to do the same thing with some arbitrary HTML on the server. It's a simple as that. Use it in web projects to do post-processing on HTML pages before they're served, for web scraping, parsing templates, and more.

### Note from the author

> CsQuery is not being actively maintained. I no longer use it in my day-to-day work, and indeed don't even work in .NET much these day! Therefore it is difficult for me to spend any time addressing problems or questions. If you post issues, I may not be able to respond to them, and it's very unlikely I will be able to make bug fixes.
> 
> While the current release on NuGet (1.3.4) is stable, there are a couple known bugs (see issues) and there are many changes since the last release in the repository. However, I am not going to publish any more official releases, since I don't have time to validate the current code base and address the known issues, or support any unforseen problems that may arise from a new release.

### Example

{{< highlight csharp >}}
public class HtmlParser
{
    /// <summary>
    /// Extract all anchor tags using CsQuery
    /// </summary>
    /// <param name="html">HTML code in string</param>
    /// <returns>List of URLs</returns>
    public IEnumerable<string> ExtractAllHrefTags(string html)
    {
        CQ cq = CQ.Create(html);
        foreach (IDomObject obj in cq.Find("a"))
        {
            yield return obj.GetAttribute("href");
        }
    }
}
{{< / highlight >}}

## [AngleSharp](https://github.com/AngleSharp/AngleSharp)

[AngleSharp](https://github.com/AngleSharp/AngleSharp) is a .NET library that gives you the ability to parse angle bracket based hyper-texts like HTML, SVG, and MathML. XML without validation is also supported by the library. An important aspect of AngleSharp is that CSS can also be parsed. The parser is built upon the official W3C specification. This produces a perfectly portable HTML5 DOM representation of the given source code. Also current features such as `querySelector` or `querySelectorAll` work for tree traversal.

### Key features

* **Portable** (designed as a portable class library)
* **Standards conform** (works exactly as in all modern browsers)
* **Great performance** (outperforms most other parsers in many cases)
* **Extensible** (extend with your own services)
* **Useful abstractions** (type helpers, jQuery like construction)
* **Fully functional DOM** (all the lists, iterators and events you love)
* **Form submission** (easily log in everywhere)
* **Navigation** (a `BrowsingContext` is like a tab - control it from .NET!).
* **LINQ enhanced** (use LINQ with DOM elements, naturally)

The advantage over similar libraries like the HtmlAgilityPack is that e.g. CSS (including selectors) is already built-in. Also the parser uses the HTML 5.1 specification, which defines error handling and element correction. The AngleSharp library focuses on standards compliance, interactivity and extensibility. It is therefore giving web developers, who are working with C#, all possibilities as they know from using the DOM in any modern browser.

The performance of [AngleSharp](https://github.com/AngleSharp/AngleSharp) is quite close to the performance of browsers. Even very large pages can be processed within milliseconds. AngleSharp tries to minimize memory allocations and reuses elements internally to avoid unnecessary object creation.

### Simple demo

{{< highlight csharp >}}
public class HtmlParser
{
    /// <summary>
    /// Extract all cells from table using AngleSharp with selector
    /// </summary>
    /// <param name="htmlSnippet"></param>
    /// <returns>List of titles from cells</returns>
    public IEnumerable<string> GetAllCellsTitles()
    {
        // Setup the configuration to support document loading
        var config = Configuration.Default.WithDefaultLoader();
        // Load the names of all The Big Bang Theory episodes from Wikipedia
        var address = "https://en.wikipedia.org/wiki/List_of_The_Big_Bang_Theory_episodes";
        // Asynchronously get the document in a new context using the configuration
        var document = await BrowsingContext.New(config).OpenAsync(address);
        // This CSS selector gets the desired content
        var cellSelector = "tr.vevent td:nth-child(3)";
        // Perform the query to get all cells with the content
        var cells = document.QuerySelectorAll(cellSelector);
        // We are only interested in the text - select it with LINQ
        var titles = cells.Select(m => m.TextContent);
    }
}
{{< / highlight >}}

## [Regex](https://msdn.microsoft.com/en-us/library/system.text.regularexpressions.regex(v=vs.110).aspx)

The worth way of parsing HTML files. Try to avoid using Regex for parsing any HTML like text as Regex is not a tool that can be used to correctly parse HTML.
But if you want.. I will should a short piece of code with get all links from some HTML string:

{{< highlight csharp >}}
public class HtmlParser
{
    public IEnumerable<string> ExtractAllAHrefTags(string html)
    {
        Regex reHref = new Regex(@"(?inx)
            <a \s [^>]*
                href \s* = \s*
                    (?<q> ['""] )
                        (?<url> [^""]+ )
                    \k<q>
                [^>]* >");
                
        foreach (Match match in reHref.Matches(html))
        {
            yield return match.Groups["url"].ToString()   
        }
    }
}
{{< / highlight >}}