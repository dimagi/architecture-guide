# Best Practice: Let the client code save the model

Django models often have helper methods that update their own fields, sometimes a couple at a time with some logic.
Every time you call that method, you're going to want it to save, right? So why not just put a `self.save` in there at the end? 

I see this pattern sometimes, and it causes enough annoyance that I would consider it an antipattern.
