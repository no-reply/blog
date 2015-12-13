---
layout: post
title:  "Example Post"
date:  2015-12-12 15:23:37 -0800
excerpt: This example post serves as documentation for this site.
keywords: kramdown, mathjax, syntax highlighting, tufte css

---
<aside class='sidenote' markdown='1'>
_Special thanks to [Dave Liepmann](https://edwardtufte.github.io/tufte-css/#) for his work on Tufte CSS, which this layout borrows from significantly._
</aside>

This is an example post and quasi-documentation for this site. The site is based on [Jekyll](http://jekyllrb.com) and supports the [`kramdown`](http://kramdown.gettalong.org/) syntax, and [MathJax](http://mathjax.org).

This is version `0.0.1` of the blog documentation.


## Blockquotes & Epigraphs

> Sets of triples are called RDF graphs: intuitively, each triple can be viewed as an edge from
> node s to node o with label p. Here, s, o, and p are constant symbols – uniform resource
> identifiers (URIs) or literals (e.g., strings) – or anonymous identifiers, called blank nodes.
> Consider, for example, the graphs \\( S = \\{ ho, rdf:type, Ai,hA, rdfs:subClassOf, Bi \\} \\) and
> \\( E = \\{ ho, rdf:type, Bi \\} \\). Hayes (2004) defines notions of RDF and RDFS entailment.
> <footer><a href=""><cite>Logical Foundations of RDF(S) with Datatypes</cite></a></footer>
{: .epigraph}

Blockquotes can be styled as epigraphs by including the class `.epigraph`, as above. Incude a
`<footer>`, ideally with a `<cite>` tag to achive the attribution styles.

Normal blockquotes appear as follows:

> This is an example of a blockquote.


## Syntax Highlighting

Use syntax highlighting by including a Ruby block with `highlight [language]`. This creates a `<figure>` with the highlight styles and a nested `<code>` block.

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}


## Figures & Sidenotes

The article text is set slightly to the right. This creates space for numbered sidenotes and general margin notes. Both forms of notes can collapse into the main body text when displayed on a narrow screen.

<aside class='sidenote'>Margin notes may contain figures, etc&hellip;</aside>

Sidenotes and margin notes should be included inline. Normal margin notes are marked up as `<aside class=".sidenote">`, and numbered sidenotes as `<span class="sidenote numbered">`.

Sidenotes<label for="sidenote-1" class='sidenote-number'></label><input type="checkbox" id="sidenote-toggle-1" class="margin-toggle"><span id='sidenote-1' class='sidenote numbered' markdown='1'>this is a&hellip;y ![exports/imports](https://edwardtufte.github.io/tufte-css/img/exports-imports.png) </span> are displayed in the right margins. Use `<label for='sidenote-id" class="sidenote-number"></label>` to automatically display, increment, and format the number. On mobile screens the note number will act as a display toggle if `<input type="checkbox" id="sidenote-toggle-1" class="margin-toggle">` is included. The sidenote<label for="sidenote-2" class='sidenote-number'></label><input type="checkbox" id="sidenote-toggle-2" class="margin-toggle"><span id='sidenote-2' class='sidenote numbered'>&hellip; sidenote</span> is not displayed until the user accesses it explictly.

Margin notes are always displayed on mobile screens.

Figures may be included, with or without captions, and may include margin notes.

<figure markdown="1">
<aside class='sidenote' markdown='1'>
This is a margin note relevant to the figure.
</aside>
![exports/imports](https://edwardtufte.github.io/tufte-css/img/exports-imports.png)
<figcaption>
From Edward Tufte, <em>Visual Display of Quantitative Information</em>, page 92.
</figcaption>
</figure>

## Math

Inline math expressions can be included using `\\( [exp] \\)`. For example `\\( sin(x^2) \\)` yields: \\( sin(x^2) \\). Math in block form uses `$$`:

$$a^2 + b^2 = c^2$$

## Metadata

YAML "front matter" is included in the normal `Jekyll` fashion. `keywords` are displayed underneath the title and date, if given. An `excerpt` will be used in the page `<head>` to provide description metadata.

{% highlight yaml %}
layout: post
title:  "Example Post"
date:  2015-12-12 15:23:37 -0800
excerpt: This example post serves as documentation for this site.
keywords: kramdown, mathjax, syntax highlighting, tufte css
{% endhighlight %}

## Notes

