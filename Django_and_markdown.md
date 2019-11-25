WYSIWYG, *What You See Is What You Get* software makes text editing easy. [Markdown](https://daringfireball.net/projects/markdown/) is an easy-to-read, easy-to-write text-to-html conversion tool that falls into the category of WYSIWYG content editors. The goal being to make formatted text as readable as possible - with minimal mark up (hence the name). Frequently used to format [readme](https://help.github.com/en/github/writing-on-github/about-writing-and-formatting-on-github) files and write messages in online forums, I use Markdown to blog.

[Osbornnick.com/blog](http://osbornnick.com/blog) is built using [Django](https://www.djangoproject.com/) and Markdown.

## Why Blog with Markdown?

Markdown is:

1. Easy
    - Markdown text looks like its formatted version, whereas HTML tags are difficult to read and visualize. A whole `<em><\em>` just to get some italics? I prefer
        - `*this*` -> *this*
        - or
        - `_this_` -> *this*
    - Great tools like [markdown preview](https://atom.io/packages/markdown-preview) for atom will format your markdown in real time - and you can use custom css style!
2. Just plain text
    - You can write plain text (and Markdown) anywhere there's a cursor.
3. Also compatible with HTML
    - Forgot your Markdown syntax? In a pinch, just use HTML. Markdown understands both.
    - It's pretty intuitive, anyway.
4. Easily converted to HTML
    - Thanks to [Python-Markdown](https://python-markdown.github.io/), custom Markdown rendering capability in Django is easy to implement. Fancy extensions like [codehilite](https://python-markdown.github.io/extensions/code_hilite/) keeps syntax coloring simple.

I can write blog posts anywhere, easily, and with lots of flexibility. Here's how.
## Rendering Markdown with Django
### Quickly

### Compactly
