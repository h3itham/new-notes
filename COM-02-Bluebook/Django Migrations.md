#### General info 
- Migrations are Django's way of managing changes to your database structure. 
- Django Models = data (blueprint) 
- Never edit migration files that have been applied to production
#### The Basic Migration Workflow
1. Generate Migration Files. 
	```bash 
	python manage.py makemigrations
	```
2. Apply Migrations to Database
	```bash
	python manage.py migrate
	```

#### The Problem  We're Facing
When we rebuild Docker containers, we lose the local migration state, but your production database still has the migration history. This can cause conflicts when Django tries to apply migrations that were already run.