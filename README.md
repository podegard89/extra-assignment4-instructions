# extra-assignment4-instructions

These instructions are extra tips/steps to help you out with part 3, 
specifically the part "Updating `HomeController`"!
 
You should have already refactored the `Employer` and `Job` classes to build a Many to One/One to Many
relationship between them and extended `AbstractEntity` to the `Job` class in the previous parts.

Now it's time to update the `HomeController`!
If you haven't reached this yet, these instructions won't mean much to you.

Once you've added the `employerRepository` into the `HomeController`, it's now time to pass in all
employer data from the employer table via the `employerRepository` to the `/add` template. First, which method
handles get requests at `/add`? That is where you will add in all of the `employerRepository` data using `model.addAttribute`
and a `CrudRepository` method that gets all objects from a given repository! (you've done this a few times, code of this kind
exists in both `SkillController` and `EmployerController`)

Once you've done that, you're now onto steps 3 and 4. This is where some real confusion can start so I am trying
to clear that up with these extra tips.

Notice first the paramters of our ```@PostRequest``` handler method ```processAddJobForm```!
This method handles the post request that the form sends when we submit it to create a new job.
```4 parameters: newJob, errors, model, and employerId```

```errors``` is an object that allows us to check our validation.
```model``` is what allows us to pass data into our templates.
These 2 are a given.
Make sure you're coding `AFTER` the if check for the errors object. You want your method to make sure there's no errors before doing anything
else.

```newJob``` is the job that is getting created by our actual form.
Its name property is already getting set via model binding. The ```@ModelAttribute``` annotation is automatically picking up on what the value
of ```newJob```'s name property should be, which is whatever we type into the text input. What isn't getting set automatically is the value of
the ```newJob```'s ```employer``` property. Remember that we've already set up a relationship between the ```Job``` and ```Employer``` classes. All job objects will 
contain at least a ```name``` string and an ```Employer``` object by this point. In the future jobs will also contain a list of skills as well. That's part 4
of the assignment. The 4th parameter is ```employerId```.

Step 3 states "Make a mental note of the name of the variable being used to pass the selected employer id on form submission."
We are handling post requests at `/add`, so that is the template we should reference.
This sentence is wanting you to recall how the `name` attribute on our HTML tags gets treated when we
send a post request with a form. The employer select input has a `name` attribute of `employerId`, it's value will be the id of 
whatever employer you choose when creating a job in the `/add` template's form. This gets sent to our post request handler method as an
`@RequestParameter`! Step 4 is asking us to then use this id to get the matching employer object out of the database in our handler method.

Now that we have received our `employerId` from our form, we need to get the matching employer object out of our database using the 
`employerRepository` and its associated methods. Recall how we searched for a single object in a table using `findById`! This method takes an
id and searches the table for a row with that given id as its primary key. `findById` returns out this funky `Optional` object. The `Optional` class
is simpler than it looks! It is what we call a wrapper class. The `Optional` object returned by `findById` will itself either contain the employer
object stored as the row with the `employerId` we passed in for the method, or it will be empty if no matching row was found.

The syntax looks like this:
```Optional<Employer> optEmployer = employerRepository.findById(employerId);```

Once this line executes, the employer object we need is locked inside this `Optional` object `optEmployer` (using opt is a typical practice to name
objects of the `Optional` class). To retrieve our employer object out of `optEmployer`, we use a method of the `Optional` class:

```Employer employer = optEmployer.get()```

This will return the employer object wrapped up inside the Optional object. We also store the returned object in this 'employer' variable to be used in the next
line of code.

From here, we actually have to take this employer object and set it as the value of the `employer` property of our `newJob` object!
We finally have a use for the setters we have generated with all of our classes up to this point!

```newJob.setEmployer(employer);```

Notice we are referencing the `newJob` that is also being passed into our request handler method from our form. Like we stated earlier, this
`newJob` already has a name, but we are manually setting the employer property with a few extra steps since model binding didn't do this automatically.

We can now save our `newJob` to the database for viewing later! I think you can figure out how to save this `newJob` to the database, but I'll 
give you a hint! You need to first create an instance of `JobRepository` in the `HomeController`, then call a method on that `jobRepository`
that saves data to its corresponding table.

And that is it! 60 some odd extra lines of instructions... but we made it. Ask me questions or let me know if there's any typos. I've tried
to edit this as best I can. Once this is done, you can now run your application and successfully add employers to the database as well as
create jobs that have associated employers! If you can successfully do this, its time to write the next SQL query, or save those until the end
and move on to Part 4! 


