WYSIWYG, *What You See Is What You Get* software makes text editing easy. [Markdown](https://daringfireball.net/projects/markdown/) is an easy-to-read, easy-to-write text-to-html conversion tool that falls into the category of WYSIWYG content editors. The goal being to make formatted text as readable as possible - with minimal mark up (hence the name). Frequently used to format [readme](https://help.github.com/en/github/writing-on-github/about-writing-and-formatting-on-github) files and write messages in online forums, I use Markdown to blog.

[Osbornnick.com/blog](http://osbornnick.com/blog) is built using [Django](https://www.djangoproject.com/) and Markdown. The source code for the Django project described in this article is available [here](https://github.com/osbornnick/markdown_blog_example)

## Why Blog with Markdown?

Markdown is:

1. Easy
    - Markdown text looks like its formatted version, whereas HTML tags are difficult to read and visualize. A whole `<em><\em>` just to get some italics? I prefer
        - `*this*` -> *this*
        - or
        - `_this_` -> *this*
    - Great tools like [markdown preview](https://atom.io/packages/markdown-preview) for atom will format your Markdown in real time - and you can use custom css style!
2. Just plain text
    - You can write plain text (and Markdown) anywhere there's a cursor.
3. Also compatible with HTML
    - Forgot your Markdown syntax? In a pinch, just use HTML. Markdown understands both.
    - It's pretty intuitive, anyway.
4. Easily converted to HTML
    - Thanks to [Python-Markdown](https://python-markdown.github.io/), custom Markdown rendering capability in Django is easy to implement. Fancy extensions like [codehilite](https://python-markdown.github.io/extensions/code_hilite/) keeps syntax coloring simple.

I can write blog posts anywhere, easily, and with lots of flexibility. Here's how.
## Rendering Markdown with Django

In the process of building this blog, I came up with two methods of storing and rendering Markdown files as HTML blog posts. One that's quicker and another that takes up less space. As space is cheap, and speed is king, I went with the fast method.

### Quickly

The quick method uses a custom `save()` method for a `Post` model. On save, the model reads a specified Markdown file and saves it in `Post.content` as HTML. Here's how to do it from the ground up, assuming the ground means you've got Django installed.

First things first, install `pygments` and `markdown` into whatever environment you are using.

```
> pip install Pygments
> pip install markdown
```

Start a new Django project and app for our Markdown blog.
```
> django-admin startproject markdown_blog_example
> cd markdown_blog_example
.\markdown_blog_example> python manage.py startapp quick
```

Let's test that out, just to be sure that it works.

```
.\markdown_blog_example> python manage.py runserver
```
Visit "localhost:8000" on your favorite web-browser, and you should see a green rocket taking off - we're good to go!

Now that our Django directory structure is all set, let's set up some simple scaffolding for viewing our blog posts.

To use the `quick` app, add it to the list `INSTALLED_APPS` in `.\markdown_blog_example\markdown_blog_example\settings.py`, like so:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'quick'
]
```

 Then, change `urls.py` in that same directory to include the URLs patterns we'll define later:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('quick/', include('quick.urls'))
]
```

Now that that's done, the rest of our work lies in the `.\markdown_blog_example\quick\` directory. In `quick\models.py` we'll define the `Post` model class. To keep it simple, we'll give it only three class attributes to store information:
- `title`, the title of our post
- `md`, to store its Markdown
- `content`, to store the HTML converted from the Markdown file in `md`

In `quick\models.py`, this looks like :

```python
class Post(models.Model):
    title = models.CharField(max_length=255)
    md = models.FileField(null=True)
    content = models.TextField(default="", editable=False)
```
To use the markdown library in the custom `save` method, import it at the top of `models.py`:

```python hl_lines="2"
from django.db import models
import markdown
```

Now for the custom save function. This method is defined within the `Post` class.
It opens the file specified in `md`, decodes it to utf-8 so that it can be parsed by the `markdown` library, and saves it into the `content` attribute.

```python
def save(self, *args, **kwargs):
      file = self.md
      decoded = file.read().decode('utf-8')
      extensions = ['fenced_code', 'codehilite']
      output = markdown.markdown(decoded, extensions=extensions)
      self.content = output
      super().save(*args, **kwargs)
```

In sum, we should have a `models.py` file that looks like:

```python
from django.db import models
import markdown


class Post(models.Model):
    title = models.CharField(max_length=255)
    md = models.FileField(null=True)
    content = models.TextField(default="", editable=False)

    def save(self, *args, **kwargs):
        file = self.md
        decoded = file.read().decode('utf-8')
        extensions = ['fenced_code', 'codehilite']
        output = markdown.markdown(decoded, extensions=extensions)
        self.content = output
        super().save(*args, **kwargs)
```

Next is defining the URL paths for blog posts, the views to responds to web requests, and the HTML template to return.


When accessing the HTML string saved in `Post.content` within the post template, we'll need to mark it as safe, so that Django does not perform any more HTML escaping prior to output. Create a directory called `templates` in the `quick` app directory, and within `templates` create `blog.html`. Save the following within `blog.html`:

```html
<h1>{{ post.title }}</h1>

<div class="content">
  {{ post.content | safe }}
</div>
```

This is a Django template. Inside the double curly brackets, we can access attributes of models (that represent database fields). Thus, `post.content` and `post.title` are the title and content of the `Post` model.

To make `post` available to the template when it renders, we pass it in the `context` dictionary in the method that defines our view. In the `quick` app directory, create `views.py`. Save the following in `views.py`:

```python
from django.shortcuts import render
from quick.models import Post


def post(request, pk):
    post = Post.objects.get(pk=pk)

    context = {
        "post": post
    }
    return render(request, "post.html", context)
```

The view function `post` takes a Web request and returns a Web response. The request specifies the primary key for the post that we want to show the user, retrieves the correct `Post` object, and passes that objects attributes in a `context` dictionary to the Django template HTML.

We're nearly there! In order to access the `post` view, we'll need a URL configuration. Informally called a URLconf, this module is pure Python code that maps between URL path expressions and Python functions (`post`).

Create a file named `urls.py` within the `quick` app directory, and save the following into it:
```python
from django.urls import path
from . import views

urlpatterns = [
    path("post/<int:pk>/", views.post, name="post")
]
```
This defines the URL patterns within the `quick` app, such that Web requests made to `/quick/post/1` will cause Django to call the function `views.post(request, pk=1)`, rendering `post.html` with the `Post` model `post = Post.objects.get(pk=pk)`. Primary keys are auto-generated for each model by default: `id = models.AutoField(primary_key=True)`.

Now for the final step: defining metadata for the admin interface. In `./quick/admin.py` define the class `PostAdmin`:
```python
from django.contrib import admin
from quick.models import Post


class PostAdmin(admin.ModelAdmin):
    list_display = ('title', 'id')


admin.site.register(Post, PostAdmin)
```

Now, when visiting the `/admin/` URL, you'll be able to use the admin site to manage posts. To do so, you'll need admin credentials - generate these with the `createsuperuser` command in your command line:
```
markdown_blog_example> django-admin createsuperuser
```
Ta-da! Migrate your database and you're ready to start blogging with Markdown! Making your first post is simple. Simply fire up the admin site and create a new `Post` object.
### Compactly

### Bonus: Code highlighting
