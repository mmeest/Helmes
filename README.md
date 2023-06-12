# Helmes
Technical task in java

## index.html - original file

    Correcting deficiencies in index.html:
        Ensure proper formatting and closing tags.
        Add name attribute to the input field for the name.
        Add value attribute to the checkbox input for the agreement.
        Wrap the form in <form> tags and specify an action and method attribute.

    Adding entries to the database and composing the selectbox:
        Set up a server-side script or backend framework (e.g., Node.js with Express, PHP, Django, etc.) to handle form submissions and database operations.
        Create a database table to store the sectors' data.
        On form submission, retrieve the selected sectors from the $_POST or request body.
        Save the selected sectors to the database.

    Activities after the "Save" button is pressed:
        Validate all input data on the server-side to ensure all fields are filled.
        Store the input data (name, sectors, agreement) in the database.
        After successful submission, retrieve the stored data from the database.
        Pre-fill the form fields with the retrieved data, allowing users to edit their information during the session.
        
## server-side code using Java and the Spring Boot framework to handle the form submission and interact with a database.

```
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

import java.util.ArrayList;
import java.util.List;

@SpringBootApplication
@Controller
public class Application {

    // In-memory list to store the sectors (replace with database access)
    private List<String> sectors = new ArrayList<>();

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @GetMapping("/")
    public String index(Model model) {
        // Pass the sectors list to the template
        model.addAttribute("sectors", sectors);
        return "index";
    }

    @PostMapping("/save")
    public String saveData(@RequestParam String name,
                           @RequestParam(value = "sectors", required = false) List<String> selectedSectors,
                           @RequestParam(value = "agree", required = false) boolean agree,
                           Model model) {
        // Validation - check if all fields are filled
        if (name.isEmpty() || selectedSectors == null || selectedSectors.isEmpty() || !agree) {
            model.addAttribute("error", "Please fill in all fields.");
            model.addAttribute("name", name);
            model.addAttribute("sectors", sectors);
            return "index";
        }

        // Save the data to the database (implement your database logic here)

        // Store the selected sectors in the in-memory list
        sectors = selectedSectors;

        // Redirect to a success page or display a success message
        return "success";
    }
}

```

In this example, we have a Spring Boot application with two endpoints: / and /save. The / endpoint serves the index.html template where the form is displayed, and the /save endpoint handles the form submission.

Make sure to create a templates directory in your project's resources folder and place the index.html file inside it. You can customize the HTML template according to your needs.

## Preview database 

1. Create a new MySQL database:

    Open a MySQL client (e.g., MySQL Command Line Client, phpMyAdmin, MySQL Workbench).
    Execute the following SQL statement to create the database:
    
```
CREATE DATABASE form_data;
```    

2. Set up the database connection in your Spring Boot application:

    Open the application.properties file located in the src/main/resources directory.
    Add the following properties to configure the database connection:
    
```
spring.datasource.url=jdbc:mysql://localhost:3306/form_data
spring.datasource.username=<your_mysql_username>
spring.datasource.password=<your_mysql_password>
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
```    

    Replace <your_mysql_username> and <your_mysql_password> with your MySQL credentials.

3. Define an entity class for the form data:

    Create a new Java class named FormData.java.
    Annotate the class with @Entity to mark it as an entity to be persisted in the database.
    Define the fields name, sectors, and agree along with their appropriate annotations.
    Here's an example of how the FormData class can be defined:
    
```
import javax.persistence.*;

@Entity
public class FormData {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ElementCollection
    private List<String> sectors;

    private boolean agree;

    // Constructors, getters, and setters
}
```    

4. Modify the Application.java class to use JPA and store the form data:

    Add the @EnableJpaRepositories annotation to the Application class to enable JPA repositories.
    Inject the FormDataRepository (a repository interface) into the Application class.
    Modify the saveData method to store the form data in the database:
    
```
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

// ...

@EnableJpaRepositories
@Controller
public class Application {
    @Autowired
    private FormDataRepository formDataRepository;

    // ...

    @PostMapping("/save")
    public String saveData(@RequestParam String name,
                           @RequestParam(value = "sectors", required = false) List<String> selectedSectors,
                           @RequestParam(value = "agree", required = false) boolean agree,
                           Model model) {
        // Validation...

        // Save the data to the database
        FormData formData = new FormData();
        formData.setName(name);
        formData.setSectors(selectedSectors);
        formData.setAgree(agree);
        formDataRepository.save(formData);

        // ...
    }
}
```    

5. Create a repository interface to perform database operations:

    Create a new Java interface named FormDataRepository.java.
    Extend the JpaRepository interface and specify the entity type (FormData) and the ID type (Long).
    This repository interface will provide the necessary methods for CRUD operations on the FormData entity.
    Here's an example of how the FormDataRepository interface can be defined:
    
```
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface FormDataRepository extends JpaRepository<FormData, Long> {
    // You can add custom query methods here if needed
}
```    

The FormDataRepository interface extends the JpaRepository interface, which provides methods for basic CRUD operations (create, read, update, delete) on the FormData entity. By extending JpaRepository, you don't need to implement these methods yourself as Spring Data JPA automatically generates the implementation based on the provided entity type.

You can also define custom query methods in the FormDataRepository interface if you have specific requirements beyond the basic CRUD operations. These methods can be based on naming conventions or annotated with @Query to write custom SQL queries.

Import the necessary classes (JpaRepository and Repository) from the appropriate packages.
 
