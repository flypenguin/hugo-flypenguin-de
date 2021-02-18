---
title: Misc Django I – forms
author: penguin
type: post
date: 2018-10-17T11:06:46+00:00
url: /2018/10/17/misc-django-i-forms/
categories:
  - Uncategorized
tags:
  - dev
  - django
  - python
  - snippet

---
## Custom form errors

If you want to validate something in the view, and return with a custom error message in the same form, you can use the "[Form.add_error][1](fieldname, errorstring)" method. And then, of course, return to the previous template.

```
class MyView(View):
    def get(self, request):
        data = form.cleaned_data
        if len(res) &gt; 0:
            form.add_error( 'login', "Diese Personalnummer existiert bereits!")
        return render(request, 'my_template.html', {'form': form})
```

## Dynamic choice fields in forms

You want a form which fills its choice field from the database? And if the database changes, if you reload the page, the form should change as well? Of course! Django got you covered.

```
class UserForm(forms.Form):
    def __init__(self, *args, **kwargs):
        super(UserForm, self).__init__(*args, **kwargs)
        self.fields['site'] = forms.ModelChoiceField( label="Site", queryset=Site.objects.all().order_by('name'), )
        for field in ('department', 'office', 'phone'):
            self.fields.move_to_end(field)

    login = forms.CharField(label="Login")
    email = forms.EmailField(label="Email")
    site = None # this is set in __init__() :)
    department = forms.CharField(label="Department")
    office = forms.CharField(label="Office")
    phone = forms.CharField(label="Phone")
```

... now, why the "for field in ('department' ...)" line you ask?

Simple. The fields dict is an OrderedDict. If you replace a field it is appended to the end again. So in the form the "Site" input box would be displayed last, although it makes more sense to display it where it is in the original definition.

Using ".move\_to\_end()" you can re-adjust this. If someone knows a better method ... feel free to tell me.

(Sources: [here][2])

 [1]: https://docs.djangoproject.com/en/2.1/ref/forms/api/#django.forms.Form.add_error
 [2]: http://www.ilian.io/django-forms-choicefield-with-dynamic-values/