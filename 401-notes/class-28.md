# Django CRUD and Forms

The form is defined in HTML as a collection of elements inside \<form>...\</form> tags, containing at least one input element of type="submit".

\<form action="/team_name_url/" method="post"> \
    \<label for="team_name">Enter name: </label> \
    \<input id="team_name" type="text" name="name_field" value="Default name for team."> \
    \<input type="submit" value="OK"> \
\</form>

The submit input will be displayed as a button (by default) that can be pressed by the user to upload the data in all the other input elements in the form to the server (in this case, just the team_name). The form attributes define the HTTP method used to send the data and the destination of the data on the server (action):

* action: The resource/URL where data is to be sent for processing when the form is submitted. If this is not set (or set to an empty string), then the form will be submitted back to the current page URL.
* method: The HTTP method used to send the data: post or get.
* The POST method should always be used if the data is going to result in a change to the server's database because this can be made more resistant to cross-site forgery request attacks.
* The GET method should only be used for forms that don't change user data (e.g. a search form). It is recommended for when you want to be able to bookmark or share the URL.

## Declaring a Form
The declaration syntax for a Form is very similar to that for declaring a Model, and shares the same field types (and some similar parameters). This makes sense because in both cases we need to ensure that each field handles the right types of data, is constrained to valid data, and has a description for display/documentation.

Form data is stored in an application's forms.py file, inside the application directory. Create and open the file locallibrary/catalog/forms.py. To create a Form, we import the forms library, derive from the Form class, and declare the form's fields. A very basic form class for our library book renewal form is shown below — add this to your new file:

from django import forms
    
class RenewBookForm(forms.Form): \
    renewal_date = forms.DateField(help_text="Enter a date between now and 4 weeks (default 3).")

## View

import datetime

from django.shortcuts import render, get_object_or_404
from django.http import HttpResponseRedirect
from django.urls import reverse

from catalog.forms import RenewBookForm

def renew_book_librarian(request, pk):
    book_instance = get_object_or_404(BookInstance, pk=pk)

    # If this is a POST request then process the Form data
    if request.method == 'POST':

        # Create a form instance and populate it with data from the request (binding):
        form = RenewBookForm(request.POST)

        # Check if the form is valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required (here we just write it to the model due_back field)
            book_instance.due_back = form.cleaned_data['renewal_date']
            book_instance.save()

            # redirect to a new URL:
            return HttpResponseRedirect(reverse('all-borrowed') )

    # If this is a GET (or any other method) create the default form.
    else:
        proposed_renewal_date = datetime.date.today() + datetime.timedelta(weeks=3)
        form = RenewBookForm(initial={'renewal_date': proposed_renewal_date})

    context = {
        'form': form,
        'book_instance': book_instance,
    }

    return render(request, 'catalog/book_renew_librarian.html', context)

## Views
Open the views file (locallibrary/catalog/views.py) and append the following code block to the bottom of it:

from django.views.generic.edit import CreateView, UpdateView, DeleteView
from django.urls import reverse_lazy

from catalog.models import Author

class AuthorCreate(CreateView):
    model = Author
    fields = '__all__'
    initial = {'date_of_death': '05/01/2018'}

class AuthorUpdate(UpdateView):
    model = Author
    fields = ['first_name', 'last_name', 'date_of_birth', 'date_of_death']

class AuthorDelete(DeleteView):
    model = Author
    success_url = reverse_lazy('authors')

## URL configurations
Open your URL configuration file (locallibrary/catalog/urls.py) and add the following configuration to the bottom of the file:

urlpatterns += [  
    path('author/create/', views.AuthorCreate.as_view(), name='author_create'),
    path('author/<int:pk>/update/', views.AuthorUpdate.as_view(), name='author_update'),
    path('author/<int:pk>/delete/', views.AuthorDelete.as_view(), name='author_delete'),
] 

## Overriding methods in class-based views, adding new data
While we don't need to do so here, you can also override some of the class methods.

For example, we can override the get_queryset() method to change the list of records returned. This is more flexible than just setting the queryset attribute as we did in the preceding code fragment (though there is no real benefit in this case):

class BookListView(generic.ListView):
    model = Book

    def get_queryset(self):
        return Book.objects.filter(title__icontains='war')[:5] # Get 5 books containing the title war
We might also override get_context_data() in order to pass additional context variables to the template (e.g. the list of books is passed by default). The fragment below shows how to add a variable named "some_data" to the context (it would then be available as a template variable).

class BookListView(generic.ListView):
    model = Book

    def get_context_data(self, **kwargs):
        # Call the base implementation first to get the context
        context = super(BookListView, self).get_context_data(**kwargs)
        # Create any data and add it to the context
        context['some_data'] = 'This is just some data'
        return context

## Pagination 
Open catalog/views.py, and add the paginate_by line shown in bold below.

class BookListView(generic.ListView):
    model = Book
    paginate_by = 10

Open /locallibrary/catalog/templates/base_generic.html and copy in the following pagination block below our content block (highlighted below in bold). The code first checks if pagination is enabled on the current page. If so then it adds next and previous links as appropriate (and the current page number). 

{% block content %}{% endblock %}
  
  {% block pagination %}
    {% if is_paginated %}
        \<div class="pagination"> \
            \<span class="page-links"> \
                {% if page_obj.has_previous %} \
                    \<a href="{{ request.path }}?page={{ page_obj.previous_page_number }}">previous</a> \
                {% endif %} \
                \<span class="page-current">
                    Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}. \
                \</span>
                {% if page_obj.has_next %} \
                    \<a href="{{ request.path }}?page={{ page_obj.next_page_number }}">next</a> \
                {% endif %} \
            \</span> \
        \</div> \
    {% endif %} \
  {% endblock %} 