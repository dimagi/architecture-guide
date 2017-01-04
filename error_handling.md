# Showing Errors to our Users


## "There was an error."

If you're writing a new feature, it's human nature to start by thinking "assuming my user does everything correctly, let's make it do the right thing". Once you're done, you'll notice that when the user does the wrong thing, you'll want it to do something better than 500. So maybe you have it tell the user "There was an eror.", and tell the user to try again.

I think anyone who's been on both sides of this can see how frustrating an experience that is,
not just for the user, but for our support team and the later developers who have to go debug someone's very routine problem.
The more complex the input, the more ways for it to go wrong, and the more helpless the user is in the case that everything doesn't go perfectly
on the first try.

## How to do better

It's clear that we can and should do better, but how? What should the experience be for the user,
and what guidelines should we follow to get there? I'll use those two questions as an outline for this document.

I will in these guidelines use the example of a bulk upload—there's a format the user is supposed to use and a number of ways known and unknown to mess it up.


### A change of perspective on user errors

Our first mistake is viewing user error as some sort of aberration, something that happens, but shouldn't, or if it has to happen, then at least rarely. In this imaginary world, error handling isn't part of the feature; it's an afterthought.

In reality, user error is the norm, and should be the default assumption. We didn't come out of the womb knowing how to multiply three digit numbers; we had to be taught by someone (1) showing us how to do it and (2) pointing out our mistakes. And since we don't get much practice, most of us probably still can't reliably multiply two three digit numbers, and if given 10 problems would make a mistake in at least one of them. The same is true of whatever your software asks of your users, except your software (and not their parent or 2nd grade teacher) is responsible for helping and training them. And even an expert is going to make a mistake one in 10 times, and they're going to be pretty frustrated that one time if you don't tell them exactly what's wrong.

In this world, the one we actually live in, user error is not a peripheral aspect of your feature—it is part of the payload. And like any feature, your error handling should be the subject of automated tests, and you should be able to know in advance what the error output of a particular mistake will be. When you rewrite the implemenation of the execution step, the messages showed to your user shouldn't unexpectedly change. They're user-facing, and they're part of the feature.


### Guidelines for the User Experience

When your user input has problems the error handling should be

- **Immediate**: they should see the errors upfront, and as quickly as possible
- **Comprehensive**: _all_ of the errors in the input, not just the first one
- **Without side effects**: no models will be saved or actions partially completed, if a user sees an error,
    then error handling is the *only* thing that has happened
- **Predictable**: If something unexpected and unforeseen (by the designer of the error handling) goes wrong,
    that is a failure of the software and an engineer must investigate and respond by adding an error case for the previously unexpected error.


### Guidelines for the backend

1. Have a validation step that is distinct from the execution step
2. Have the validation step aggressively and eagerly check for potential problems,
   keeping a running list of errors as it validates the entirety of the input.
3. Keep all possible error messages listed together, and write a test for your validator that includes a case for each known error


### Common mistakes

Mistake #1: Catch all errors with a blanket catch (`except Exception:` in python) so that your process can keep going even if there's an error.

This is by far the most common mistake. When you do this, what you are saying is "I don't want to know when something goes wrong". Never use `except Exception:` for this purpose; instead, let the code fail, let the error be logged, and let whoever's on call then fix the problem or ask you to fix it.

It's true that you won't be able to foresee all possible errors the user could make, or all possible ways in which your code won't execute correctly. But it's much better to build a list of known ways something can go wrong and expect to add to it as users find new ways to break your feature than it is to silence exceptions and make users file a ticket when something goes wrong.

Mistake #2: When something fails, catch the error as `e` and show them `e.message`.

Exception messages have as their audience the programmers who will be writing and debugging your feature. Error messages you display have as their audience the person using your feature. These audiences could not be more different! Bake it into your feature from the start that there are help messages crafted for users (that can be translated, etc.) that are separate from exception messages.

Mistake #3: Assume user input is correct, begin executing against it, and then show an error if something goes wrong

For very simple input, this can be fine, but for something like a bulk upload, in which you loop through many actions, this means that much of the request will go through even as some of it fails. This is confusing and dangerous behavior. It's much better to have separate validation and execution steps: first make sure that the user's input is completely valid and can be executed against without issue; then exectute against it. If anything shows up during the validation step, you can compile a list of all problems with the input to show the user at once.
