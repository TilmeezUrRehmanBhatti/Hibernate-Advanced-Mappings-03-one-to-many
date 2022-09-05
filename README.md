

# One-to-Many Mapping

+ An instructor can have many courses
  + Bi-directional


<img src="https://user-images.githubusercontent.com/80107049/188426449-ef6f817f-62d5-4537-af54-3c37ceef1ae6.png" width = 600 />

+ Many courses can have on instructor
  + Inverse / opposite of One-to-Many


**Real-World Project Requirement**
+ If we delete an instructor, DO NOT delete the courses 
+ If we delete a course, DO NOT delete the instructor
+ Do not apply cascading deletes!

**Development Process:One-to-Many**
1. Prep Work - Define database tables
2. Create Course class
3. Update Instructor class
4. Create Main App

_Step 1:Define database tables_

**table:course**

```POSTGRESQL
Create TABLE course (
  id BIGSERIAL NOT NULL,
  title VARCHAR(128) DEFAULT NULL,
  instructor_id BIGINT DEFAULT NULL,
  PRIMARY KEY(id),
  UNIQUE KEY TITLE_UNIQUE(title),
  ...
);
```
+ `UNIQUE KEY` is for Prevent duplicate course titles


_table:course - foreign key_
```POSTGRESQL
CREATE TABLE course(
  ...
  CONSTRAINT FK_INSTRUCTOR
        FOREIGN KEY (instructor_id)
            REFERENCES instructor(id),
  ...
)
```

<img src="inkdrop://file:Krx8hLJkn" width = 600 />


_Step 2:Create Course class - @ManyToOne_

```JAVA 
@Entity
@Table(name="course")
public class Course {
  
  @Id
  @GeneratedValue(strategy=GenerationType.IDENTITY)
  @Column(name="id")
  private int id;
  
  @Column(name="title")
  private String title;
  
  @ManyToOne
  @JoinColumn(name="instructor_id")
  private Instructor instructor;
 
  ...
    // constructor, getters / setters
  
}
```

_STEP 3:Update Instructor - reference courses_

```JAVA 
@Entity
@Table(name="instructor")
public class Instructor {
  
  ...
  
  private List<Course> courses;
  
  public List<Course> getCourses() {
    return courses;
  }
  
  public void setCourses(List<Course> courses) {
    this.courses = courses;
  }
}
```

**Add @IneToMany annotation**
```JAVA 
@Entity
@Table(name="instructor")
public class Instructor {
  ...
  @OneToMany(mappedBy="instructor")
  private List<Course> courses;
  
    public List<Course> getCourses() {
    return courses;
  }
  
  ...
    
}
```

+ `@OneToMany(mappedBy="instructor")` Refers to "instructor" property in "Course" class.

+ **mappedBy** tells Hibernate
  + look at the instructor property in the Course class
  + Use information from the Course class `@JoinColumn`
  + To help find associated course for instructor

**Add support for Cascading**

```JAVA 
@Entity
@Table(name="instructor")
public class Instructor {
  ...
    
  @OneToMany(mappedBy="instructor",
             cascade={CascadeType.PRESIST, CascadeType.MERGE,
                      CascadeType.DETACH, CascadeType.REFRESH})
  private List<Course> course;
  
 ... 
}
```
+ Do not apply cascading delete

Same in class course

```JAVA 
@Entity
@Table(name="course")
public class Course {
  ...
    
  @OneToMany(cascade={CascadeType.PRESIST, CascadeType.MERGE,
                      CascadeType.DETACH, CascadeType.REFRESH})
  @JoinColumn(name="instructor_id")
  private Instructor instructor;
  
 ... 
 // constructor, getters / setters
}
```
+ Do not apply cascading delete

**Add convenience method for bi-directional**
```JAVA
@Entity
@Table(name="instructor")
public class Instructor {
  
  // add convienience methods for the bi-directional relationship
  
  public void add(Course tempCourse){
    
    if(courses == null) {
      courses = new ArrayList<>();
    }
    
    courses.add(tempCourse);
    
    tempCourse.setInstructor(this);
  }
 ... 
}
```
+ `tempCourse.setInstructor(this);` Bi-directional link 

_Step 4:Create Main App_
```JAVA
public static void main(String[] args) {
  
  // get the instructor object
  int theId = 1;
  Instructor thempInstructor = session.get(Instructor.class, theId);
  
  // print instructor
  System.ou.println("tempInstructor: " + tempInstructor);
  
  // print the associated courses
  System.out.println("courses: " + tempInstructor.getCourses());
  
```
