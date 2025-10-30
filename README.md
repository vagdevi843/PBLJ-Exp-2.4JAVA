# PBLJ-Exp-2.4JAVA
// JDBC_Application.java
import java.sql.*;
import java.util.*;

// =========================
// Part (a): JDBC Connection & Fetch
// =========================
class JDBCConnectionDemo {
    public static void fetchEmployees() {
        String url = "jdbc:mysql://localhost:3306/testdb"; // Change DB name
        String user = "root"; // Your MySQL username
        String password = "root"; // Your MySQL password

        try (Connection con = DriverManager.getConnection(url, user, password);
             Statement st = con.createStatement();
             ResultSet rs = st.executeQuery("SELECT EmpID, Name, Salary FROM Employee")) {

            System.out.println("\n--- Employee Records ---");
            while (rs.next()) {
                System.out.println("ID: " + rs.getInt("EmpID") +
                                   ", Name: " + rs.getString("Name") +
                                   ", Salary: " + rs.getDouble("Salary"));
            }

        } catch (SQLException e) {
            System.out.println("Database Error: " + e.getMessage());
        }
    }
}

// =========================
// Part (b): CRUD on Product Table
// =========================
class ProductCRUD {
    private static final String URL = "jdbc:mysql://localhost:3306/testdb";
    private static final String USER = "root";
    private static final String PASS = "root";

    public static void productMenu() {
        Scanner sc = new Scanner(System.in);
        while (true) {
            System.out.println("\n--- Product Management Menu ---");
            System.out.println("1. Add Product");
            System.out.println("2. View Products");
            System.out.println("3. Update Product");
            System.out.println("4. Delete Product");
            System.out.println("5. Exit");
            System.out.print("Enter choice: ");
            int choice = sc.nextInt();

            switch (choice) {
                case 1 -> insertProduct();
                case 2 -> displayProducts();
                case 3 -> updateProduct();
                case 4 -> deleteProduct();
                case 5 -> {
                    return;
                }
                default -> System.out.println("Invalid choice!");
            }
        }
    }

    private static void insertProduct() {
        try (Connection con = DriverManager.getConnection(URL, USER, PASS)) {
            con.setAutoCommit(false);
            String query = "INSERT INTO Product(ProductID, ProductName, Price, Quantity) VALUES(?, ?, ?, ?)";
            PreparedStatement ps = con.prepareStatement(query);
            Scanner sc = new Scanner(System.in);

            System.out.print("Enter Product ID: ");
            int id = sc.nextInt();
            sc.nextLine();
            System.out.print("Enter Product Name: ");
            String name = sc.nextLine();
            System.out.print("Enter Price: ");
            double price = sc.nextDouble();
            System.out.print("Enter Quantity: ");
            int qty = sc.nextInt();

            ps.setInt(1, id);
            ps.setString(2, name);
            ps.setDouble(3, price);
            ps.setInt(4, qty);

            ps.executeUpdate();
            con.commit();
            System.out.println("Product added successfully!");
        } catch (SQLException e) {
            System.out.println("Insert Error: " + e.getMessage());
        }
    }

    private static void displayProducts() {
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             Statement st = con.createStatement();
             ResultSet rs = st.executeQuery("SELECT * FROM Product")) {

            System.out.println("\n--- Product List ---");
            while (rs.next()) {
                System.out.println("ID: " + rs.getInt("ProductID") +
                                   ", Name: " + rs.getString("ProductName") +
                                   ", Price: " + rs.getDouble("Price") +
                                   ", Quantity: " + rs.getInt("Quantity"));
            }
        } catch (SQLException e) {
            System.out.println("Display Error: " + e.getMessage());
        }
    }

    private static void updateProduct() {
        try (Connection con = DriverManager.getConnection(URL, USER, PASS)) {
            con.setAutoCommit(false);
            Scanner sc = new Scanner(System.in);
            System.out.print("Enter Product ID to update: ");
            int id = sc.nextInt();
            System.out.print("Enter new Price: ");
            double price = sc.nextDouble();

            String query = "UPDATE Product SET Price=? WHERE ProductID=?";
            PreparedStatement ps = con.prepareStatement(query);
            ps.setDouble(1, price);
            ps.setInt(2, id);
            int rows = ps.executeUpdate();

            if (rows > 0) {
                con.commit();
                System.out.println("Product updated successfully!");
            } else {
                System.out.println("Product not found!");
            }
        } catch (SQLException e) {
            System.out.println("Update Error: " + e.getMessage());
        }
    }

    private static void deleteProduct() {
        try (Connection con = DriverManager.getConnection(URL, USER, PASS)) {
            con.setAutoCommit(false);
            Scanner sc = new Scanner(System.in);
            System.out.print("Enter Product ID to delete: ");
            int id = sc.nextInt();

            String query = "DELETE FROM Product WHERE ProductID=?";
            PreparedStatement ps = con.prepareStatement(query);
            ps.setInt(1, id);
            int rows = ps.executeUpdate();

            if (rows > 0) {
                con.commit();
                System.out.println("Product deleted successfully!");
            } else {
                System.out.println("Product not found!");
            }
        } catch (SQLException e) {
            System.out.println("Delete Error: " + e.getMessage());
        }
    }
}

