#  @OneToOne - Bi-Directional Overview

_New USer Case_
+ If we load an InstructorDetail
    + Then we'd like to get the association Instructor
+ Can't do this with current uni-directional relationship

<img src="https://user-images.githubusercontent.com/80107049/188320969-de93adc7-77db-47a0-a2b6-f25df5f713a0.png" width = 300 />

_Bi-Directional_
+ Bi-Directional relationship is the solution
+ We can start with InstructorDetail and makt it back to the Instructor

<img src="https://user-images.githubusercontent.com/80107049/188321013-78c99c67-4362-44c1-9400-16c71ed8636d.png" width = 300 />

+ To use Bi-Directional, we can keep the existing database schema
  + No change required to database 

+ Simply update the Java code


**Development Process: One-to-One (Bi-Directional**
1. Make updates to InstructorDetail class.
    1. Add new field to refer Instructor
    2.  Add getter / setter methods for instructor
    3.  Add @OneToOne annotation

2. Create Main App

_Step 1.1:Add new field to reference Instructor_
```JAVA
@Entity
@Table(name="instructor_detail")
public clas instructorDetail {
  
 ...
  private Instrucotr instructor;
  
 ... 
}
```

_Step 1.2:Add getter/setter methods Instructor_
```JAVA
@Entity
@Table(name="instructor_detail")
public clas instructorDetail {
  
 ...
  private Instrucotr instructor;
  
  public instructor getInstructor(){
    return instructor;
  }
  
  public void setInstructor(Instructor instructor) {
    this.instructor = instructor;
  }
  ...
}
```

_Step 1.3:Add @OneToOne annotation_
```JAVA
@Entity
@Table(name="instructor_detail")
public clas instructorDetail {
  
 ...
  @OneToOne(mappedBy="instructorDetail")
  private Instrucotr instructor;
  
  public instructor getInstructor(){
    return instructor;
  }
  
  public void setInstructor(Instructor instructor) {
    this.instructor = instructor;
  }
  ...
}
```
+ mappedBy in `@OneToOne(mappedBy="instructorDetail")` Refers to "instructorDetail" property in "instructor" class
+ **mappedBy** tells Hibernate
    + Look at the instructorDetail property in th Instructor class
    + Use information from Instructor class @JoinColumn
    + To help find associated instructor

_Add support for Cascading_
```JAVA
@Entity
@Table(name="instructor_detail", cascade=CascadeType.ALL)
public class InstructorDetail {
    @OneToOne(mappedBy="instructorDetail")
  private Instrucotr instructor;
  
  public instructor getInstructor(){
    return instructor;
  }
  
  public void setInstructor(Instructor instructor) {
    this.instructor = instructor;
  }
  ...
}
```
+ `cascade=CascadeType.ALL` Cascade all operations to the associated Instructor

_Step 2:Create Main App_
```JAVA
public static void main(String[] args) {
  ...
  // get the instructor details object
  int theId = 1;
  InstructorDetail tempInstructorDetail = session.get(InstructorDetail.class, theId);
  
  // print detail
  System.out.println("tempInstructorDetail: " + tempInstructorDetail);
  
  // print the associated instructor
  System.out.println("the associated instructor: " + tempInstructorDetail.getInstructor());
  
```
