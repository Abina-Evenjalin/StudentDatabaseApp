# StudentDatabaseApp

## Project Description 
This is a StudentDatabaseApp project, built in **Java (using IntelliJ IDEA)** and connected to a **MySQL database via XAMPP**.  
The application allows users to manage student records with basic operations like adding, viewing, updating, and deleting students.

---

## Requirements
- **Java JDK 8 or higher**
- **XAMPP** (MySQL running locally)
- **IntelliJ IDEA** (for coding and running the project)

---

## Setup Instructions

### 1. Start MySQL via XAMPP
1. Open **XAMPP Control Panel**.
2. Start **Apache**  and **MySQL**.

---

### 2. Create the Database
1. Open **phpMyAdmin** from XAMPP (`http://localhost/phpmyadmin`).
2. Run the SQL given below to create the database and table:
### 3. Configure Database Connection in IntelliJ
   db.url=jdbc:mysql://localhost:3306/StudentDB
   db.user=root
   db.password=

### 4. Run the Project

Open the project in IntelliJ IDEA.

Run Main.java.

The application will connect to MySQL via XAMPP and allow you to manage student records.

### Assumptions
- MySQL is running locally through XAMPP on port 3306.
- Java 8+ is installed and configured in the system PATH.
- The MySQL user `root` has permissions to create and modify the database.
- All source code needed to run the project is included in this repository.



### sql
CREATE DATABASE StudentDB;

USE StudentDB;

CREATE TABLE students (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    age INT,
    grade VARCHAR(10)
);

### java
import java.sql.*;
import java.util.Scanner;

public class StudentDatabaseApp {

    // JDBC connection details
    static final String URL = "jdbc:mysql://localhost:3306/studentdb";
    static final String USER = "root";  // change if needed
    static final String PASSWORD = "";  // your MySQL password

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        try (Connection conn = DriverManager.getConnection(URL, USER, PASSWORD)) {
            System.out.println("Connected to the database!");

            while (true) {
                System.out.println("\n1. Add Student\n2. View Students\n3. Update Student\n4. Delete Student\n5. Exit");
                System.out.print("Choose option: ");
                int choice = sc.nextInt();
                sc.nextLine(); // consume newline

                switch (choice) {
                    case 1 -> addStudent(conn, sc);
                    case 2 -> viewStudents(conn);
                    case 3 -> updateStudent(conn, sc);
                    case 4 -> deleteStudent(conn, sc);
                    case 5 -> { System.out.println("Goodbye!"); return; }
                    default -> System.out.println("Invalid choice!");
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private static void addStudent(Connection conn, Scanner sc) throws SQLException {
        System.out.print("Enter name: ");
        String name = sc.nextLine();
        System.out.print("Enter age: ");
        int age = sc.nextInt();
        sc.nextLine();
        System.out.print("Enter grade: ");
        String grade = sc.nextLine();

        String sql = "INSERT INTO students (name, age, grade) VALUES (?, ?, ?)";
        PreparedStatement stmt = conn.prepareStatement(sql);
        stmt.setString(1, name);
        stmt.setInt(2, age);
        stmt.setString(3, grade);
        stmt.executeUpdate();
        System.out.println("Student added successfully!");
    }

    private static void viewStudents(Connection conn) throws SQLException {
        String sql = "SELECT * FROM students";
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery(sql);

        System.out.println("\nID | Name | Age | Grade");
        while (rs.next()) {
            System.out.printf("%d | %s | %d | %s\n",
                    rs.getInt("id"),
                    rs.getString("name"),
                    rs.getInt("age"),
                    rs.getString("grade"));
        }
    }

    private static void updateStudent(Connection conn, Scanner sc) throws SQLException {
        System.out.print("Enter student ID to update: ");
        int id = sc.nextInt();
        sc.nextLine();
        System.out.print("Enter new grade: ");
        String grade = sc.nextLine();

        String sql = "UPDATE students SET grade=? WHERE id=?";
        PreparedStatement stmt = conn.prepareStatement(sql);
        stmt.setString(1, grade);
        stmt.setInt(2, id);
        int rows = stmt.executeUpdate();
        System.out.println(rows > 0 ? "Student updated!" : "Student not found!");
    }

    private static void deleteStudent(Connection conn, Scanner sc) throws SQLException {
        System.out.print("Enter student ID to delete: ");
        int id = sc.nextInt();

        String sql = "DELETE FROM students WHERE id=?";
        PreparedStatement stmt = conn.prepareStatement(sql);
        stmt.setInt(1, id);
        int rows = stmt.executeUpdate();
        System.out.println(rows > 0 ? "Student deleted!" : "Student not found!");
    }
}



