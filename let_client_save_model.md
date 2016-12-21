# Best Practice: Let the client code save the model

Django models often have helper methods that update their own fields, sometimes a couple at a time with some logic.
Every time you call that method, you're going to want it to save, right? So why not just put a `self.save` in there at the end? 

I see this pattern sometimes, and it causes enough annoyance that I would consider it an antipattern.

Let me illustrate with an example. Let's say you have code like this in `models.py`:

```python
class Person(models.Model):
    name = models.CharField(max_length=256)

    ...

    def set_name(self, name):
        if self.name != name:
            self.name = name
            self.save()
```
The method itself is a little silly, but it does do one important thing: it doesn't save if there's no change to save,
which in a loop could matter by reducing the number of calls to the db.

Then in the client code you would have

```python
person.set_name('martha')
```

The main thing I don't like about this is that it doesn't let the client do basic planning about when to save.

```python
person.age = 17
person.set_name('martha')
person.zodiak = 'dragon'
if everything_went_well():
    person.save()
```
In the code above, two things are wrong. Maybe the most obvious is that person gets saved twice when once will do.
The other is that whether or not `everything_went_well()`, the `age=17` and `name='martha'` changes will be saved. In general, it takes away the client code's disgression over whether and when to save the changes it's orchestrating. Given that wanting to have control over when a model saves is such a common part of dealing with database models, taking that control away from the client code can be very frustrating.

In the above example, I would suggest the following:

```python
class Person(models.Model):
    name = models.CharField(max_length=256)

    ...

    def set_name(self, name):
        if self.name != name:
            self.name = name
            return True
        else:
            return False
```

In the simplest case (where you just want to set the name and save if necessary), the client code then looks like:

```python
if person.set_name('martha'):
    person.save()
```

In a the longer example, the client code can use the same code

```python
person.age = 17
person.set_name('martha')
person.zodiak = 'dragon'
if everything_went_well():
    person.save()
```
but this time it'll only save once, and only if `everything_went_well()`.