// =========================
// Part (c): MVC - Student Management System
// =========================
class Student {
    private int studentID;
    private String name;
    private String department;
    private double marks;

    public Student(int studentID, String name, String department, double marks) {
        this.studentID = studentID;
        this.name = name;
        this.department = department;
        this.marks = marks;
    }

    public int getStudentID() { return studentID; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    public double getMarks() { return marks; }
}

// Controller class for JDBC operations
class StudentController {
    private static final String URL = "jdbc:mysql://localhost:3306/testdb";
    private static final String USER = "root";
    private static final String PASS = "root";

    public void addStudent(Student s) {
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             PreparedStatement ps = con.prepareStatement("INSERT INTO Student VALUES(?, ?, ?, ?)")) {
            ps.setInt(1, s.getStudentID());
            ps.setString(2, s.getName());
            ps.setString(3, s.getDepartment());
            ps.setDouble(4, s.getMarks());
            ps.executeUpdate();
            System.out.println("Student added successfully!");
        } catch (SQLException e) {
            System.out.println("Error adding student: " + e.getMessage());
        }
    }

    public void viewStudents() {
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             Statement st = con.createStatement();
             ResultSet rs = st.executeQuery("SELECT * FROM Student")) {

            System.out.println("\n--- Student Records ---");
            while (rs.next()) {
                System.out.println("ID: " + rs.getInt("StudentID") +
                                   ", Name: " + rs.getString("Name") +
                                   ", Dept: " + rs.getString("Department") +
                                   ", Marks: " + rs.getDouble("Marks"));
            }
        } catch (SQLException e) {
            System.out.println("Error viewing students: " + e.getMessage());
        }
    }
}

// View class
class StudentView {
    private StudentController controller = new StudentController();

    public void menu() {
        Scanner sc = new Scanner(System.in);
        while (true) {
            System.out.println("\n--- Student Management ---");
            System.out.println("1. Add Student");
            System.out.println("2. View Students");
            System.out.println("3. Exit");
            System.out.print("Enter choice: ");
            int ch = sc.nextInt();

            switch (ch) {
                case 1 -> {
                    System.out.print("Enter ID: ");
                    int id = sc.nextInt();
                    sc.nextLine();
                    System.out.print("Enter Name: ");
                    String name = sc.nextLine();
                    System.out.print("Enter Department: ");
                    String dept = sc.nextLine();
                    System.out.print("Enter Marks: ");
                    double marks = sc.nextDouble();
                    Student s = new Student(id, name, dept, marks);
                    controller.addStudent(s);
                }
                case 2 -> controller.viewStudents();
                case 3 -> { return; }
                default -> System.out.println("Invalid choice!");
            }
        }
    }
}

// =========================
// MAIN CLASS
// =========================
public class JDBC_Application {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (true) {
            System.out.println("\n===== JDBC Application Menu =====");
            System.out.println("1. Fetch Employee Data");
            System.out.println("2. Product CRUD Operations");
            System.out.println("3. Student Management (MVC)");
            System.out.println("4. Exit");
            System.out.print("Enter choice: ");
            int ch = sc.nextInt();

            switch (ch) {
                case 1 -> JDBCConnectionDemo.fetchEmployees();
                case 2 -> ProductCRUD.productMenu();
                case 3 -> new StudentView().menu();
                case 4 -> {
                    System.out.println("Exiting...");
                    return;
                }
                default -> System.out.println("Invalid choice!");
            }
        }
    }
}
