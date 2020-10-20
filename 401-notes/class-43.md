# Getting Stylish with Sass
## [Django SASS Processor](https://github.com/jrief/django-sass-processor#introduction)

### Installation
pip install libsass django-compressor django-sass-processor

### Configuration
In settings.py add to:

INSTALLED_APPS = [\
    ...\
    'sass_processor',\
    ...\
]

If the directory referred by SASS_PROCESSOR_ROOT does not exist, then django-sass-processor creates it. This does not apply, if SASS_PROCESSOR_ROOT is unset and hence defaults to STATIC_ROOT. Therefore it is a good idea to otherwise use SASS_PROCESSOR_ROOT = STATIC_ROOT in your settings.py.

STATICFILES_FINDERS = [
    'django.contrib.staticfiles.finders.FileSystemFinder',\
    'django.contrib.staticfiles.finders.AppDirectoriesFinder',\
    'sass_processor.finders.CssFinder',
    ...
]

Optionally, add a list of additional search paths, the SASS compiler may examine when using the @import "..."; statement in SASS/SCSS files:

import os

SASS_PROCESSOR_INCLUDE_DIRS = [
    os.path.join(PROJECT_PATH, 'extra-styles/scss'),
    os.path.join(PROJECT_PATH, 'node_modules'),
]

If inside of your SASS/SCSS files, you also want to import (using @import "path/to/scssfile";) files which do not start with an underscore, then you can configure another Regex pattern in your settings, for instance:

SASS_PROCESSOR_INCLUDE_FILE_PATTERN = r'^.+\.scss$'

Integer SASS_PRECISION sets floating point precision for output css. libsass' default is 5. Note: bootstrap-sass requires 8, otherwise various layout problems will occur.

SASS_PRECISION = 8

SASS_OUTPUT_STYLE sets coding style of the compiled result, one of compact, compressed, expanded, or nested. Default is nested for DEBUG and compressed in production.

Note: libsass-python 0.8.3 has problem encoding result while saving on Windows, the issue is already fixed and will be included in future pip package release, in the meanwhile avoid compressed output style.

SASS_OUTPUT_STYLE = 'compact'

### Jinja2 support
sass_processor.jinja2.ext.SassSrc is a Jinja2 extension. Add it to your Jinja2 environment to enable the tag sass_src, there is no need for a load tag. Example of how to add your Jinja2 environment to Django:

In settings.py:

TEMPLATES = [{
    'BACKEND': 'django.template.backends.jinja2.Jinja2',
    'DIRS': [],
    'APP_DIRS': True,
    'OPTIONS': {
        'environment': 'yourapp.jinja2.environment'
    },
    ...
}]

### In your Django templates
{% load sass_tags %}

'<'link href="{% sass_src 'myapp/css/mystyle.scss' %}" rel="stylesheet" type="text/css" />

### In Media classes or properties
In Python code, you can access the API of the SASS processor directly. This for instance is useful in Django's admin or form framework.

from sass_processor.processor import sass_processor

class SomeAdminOrFormClass(...):
    ...
    class Media:
        css = {
            'all': [sass_processor('myapp/css/mystyle.scss')],
        }

### Offline compilation
If you want to precompile all occurrences of your SASS/SCSS files for the whole project, on the command line invoke:

./manage.py compilescss
This is useful for preparing production environments, where SASS/SCSS files can't be compiled on the fly.

To simplify the deployment, the compiled *.css files are stored side-by-side with their corresponding SASS/SCSS files. After compiling the files run

./manage.py collectstatic
as you would in a normal deployment.

In case you don't want to expose the SASS/SCSS files in a production environment, deploy with:

./manage.py collectstatic --ignore=*.scss
To get rid of the compiled *.css files in your local static directories, simply reverse the above command:

./manage.py compilescss --delete-files

Or you may compile results to the SASS_PROCESSOR_ROOT directory directy (if not specified - to STATIC_ROOT):

./manage.py compilescss --use-processor-root

### Heroku
If you are deploying to Heroku, use the heroku-buildpack-django-sass buildpack to automatically compile scss for you.

### Development
To run the tests locally, clone the repository, create a new virtualenv, activate it and then run these commands:

cd django-sass-processor
pip install tox
tox

## [SASS](https://sass-lang.com/guide)

## [SMACSS](http://smacss.com/book/)
### Base Rules
Base styles include setting heading sizes, default link styles, default font styles, and body backgrounds. There should be no need to use !important in a Base style.\
***CSS Resets***\
A CSS Reset is a set of Base styles designed to strip out—or reset—the default margin, padding, and other properties. Its purpose is to define a consistent foundation across browsers to build the site on.

### Layout Rules
Layout styles can also be divided into major and minor styles based on reuse. Major layout styles such as header and footer are traditionally styled using ID selectors but take the time to think about the elements that are common across all components of the page and use class selectors where appropriate.

### Module Rules
When defining the rule set for a module, avoid using IDs and element selectors, sticking only to class names. A module will likely contain a number of elements and there is likely to be a desire to use descendent or child selectors to target those elements.\
***Avoid element selectors***\
Use child or descendant selectors with element selectors if the element selectors will and can be predictable. Using .module span is great if a span will predictably be used and styled the same way every time while within that module.
### State Rules
Isnʼt it just a module?
There is plenty of similarity between a sub-module style and a state style. They both modify the existing look of an element. However, they differ in two key ways:

State styles can apply to layout and/or module styles; and
State styles indicate a JavaScript dependency.
It is this second point that is the most important distinction. Sub-module styles are applied to an element at render time and then are never changed again. State styles, however, are applied to elements to indicate a change in state while the page is still running on the client machine.

For example, clicking on a tab will activate that tab. Therefore, an is-active or is-tab-active class is appropriate. Clicking on a dialog close button will hide the dialog. Therefore, an is-hidden class is appropriate.

### Theme Rules
Theme Rules aren't as often used within a project and because of that, they aren't included as part of the core types. Some projects may have a need for them, though, as we did when working on Yahoo! Mail.