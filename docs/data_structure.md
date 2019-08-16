# Package Data Description

### Introduction

This package assumes that you have 2-D matrix-shaped data stored in django models. The 
data should be structured such that there is a matrix-level model, and the data, and 
column names are associated with the specific matrix. For example:
##### Matrix1
|Col1|Col2|Col3|Col4|Col5|
|---|---|---|---|---|
| d1_1|...|...|...|...|
|...|   |   |   |   |
|...|   |   |   |   |

where:

- `>>> matrix = Matrix(Name='Matrix1')`
- `>>> Col1 = ColumnHeader(ColumnNumber=1, Matrix=matrix)`
- `>>> d1_1 = Data(Col=1, Row=1, Data=xxx, Matrix=matrix)`


Where the Django models are:

```python
from django.db import models

class Matrix(models.Model):

    Name = models.CharField(max_length=25) # no real max length


class ColumnHeader(models.Model):

    ColumnName = models.CharField(max_length=25)
    ColumnNumber = models.IntegerField()
    Matrix = models.ForeignKey(Matrix, on_delete=models.CASCADE)


class Data(models.Model):

    Col = models.IntegerField()
    Row = models.IntegerField()
    Data = models.FloatField()
    Matrix = models.ForeignKey(Matrix, on_delete=models.CASCADE)

```