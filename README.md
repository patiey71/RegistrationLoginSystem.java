# RegistrationLoginSystem.java
import java.util.ArrayList;
import java.util.Scanner;

/**
 * Registration class
 * Handles user account creation with validation of username, password, and cell phone number.
 */
class Registration {

    // Stores registered users as [username, password, cellphone]
    private ArrayList<String[]> userList = new ArrayList<>();

    /**
     * Checks that the username:
     * - Contains an underscore (_)
     * - Is no more than 5 characters long
     */
    public boolean checkUserName(String userName) {
        return userName.contains("_") && userName.length() <= 5;
    }

    /**
     * Checks that the password meets complexity rules:
     * - At least 8 characters long
     * - Contains a capital letter
     * - Contains a number
     * - Contains a special character
     */
    public boolean checkPasswordComplexity(String password) {
        if (password.length() < 8) return false;

        boolean hasCapital = false;
        boolean hasNumber  = false;
        boolean hasSpecial = false;

        for (char c : password.toCharArray()) {
            if (Character.isUpperCase(c))      hasCapital = true;
            if (Character.isDigit(c))          hasNumber  = true;
            if (!Character.isLetterOrDigit(c)) hasSpecial = true;
        }

        return hasCapital && hasNumber && hasSpecial;
    }

    /**
     * Checks that the cell phone number:
     * - Contains the international country code (starts with +)
     * - Is no more than 15 characters long
     * Uses a regular expression.
     *
     * Reference: E.164 international telephone numbering plan
     * https://en.wikipedia.org/wiki/E.164
     */
    public boolean checkCellPhoneNumber(String cellPhoneNumber) {
        String regex = "^\\+[0-9]{1,3}[0-9]{6,12}$";
        return cellPhoneNumber.matches(regex) && cellPhoneNumber.length() <= 15;
    }

    /**
     * Registers a new user after validating all three fields.
     * Returns the appropriate message for each condition.
     */
    public String registerUser(String userName, String password, String cellPhoneNumber) {

        if (!checkUserName(userName)) {
            return "Username is not correctly formatted; please ensure that your username " +
                   "contains an underscore and is no more than five characters in length.";
        }

        if (!checkPasswordComplexity(password)) {
            return "Password is not correctly formatted; please ensure that the password " +
                   "contains at least eight characters, a capital letter, a number, and a special character.";
        }

        if (!checkCellPhoneNumber(cellPhoneNumber)) {
            return "Cell phone number incorrectly formatted or does not contain international code.";
        }

        userList.add(new String[]{userName, password, cellPhoneNumber});
        return "Username successfully captured. Password successfully captured. " +
               "Cell phone number successfully added. User registered successfully.";
    }

    public ArrayList<String[]> getUserList() {
        return userList;
    }
}

/**
 * Login class
 * Handles user authentication by verifying credentials against registered users.
 */
class Login {

    private ArrayList<String[]> userList;

    public Login(ArrayList<String[]> userList) {
        this.userList = userList;
    }

    /** Checks username contains underscore and is ≤5 characters. */
    public boolean checkUserName(String userName) {
        return userName.contains("_") && userName.length() <= 5;
    }

    /** Checks password has ≥8 chars, capital, number, special character. */
    public boolean checkPasswordComplexity(String password) {
        if (password.length() < 8) return false;

        boolean hasCapital = false;
        boolean hasNumber  = false;
        boolean hasSpecial = false;

        for (char c : password.toCharArray()) {
            if (Character.isUpperCase(c))      hasCapital = true;
            if (Character.isDigit(c))          hasNumber  = true;
            if (!Character.isLetterOrDigit(c)) hasSpecial = true;
        }

        return hasCapital && hasNumber && hasSpecial;
    }

    /** Checks cell phone has international code and is ≤15 characters.
     *  Reference: E.164 — https://en.wikipedia.org/wiki/E.164 */
    public boolean checkCellPhoneNumber(String cellPhoneNumber) {
        String regex = "^\\+[0-9]{1,3}[0-9]{6,12}$";
        return cellPhoneNumber.matches(regex) && cellPhoneNumber.length() <= 15;
    }

    /** Returns true if credentials match a registered user. */
    public boolean loginUser(String userName, String password) {
        for (String[] user : userList) {
            if (user[0].equals(userName) && user[1].equals(password)) {
                return true;
            }
        }
        return false;
    }

    /** Returns welcome message on success, error message on failure. */
    public String returnLoginStatus(String userName, String password) {
        if (loginUser(userName, password)) {
            return "Welcome " + userName + ", it is great to see you again.";
        } else {
            return "Username or password incorrect, please try again.";
        }
    }
}

/**
 * Main class
 * Entry point for the Registration and Login application
 */
public class RegistrationLoginSystem {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Registration registration = new Registration();

        System.out.println("=== Registration and Login Application ===\n");

        // Registration
        System.out.print("Enter username (must contain '_', max 5 chars): ");
        String username = scanner.nextLine().trim();

        System.out.print("Enter password (min 8 chars, 1 capital, 1 number, 1 special): ");
        String password = scanner.nextLine().trim();

        System.out.print("Enter cell phone number (e.g. +27838968976): ");
        String cellPhone = scanner.nextLine().trim();

        System.out.println("\n[Registration]: " + registration.registerUser(username, password, cellPhone));

        // Login
        if (!registration.getUserList().isEmpty()) {
            System.out.println("\n--- LOGIN ---");
            System.out.print("Username: ");
            String lu = scanner.nextLine().trim();
            System.out.print("Password: ");
            String lp = scanner.nextLine().trim();

            Login login = new Login(registration.getUserList());
            System.out.println("\n[Login]: " + login.returnLoginStatus(lu, lp));
        }

        scanner.close();
    }
}
