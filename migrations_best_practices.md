# Best Practices for Migrations

1. Don't do it at all if you can avoid it
1. Test it thoroughly before running it on production data. A extra day up front could save you 5 days
afterwards.
1. Create backup copies of data so that you can revert to or at least know what the intial state was.
   1. Keep the backups around for long enough that you're sure there weren't any issues with the migration.
1. Log enough information to know exactly what changed.
   1. e.g. domain, app_id, form_id, question_id, old_value, new_value
