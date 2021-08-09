# Going Further with JPA


## Part 1 Incorporating Relationships

### Aims
You will now create a class for the track table, and add in the annotations for the relationship between the compact disc and the track.

Your relationship will be a **one way / one to many relationship**. In other words, the `CompactDisc` will know about its `Track`s, but the tracks will not know about the CompactDisc object. They will however maintain the foreign key as a property in case you ever need to retrieve the album that a track is on.

Create the Annotated Track Entity
1.	In your IDE, in the same package as the `CompactDisc`, create a new class with the name `Track`.

2.	Add the following properties along with the get/set methods and required annotations.

| Property name | Property type | Column name |Special considerations |
---|---|---|---
| id	   |Integer| id | Primary key 
| title |String | title | |
|cdId | Integer | cd_id | Foreign key just mapped as a simple property |

4.	Add the annotation to specify that the class is an entity, and then the annotation specifying the table that you are mapping it to.

5.	Ensure that the class implements the Serializable interface.

### Update the CompactDisc Entity

1.	Now open the CompactDisc class and add a new property of type List<Track> called trackTitles complete with get/set methods.

2.	Annotate the List as follows:

```
@JoinColumn(name="cd_id", referencedColumnName="id")
@OneToMany
private List<Track> trackTitles = new ArrayList<Track>();
```


3.	Open the TestCompactDiscs class and update the code to retrieve CD number 16 (which has tracks in the database), and then retrieve the track information and display it. A suitable query is shown below:

```
Query allSpiceGirlTracks = em.createQuery("select t.title from Track t where t.cdId  = 16”);
```

5.	Run the application to see it working.


### Inserting a new Album

1.	Create a new CD object and set its properties. Add some Track objects the the CD.

2.	Persist the CD. Do the tracks go into the database? (you can check using the MySQL command line tool). If not, why not. 

3.	In the CompactDisc class, update the Annotation on the Track collection to cascade. Try adding an album again with tracks. Does it persist the tracks this time?

```
@OneToMany( cascade={CascadeType.MERGE, CascadeType.PERSIST})
private List<Track> trackTitles = new ArrayList<Track>();
```



## Part 2: Queries
In this exercise you will be creating some JPL queries.

1.	Open the TestCompactDiscs class and add code to do the following:
    
    a.	Retrieve all CDs where the artist name starts with the letter ‘S’.

    b.	Retrieve the number of CDs.

    c.	Retrieve all CDs in a list in alphabetic order by title.

    d.	Retrieve all the tracks by the Spice Girls.

    e.	All tracks names and the album name for album with id of 16.

    f.	Create a parameterized query that will allow you to specify a title and the matching CDs will be returned.

2.	Test your examples, and review the generated SQL that is output by Log4J.


## Part 3: Understanding Detached Objects
In this exercise, you will see for yourself how the lifecycle of our objects can affect how your applications behave.

### Modifying Object Properties
1.	In your existing project, create a new Java class with a main method called LifecycleTest.

2.	Add the code required to begin a new transaction from an entity manager using the persistence context created earlier.

3.	Add a few blank lines and then add the code to commit the transaction and close the entity manager.

4.	In the gap between the blank lines, retrieve CD number 12 and then change the title to something else simply using the setTitle method on the CompactDisc. 

5.	Run the code and then query the database using the MySQL client to see if it has changed. 

You will see from the console that when you ran the code an update was run against the database and the value in the table has changed. This was because the object was in a  managed state.

6.	Now move the call to setTitle to below the closing of the entity manager. This will not now update the database. You can try it if you like to see it for yourself. Run the code, and the database will not change. After the entity manager is closed the object becomes detatched.

7.	After the code closing the entity manager, open a new entity manager and get a new transaction.

8.	Now merge your modified CompactDisc object and close the new entity manager and transaction. See if the database has changed. You will now see that it has. This is because a detatched entity has had its properties copied into a managed version of the entity.

