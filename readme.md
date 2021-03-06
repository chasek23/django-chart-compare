# Chart Compare


This is meant to be a distributed package for use in allowing chart and graphical comparisons from numerically based django models.

Detailed documentation is in the "docs" directory.

## Quick start



### Imports


1. `from django_chart_compare import Utils`

or:

2. `from django_chart_compare import Compare`


### Uses


For comparing base data from multiple parent models, you can use the compare module.

Implementation example:

views.py:
```python
from django_chart_compare import Compare
from django.views import generic
from .models import Schedule
...
class ComparisonGraphs(generic.TemplateView):

    model = Schedule # your matrix model
    template_name = 'app/multi.html'
    context_object_name = 'chart'

    def get_context_data(self, **kwargs):
        context = super(ComparisonGraphs, self).get_context_data(**kwargs)
        comp = Compare(Schedule, self.request, **kwargs)
        script, div = comp.lane_comp_plotter()
        context['html'] = comp.df.to_html()
        context['col_dict'] = comp.col_dict
        context['name_dict'] = comp.name_dict
        context['script'] = script
        context['div'] = div
        context['schedule'] = Schedule.objects.get(pk=self.kwargs['pk'])
        return context
```
urls.py:
 The view above needs   self.get_context_data() to receive kwargs of pk and slug, where pk is the id of the primary model
 and the slug is a string with dashes ("-") separating the id's of the comparison models.
```python

url_patterns = [
    
    ...

    url(r'^schedule/(?P<pk>[0-9_]+)/compare/(?P<slug>[0-9]+(-[0-9]+)*)/more-reports/$',
        views.ComparisonGraphs.as_view(), name='more-reports'),

    # or if using path in django 2
    
    path('test/<int:pk>/<slug:slug>/', ComparisonGraphs.as_view(), name='test'),

    ]
    

```



And in app/multi.html:

```djangotemplate
<form action="" method="GET" class="form-horizontal">
    <!-- iterate through column values to list all possible selections for comparison -->
    {% for pk, sched_columns in col_dict.items %}
        <div class="ColumnLists" >
            {{ name_dict|get_item:pk }} <br>
            <select class="ColumnLists" size="{{ sched_columns|length }}" name="{{ pk }}">
                {% for key, col in sched_columns.items %}
                <option value="{{ key }}" >
                    <td>{{ col }}</td>
                </option>
                {% endfor %}
            </select>
        </div>
    {% endfor %}
</form>
```