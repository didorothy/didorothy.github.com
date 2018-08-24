Title: Django Admin and Three Level Model Relationships
Date: 2013-08-07
Category: Django
Tags: Django, Python
Slug: django-admin-and-three-level-model-relationships

The Django Admin app provides great functionality when roughing out a new feature in a web application.
There are a few limits that will occasionally reveal themselves.
One of these limits is creating an object with two levels of dependent related objects.

So, what do I mean by "two levels of dependent related objects?"
Look at the following Model declarations:

    :::python
    class A(models.Model):  
        title = models.CharField(max_length=100)  

    class B(models.Model):
        a = models.ForeignKey(A)  

    class C(models.Model):
        b = models.ForeignKey(B)  

Notice that C points to B and B points to A.
There are two levels (B and C) of dependent and related objects.
They are dependent in that you cannot have C without a B and you cannot have a B without an A.

Nathan Yergler describes a very similar situation [here][1].
He focused on nesting FormSets and related it to Django Models.
While his solution may work and may be a good one it appears that there are some issue with it in Django 1.5 as seen from a [Stack Overflow post][2].

Another [Stack Overflow post][3] describes this as inline inlines.
This is perhaps the best Django way of describing the issue as I am interested in it.
The first time I encountered this issue I chose to use the accepted solution from here.
Unfortunately, yesterday I found that this solution no longer worked in Django 1.5.

What I wanted was a new solution that would work in Django 1.5 that could be
used in multiple different Django applications and only require a small amount
of code to hook up.
As I thought about it I decided I wanted something that was flexible enough that I could add another Model class that was like C and related to B.

I decided I would build on the "inline inlines" concept and see if it would be possible to make it more generic.

You can see the full repository here: [https://github.com/didorothy/mlrma][4]

Basically, I created a class, MlrmaModelForm, that inherited from ModelForm that allows for you to associate multiple FormSet classes with the form in an array when you declare your class that inherits from MlrmaModelForm. Then to use that MlrmaModelForm class in an admin interface you merely define a class that inherits from MlrmaStackedInline and uses your form class.

It would look something like the following if you used the above model structure:

    :::python
    CFormSet = modelformset_factory(models.C, extra=2, can_delete=True, exclude('b',))  


    class BForm(MlrmaModelForm):  
        class Meta:  
            model = model.B  

       formset_classes = [CFormSet]  


    class BStackedInline(MlrmaStackedInline):  
        model = model.B  
        extra = 1  
        form = BForm  

    class AAdmin(admin.ModelAdmin):  
        inlines = [BStackedInline]  

    admin.site.register(models.A, AAdmin)  

I would love to hear feedback on this.
If you have improvements or criticisms let me know.

[1]: http://yergler.net/blog/2009/09/27/nested-formsets-with-django/
[2]: http://stackoverflow.com/questions/17800509/django-nested-formsets-snag
[3]: http://stackoverflow.com/questions/702637/django-admin-inline-inlines-or-three-model-editing-at-once
[4]: https://github.com/didorothy/mlrma
