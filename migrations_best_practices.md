# Best Practices for Migrations

1. Don't do it at all if you can avoid it
1. Test it thoroughly before running it on production data. An extra day up front could save you 5 days
afterwards.
1. Create backup copies of data so that you can revert to or at least know what the intial state was.
   1. Keep the backups around for long enough that you're sure there weren't any issues with the migration.
1. Log enough information to know exactly what changed.
   1. e.g. domain, app_id, form_id, question_id, old_value, new_value
1. If possible, include the ability to run a "dryrun" so you can see output of changes without making any.
1. Create the ability to run the migration in small batches.
1. Ensure strict data validation of the assumptions made about the migrating data.
1. If possible, make migration of the datum atomic and have any error cancel the migration of that datum.
