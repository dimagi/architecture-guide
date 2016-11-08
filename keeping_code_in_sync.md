# Keeping Code in Sync during a Split

## When to use

- you're reworking a feature/UI
- not rewriting an entire django app from scratch, but rather re-working some part of it (say, restyling all the templates but keeping most of the backend the same, etc.)
- it's a big enough change that you want to the old and new to live side by side so you can write the new one under a toggle and control the roll-out
- you've decided the best way to go is to copy-paste-and-edit some portion of the code so there's a clean top-level if TOGGLE then OLD CODE else NEW CODE.

## What to do

1. Recognize that absent an effective plan, the two versions of code will grow apart unintentionally—changes will be made to the live (old) version without the equivalent change being made to the new, hidden, version.
2. Organize your files in such a way as to isolate the split code in parallel directory structures with roots called v1/ and v2/ or the appropriate equivalent—what's important is that the structure and naming makes clear which old and new files are analogous.
3. Write automated tests that will fail if someone changes one file but not the other of a pair, usually by asserting something about the diff between `v1/fileA` and `v2/fileA`—in the simplest case you will put the full pairwise file diffs somewhere under `tests/` and then your test will assert that the diff between `v1/fileA` and `v2/fileA` matches the stored version.
4. Make sure that when the tests fail, the message includes actionable instructions for how to fix, that are easy to follow without deep understanding of the area or knowledge of the split. This will include (a) what the intention is (keeping the files in sync) and (b) what command to run to regenerate the diff once you've applied the chagne to both and the diff is still not matching.
5. Write a test that fails if a test is missing, so that if you add another file to the v1/ v2/ split and forget to add a test for it, the build will fail.

## Example

Let's say you're overhauling the app builder UI. This may include both navigational changes and styling changes, and it's important to roll the changes out together because either one by itself will already be a lot for users to adjust to. It's also going to be a huge change code-wise, and we'll also want to roll it out slowly to different users, so it's important not to just make the change in a big PR and merge it in, because it'll be really difficult to keep it in sync with master, and we'd only have the option of deploying it to all users at once.

Most of the `app_manager` code is left unchanged; nearly all the backend code, for instance, remains basically unchanged. But the parts that are going to be different are going to have a lot of the analogous content, but with major differences scattered throughout. Styling in particular is something that inevitably invovles changing all Xs to Ys throughout a template. So we decide the best way to handle this is to isolate the templates where all the changes are happening and split them into v1 and v2 directories, with v2 beginning as an exact copy of v1, and then applying the styling changes to v2.

A lot is going to stay the same between v1 and v2. For example, the same input boxes for editing a form's display condition, etc., are going to be there—they're just going to be in included in a different place and styled differently.

In order to automatically keep tabs on the diff between v1/* and v2/*, we write a test for each file that compares the diff between the v1 and v2 version to a stored diff and fails if the diffs are different. This is nice because it'll fail if you change one file but not the other, and when you do make changes to both, you are forced explictly to audit how the change was applied to both, and to commit that as well (as the new stored diff).

## Links

- This convention was first proposed on Oct. 21, 2016 after an architecture review in [App Manager V2: Development Plan -- Google Doc](https://docs.google.com/document/d/1KT3ieIA-RDpIbofKt2RdYnX9-f3qGjwxr6rD9fSThMc/edit#)
