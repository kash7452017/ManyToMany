## @ManyToMany關係

>當兩個類（A，B）使用@ManyToMany註解時，會產生一個中間表A_B，該中間表表示兩個類的關聯關係，此時有兩種情況：只有一端（類）維護該關聯關係，兩端（類）均可維護該關聯關係。
>
>以下示例為兩端皆可維護
>
>![image](https://user-images.githubusercontent.com/101872264/215510034-9357fc66-a384-4466-a6e8-a05074d4e775.png)

**由於此示例為兩端皆可維護，需在Course類別中添加關係映射，透過`@JoinTable`註釋加入一個中間表，該中間表將替我們管理兩表之間的相互映射關係**
>注意joinColumns與inverseJoinColumns用法，在Course類別中，joinColumns代表的是對於Course表中主鍵的引用與配置，而inverseJoinColumns則代表著對於Course而言的副表(外表)也就是students表單的主鍵參考引用配置

```
@ManyToMany(fetch=FetchType.LAZY,
		cascade= {CascadeType.PERSIST, CascadeType.MERGE, 
		CascadeType.DETACH, CascadeType.REFRESH})
@JoinTable(name="course_student",
			joinColumns=@JoinColumn(name="course_id"),
			inverseJoinColumns=@JoinColumn(name="student_id")
		)
private List<Student> students;
```
**另外加入了添加學生(Student)方法**
```
// add a convenience method
public void addStudent(Student theStudent) {
	if (students == null) students = new ArrayList<>();
	students.add(theStudent);
}
```
**Course類別完整程式碼**
```
@Entity
@Table(name="course")
public class Course {
	
	// define our field
	
	// define constructors
	
	// define getter setters
	
	// define tostring
	
	// annotate fields
	
	
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private int id;
	
	@Column(name="title")
	private String title;
	
	@ManyToOne(cascade= {CascadeType.PERSIST, CascadeType.MERGE, 
						CascadeType.DETACH, CascadeType.REFRESH})
	@JoinColumn(name="instructor_id")
	private Instructor instructor;
	
	@OneToMany(fetch=FetchType.LAZY, cascade=CascadeType.ALL)
	@JoinColumn(name="course_id")
	private List<Review> reviews;
	
	@ManyToMany(fetch=FetchType.LAZY,
			cascade= {CascadeType.PERSIST, CascadeType.MERGE, 
			CascadeType.DETACH, CascadeType.REFRESH})
	@JoinTable(name="course_student",
				joinColumns=@JoinColumn(name="course_id"),
				inverseJoinColumns=@JoinColumn(name="student_id")
			)
	private List<Student> students;
	
	public Course() {
		
	}

	public Course(String title) {
		this.title = title;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public Instructor getInstructor() {
		return instructor;
	}

	public void setInstructor(Instructor instructor) {
		this.instructor = instructor;
	}

	
	public List<Review> getReview() {
		return reviews;
	}

	public void setReview(List<Review> reviews) {
		this.reviews = reviews;
	}
	
	// add a convenience method
	
	public void addReview(Review theReview) {
		
		if (reviews == null) reviews = new ArrayList<>();
		
		reviews.add(theReview);
	}
	
	public List<Student> getStudents() {
		return students;
	}

	public void setStudents(List<Student> students) {
		this.students = students;
	}
	
	// add a convenience method
	public void addStudent(Student theStudent) {
		if (students == null) students = new ArrayList<>();
		students.add(theStudent);
	}

	@Override
	public String toString() {
		return "Course [id=" + id + ", title=" + title + "]";
	}
}
```
**同樣的位於Student類別中，我們需要再次進行類似的操作，如上述Course類別時提到的，特別注意到此處的@JoinTable配置，此時對於Student而言是主表，因此在joinColumns所配置的是針對Student表中主鍵的引用，而Course對於Student來說是副表(外表)，因此在inverseJoinColumns配置對於外表Course主鍵的參考引用**
```
@ManyToMany(fetch=FetchType.LAZY,
		cascade= {CascadeType.PERSIST, CascadeType.MERGE, 
		CascadeType.DETACH, CascadeType.REFRESH})
@JoinTable(name="course_student",
			joinColumns=@JoinColumn(name="student_id"),
			inverseJoinColumns=@JoinColumn(name="course_id")
		)
private List<Course> courses;
```
**Student完整程式碼**
```
@Entity
@Table(name="student")
public class Student {
	
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private int id;
	
	@Column(name="first_Name")
	private String firstName;
	
	@Column(name="last_Name")
	private String lastName;

	@Column(name="email")
	private String email;
	
	@ManyToMany(fetch=FetchType.LAZY,
			cascade= {CascadeType.PERSIST, CascadeType.MERGE, 
			CascadeType.DETACH, CascadeType.REFRESH})
	@JoinTable(name="course_student",
				joinColumns=@JoinColumn(name="student_id"),
				inverseJoinColumns=@JoinColumn(name="course_id")
			)
	private List<Course> courses;
	
	public Student()
	{
		
	}

	public Student(String firstName, String lastName, String email) {
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}	

	public List<Course> getCourses() {
		return courses;
	}

	public void setCourses(List<Course> courses) {
		this.courses = courses;
	}

	@Override
	public String toString() {
		return "Student [id=" + id + ", firstName=" + firstName + ", lastName=" + lastName + ", email=" + email
				+ ", courses=" + courses + "]";
	}
}
```
**以下示例對於課程與學生創建的簡易演示，創建新課程以及新學生成員，並將課程透過我們的`addStudent`方法添加成員，此時我們剛剛所建立的中間表即會協助我們管理兩表之間的相互映射關西**
```
public class CreateCoursesAndStudentsDemo {

	public static void main(String[] args) {
		
		// create session factory
		SessionFactory factory = new Configuration()
				.configure("hibernate.cfg.xml")
				.addAnnotatedClass(Instructor.class)
				.addAnnotatedClass(InstructorDetail.class)
				.addAnnotatedClass(Course.class)
				.addAnnotatedClass(Review.class)
				.addAnnotatedClass(Student.class)
				.buildSessionFactory();
		
		// create session
		Session session = factory.getCurrentSession();
		
		try {  											
            // start transaction
            session.beginTransaction();
            
            // create a course
            Course tempCourse = new Course("Pacman4 - How To Score One Million Points");
            
            // save the course
            System.out.println("\nSaving the course ...");
            session.save(tempCourse);
            System.out.println("Saved the course: " + tempCourse);
            
            // create the students
            Student tempStudent1 = new Student("John", "Doe", "john@luv2code.com");
            Student tempStudent2 = new Student("Mary", "Public", "mary@luv2code.com");
            
            // add students to the course
            tempCourse.addStudent(tempStudent1);
            tempCourse.addStudent(tempStudent2);
            
            // save the students
            System.out.println("\nSaving students ...");
            session.save(tempStudent1);
            session.save(tempStudent2);
            System.out.println("Saved students: " + tempCourse.getStudents());
            
            // commit transaction
            session.getTransaction().commit();
 
            System.out.println("Done!");
            
        } catch (Exception exc) {
            exc.printStackTrace();
        } finally {
        	
        	// add clean up code
        	session.close();
        	
            factory.close();
        }
	}
}
```
**此處示例為針對特定課程添加特定成員**

![image](https://user-images.githubusercontent.com/101872264/215510185-3d18e547-863d-4f4f-85c0-9687a2180b2d.png)

![image](https://user-images.githubusercontent.com/101872264/215510265-b0723492-e5f5-4a34-92f1-0c468e2a9f4a.png)

![image](https://user-images.githubusercontent.com/101872264/215510305-f2091288-bd02-4860-99d4-daef24a00a58.png)

```
public class AddCoursesForMaryDemo {

	public static void main(String[] args) {
		
		// create session factory
		SessionFactory factory = new Configuration()
				.configure("hibernate.cfg.xml")
				.addAnnotatedClass(Instructor.class)
				.addAnnotatedClass(InstructorDetail.class)
				.addAnnotatedClass(Course.class)
				.addAnnotatedClass(Review.class)
				.addAnnotatedClass(Student.class)
				.buildSessionFactory();
		
		// create session
		Session session = factory.getCurrentSession();
		
		try {  											
            // start transaction
            session.beginTransaction();
            
            // get the student mary from database
            int studentId =2;
            Student tempStudent = session.get(Student.class, studentId);
            
            System.out.println("\nLoaded student: " + tempStudent);
            System.out.println("Courses: " + tempStudent.getCourses());
            
            // create more course
            Course tempCourse1 = new Course("Rubisk's Cube - How to Speed Cube");
            Course tempCourse2 = new Course("Atari 2600 - Game Development");
            
            // add student to courses
            tempCourse1.addStudent(tempStudent);
            tempCourse2.addStudent(tempStudent);
            
            // save the courses
            System.out.println("\nSaving the courses ...");
            
            session.save(tempCourse1);
            session.save(tempCourse2);
            
            // commit transaction
            session.getTransaction().commit();
 
            System.out.println("Done!");
            
        } catch (Exception exc) {
            exc.printStackTrace();
        } finally {
        	
        	// add clean up code
        	session.close();
        	
            factory.close();
        }
	}
}
```
**此處示範刪除特定課程但不刪除相關成員，因級聯關係中並未加入同步刪除這項，只會斷開成員與被刪除課程之間的關係**
```
public class DeletePacmanCourseDemo {

	public static void main(String[] args) {
		
		// create session factory
		SessionFactory factory = new Configuration()
				.configure("hibernate.cfg.xml")
				.addAnnotatedClass(Instructor.class)
				.addAnnotatedClass(InstructorDetail.class)
				.addAnnotatedClass(Course.class)
				.addAnnotatedClass(Review.class)
				.addAnnotatedClass(Student.class)
				.buildSessionFactory();
		
		// create session
		Session session = factory.getCurrentSession();
		
		try {  											
            // start transaction
            session.beginTransaction();
            
            // get the pacman course from db
            int courseId = 15;
            Course tempCourse = session.get(Course.class, courseId);
            
            // delete the course
            System.out.println("Deleting course: " + tempCourse);
            
            session.delete(tempCourse);
            
            // commit transaction
            session.getTransaction().commit();
 
            System.out.println("Done!");
            
        } catch (Exception exc) {
            exc.printStackTrace();
        } finally {
        	
        	// add clean up code
        	session.close();
        	
            factory.close();
        }
	}
}
```
**此處示範刪除特定成員但不刪除相關課程，同樣，因級聯關係中並未加入同步刪除這項，只會斷開課程與被刪除成員之間的關係**
```
public class DeleteMaryStudentDemo {

	public static void main(String[] args) {
		
		// create session factory
		SessionFactory factory = new Configuration()
				.configure("hibernate.cfg.xml")
				.addAnnotatedClass(Instructor.class)
				.addAnnotatedClass(InstructorDetail.class)
				.addAnnotatedClass(Course.class)
				.addAnnotatedClass(Review.class)
				.addAnnotatedClass(Student.class)
				.buildSessionFactory();
		
		// create session
		Session session = factory.getCurrentSession();
		
		try {  											
            // start transaction
            session.beginTransaction();
            
            // get the student mary from database
            int studentId =2;
            Student tempStudent = session.get(Student.class, studentId);
            
            System.out.println("\nLoaded student: " + tempStudent);
            System.out.println("Courses: " + tempStudent.getCourses());
            
            // delete student
            System.out.println("\nDeleting student: " + tempStudent);
            session.delete(tempStudent);
                        
            
            // commit transaction
            session.getTransaction().commit();
 
            System.out.println("Done!");
            
        } catch (Exception exc) {
            exc.printStackTrace();
        } finally {
        	
        	// add clean up code
        	session.close();
        	
            factory.close();
        }
	}
}
```
