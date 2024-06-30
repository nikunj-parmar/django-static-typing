# Guide to Static Typing in Django

Static typing in Django can enhance code readability and reliability. This guide will demonstrate static typing in common scenarios like fetching data from models, handling UUIDs, and functions returning multiple types.

## Table of Contents
1. [Fetching Data from Models](#fetching-data-from-models)
    - [Using `Model.objects.get()`](#using-modelobjectsget)
    - [Using `Model.objects.filter().first()`](#using-modelobjectsfilterfirst)
2. [Handling UUIDs](#handling-uuids)
3. [Functions Returning Multiple Types](#functions-returning-multiple-types)

## Fetching Data from Models

### Using `Model.objects.get()`

`Model.objects.get()` returns a single instance of the model or raises a `DoesNotExist` exception if no object is found. 

```python
from myapp.models import MyModel
from django.core.exceptions import ObjectDoesNotExist

def get_my_model_instance(pk: int) -> MyModel:
    try:
        instance = MyModel.objects.get(pk=pk)
        return instance
    except MyModel.DoesNotExist:
        raise ValueError("Instance not found")
```

### Using `Model.objects.filter().first()`

`Model.objects.filter().first()` returns the first matching instance or `None` if no objects are found.

```python
from typing import Optional
from myapp.models import MyModel
from django.db.models import QuerySet

def get_first_filtered_instance(filter_criteria: dict[str, Any]) -> Optional[MyModel]:
    return MyModel.objects.filter(**filter_criteria).first()
```

### Using `Model.objects.filter()`

`Model.objects.filter()` returns a `QuerySet` containing all matching instances.

```python
from myapp.models import MyModel
from django.db.models import QuerySet

def get_filtered_instances(filter_criteria: dict[str, Any]) -> QuerySet[MyModel]:
    return MyModel.objects.filter(**filter_criteria)
```

## Handling UUIDs

Django's `UUIDField` is used for storing UUID values. Here’s how you can handle UUIDs in your models.

```python
from uuid import UUID
from myapp.models import MyModel
from django.core.exceptions import ObjectDoesNotExist

def get_instance_by_uuid(uuid: UUID) -> MyModel:
    try:
        instance = MyModel.objects.get(uuid=uuid)
        return instance
    except MyModel.DoesNotExist:
        raise ValueError("Instance not found")
```

## Functions Returning Multiple Types

For functions that can return different types, use `Union` from the `typing` module.

### Example: Returning `None` or an Instance

```python
from typing import Union
from myapp.models import MyModel

def get_instance_or_none(pk: int) -> Union[MyModel, None]:
    return MyModel.objects.filter(pk=pk).first()
```

### Example: Returning `None`, a Single Instance, or a QuerySet

```python
from typing import Union
from myapp.models import MyModel
from django.db.models import QuerySet

def get_instance_or_queryset(filter_criteria: dict[str, Any]) -> Union[None, MyModel, QuerySet[MyModel]]:
    instances = MyModel.objects.filter(**filter_criteria)
    if instances.exists():
        if instances.count() == 1:
            return instances.first()
        else:
            return instances
    return None
```

## Conclusion

Using static typing in Django helps to define expected data types, making your code more maintainable and less prone to errors. By following these examples and incorporating type hints into your Django projects, you can improve the quality of your codebase.
