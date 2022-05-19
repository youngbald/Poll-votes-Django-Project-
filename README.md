# Poll-votes-Django-Project-

This project is created based on Django python, process followed by https://docs.djangoproject.com/en/4.0/intro/
It gives you basic understanding about Django, notice that the database used is SQLlite.
There are many built in documents, it makes you build your website more convinience and easy to understand.

---

This document contains commands to run the program, and output for these commands.

py -m django --version
django-admin startproject mysite
py manage.py runserver
py manage.py runserver 8080
py manage.py runserver 0:8000
py manage.py startapp polls
py manage.py runserver
http://localhost:8000/polls/
python manage.py migrate
Getting runtime help¶
django-admin help¶
Run django-admin help to display usage information and a list of the commands provided by each application.

Run django-admin help --commands to display a list of all available commands.

Run django-admin help <command> to display a description of the given command and a list of its available options.
django-admin check auth admin myapp
django-admin check --tag models --tag compatibility
django-admin check --database default --database other
python manage.py migrate //for database
python manage.py makemigrations polls //for changes to models
python manage.py sqlmigrate polls 0001 //return sql and migration name
looks like below, for postgreSQL
BEGIN;
--
-- Create model Question
--
CREATE TABLE "polls_question" (
"id" serial NOT NULL PRIMARY KEY,
"question_text" varchar(200) NOT NULL,
"pub_date" timestamp with time zone NOT NULL
);
--
-- Create model Choice
--
CREATE TABLE "polls_choice" (
"id" serial NOT NULL PRIMARY KEY,
"choice_text" varchar(200) NOT NULL,
"votes" integer NOT NULL,
"question_id" integer NOT NULL
);
ALTER TABLE "polls_choice"
ADD CONSTRAINT "polls_choice_question_id_c5b4b260_fk_polls_question_id"
FOREIGN KEY ("question_id")
REFERENCES "polls_question" ("id")
DEFERRABLE INITIALLY DEFERRED;
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");

COMMIT;

Run python manage.py makemigrations to create migrations for those changes
python manage.py sqlmigrate polls 0001
Run python manage.py migrate to apply those changes to the database.

python manage.py shell //play with api

> > > from polls.models import Choice, Question # Import the model classes we just wrote.

# No questions are in the system yet.

> > > Question.objects.all()
> > > <QuerySet []>

# Create a new Question.

# Support for time zones is enabled in the default settings file, so

# Django expects a datetime with tzinfo for pub_date. Use timezone.now()

# instead of datetime.datetime.now() and it will do the right thing.

> > > from django.utils import timezone
> > > q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.

> > > q.save()

# Now it has an ID.

> > > q.id
> > > 1

# Access model field values via Python attributes.

> > > q.question_text
> > > "What's new?"
> > > q.pub_date
> > > datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# Change values by changing the attributes, then calling save().

> > > q.question_text = "What's up?"
> > > q.save()

# objects.all() displays all the questions in the database.

> > > Question.objects.all()
> > > <QuerySet [<Question: Question object (1)>]>
> > > from polls.models import Choice, Question

# Make sure our **str**() addition worked.

> > > Question.objects.all()
> > > <QuerySet [<Question: What's up?>]>

# Django provides a rich database lookup API that's entirely driven by

# keyword arguments.

> > > Question.objects.filter(id=1)
> > > <QuerySet [<Question: What's up?>]>
> > > Question.objects.filter(question_text\_\_startswith='What')
> > > <QuerySet [<Question: What's up?>]>

# Get the question that was published this year.

> > > from django.utils import timezone
> > > current_year = timezone.now().year
> > > Question.objects.get(pub_date\_\_year=current_year)
> > > <Question: What's up?>

# Request an ID that doesn't exist, this will raise an exception.

> > > Question.objects.get(id=2)
> > > Traceback (most recent call last):

    ...

DoesNotExist: Question matching query does not exist.

# Lookup by a primary key is the most common case, so Django provides a

# shortcut for primary-key exact lookups.

# The following is identical to Question.objects.get(id=1).

> > > Question.objects.get(pk=1)
> > > <Question: What's up?>

# Make sure our custom method worked.

> > > q = Question.objects.get(pk=1)
> > > q.was_published_recently()
> > > True

# Give the Question a couple of Choices. The create call constructs a new

# Choice object, does the INSERT statement, adds the choice to the set

# of available choices and returns the new Choice object. Django creates

# a set to hold the "other side" of a ForeignKey relation

# (e.g. a question's choice) which can be accessed via the API.

> > > q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.

> > > q.choice_set.all()
> > > <QuerySet []>

# Create three choices.

> > > q.choice_set.create(choice_text='Not much', votes=0)
> > > <Choice: Not much>
> > > q.choice_set.create(choice_text='The sky', votes=0)
> > > <Choice: The sky>
> > > c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice objects have API access to their related Question objects.

> > > c.question
> > > <Question: What's up?>

# And vice versa: Question objects get access to Choice objects.

> > > q.choice_set.all()
> > > <QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
> > > q.choice_set.count()
> > > 3

# The API automatically follows relationships as far as you need.

# Use double underscores to separate relationships.

# This works as many levels deep as you want; there's no limit.

# Find all Choices for any question whose pub_date is in this year

# (reusing the 'current_year' variable we created above).

> > > Choice.objects.filter(question**pub_date**year=current_year)
> > > <QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.

> > > c = q.choice_set.filter(choice_text\_\_startswith='Just hacking')
> > > c.delete()

py manage.py createsuperuser
python manage.py shell
python manage.py test polls

> > > from django.test.utils import setup_test_environment
> > > setup_test_environment()
> > > from django.test import Client
> > >
> > > # create an instance of the client for our use
> > >
> > > client = Client()
> > >
> > > # get a response from '/'
> > >
> > > response = client.get('/')
> > > Not Found: /
> > >
> > > # we should expect a 404 from that address; if you instead see an
> > >
> > > # "Invalid HTTP_HOST header" error and a 400 response, you probably
> > >
> > > # omitted the setup_test_environment() call described earlier.
> > >
> > > response.status_code
> > > 404
> > >
> > > # on the other hand we should expect to find something at '/polls/'
> > >
> > > # we'll use 'reverse()' rather than a hardcoded URL
> > >
> > > from django.urls import reverse
> > > response = client.get(reverse('polls:index'))
> > > response.status_code
> > > 200
> > > response.content
> > > b'\n <ul>\n \n <li><a href="/polls/1/">What&#x27;s up?</a></li>\n \n </ul>\n\n'
> > > response.context['latest_question_list']
> > > <QuerySet [<Question: What's up?>]>
