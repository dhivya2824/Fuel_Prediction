# Fuel_Prediction

package Car;

import java.io.*;
import java.util.*;

public class Main {

    private static List<String[]> cars = new ArrayList<>();
    private static String[] headers;
    private static final String csvFile = "C:\\Users\\DELL\\Documents\\java mini\\car_data.csv";

    public static void main(String[] args) {
        try {
            loadCars();
        } catch (IOException e) {
            System.out.println("Error loading CSV: " + e.getMessage());
            return;
        }

        Scanner sc = new Scanner(System.in);

        while (true) {
            System.out.println("\n=== Car Management System ===");
            System.out.println("1. View all cars");
            System.out.println("2. Search car by model");
            System.out.println("3. Sort cars by price");
            System.out.println("4. Add a new car");
            System.out.println("5. Save & Exit");
            System.out.print("Choose: ");

            int choice;
            try {
                choice = Integer.parseInt(sc.nextLine());
            } catch (NumberFormatException e) {
                System.out.println("Invalid input! Enter a number 1-5.");
                continue;
            }

            switch (choice) {
                case 1 -> viewCars();
                case 2 -> searchCar(sc);
                case 3 -> sortByPrice();
                case 4 -> addCar(sc);
                case 5 -> {
                    try {
                        saveCars();
                        System.out.println("Changes saved. Exiting...");
                    } catch (IOException e) {
                        System.out.println("Error saving CSV: " + e.getMessage());
                    }
                    System.exit(0);
                }
                default -> System.out.println("Invalid choice! Please choose 1-5.");
            }
        }
    }

    // Load CSV and store header separately
    static void loadCars() throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader(csvFile))) {
            String line;
            if ((line = br.readLine()) != null) {
                headers = line.split(",");
            }
            while ((line = br.readLine()) != null) {
                cars.add(line.split(","));
            }
        }
    }

    // Display header + all cars
    static void viewCars() {
        for (String header : headers) {
            System.out.printf("%-20s", header);
        }
        System.out.println();
        System.out.println("=".repeat(80));

        for (String[] car : cars) {
            for (String value : car) {
                System.out.printf("%-20s", value);
            }
            System.out.println();
        }
    }

    // Search car by model keyword
    static void searchCar(Scanner sc) {
        System.out.print("Enter model to search: ");
        String keyword = sc.nextLine().toLowerCase();
        boolean found = false;

        for (String[] car : cars) {
            if (car[1].toLowerCase().contains(keyword)) {
                if (!found) {
                    for (String header : headers) {
                        System.out.printf("%-20s", header);
                    }
                    System.out.println("\n" + "=".repeat(80));
                    found = true;
                }
                for (String value : car) {
                    System.out.printf("%-20s", value);
                }
                System.out.println();
            }
        }

        if (!found) {
            System.out.println("No cars found with model containing '" + keyword + "'.");
        }
    }

    // Sort cars by price safely
    static void sortByPrice() {
        try {
            cars.sort((c1, c2) -> {
                try {
                    double price1 = Double.parseDouble(c1[2]);
                    double price2 = Double.parseDouble(c2[2]);
                    return Double.compare(price1, price2);
                } catch (NumberFormatException e) {
                    return 0; // invalid data treated as equal
                }
            });
            System.out.println("Cars sorted by price.");
            viewCars();
        } catch (Exception e) {
            System.out.println("Error sorting cars: " + e.getMessage());
        }
    }

    // Add new car
    static void addCar(Scanner sc) {
        String[] newCar = new String[headers.length];
        for (int i = 0; i < headers.length; i++) {
            System.out.print("Enter " + headers[i] + ": ");
            newCar[i] = sc.nextLine();
        }
        cars.add(newCar);
        System.out.println("Car added successfully!");
    }

    // Save all cars back to CSV
    static void saveCars() throws IOException {
        try (BufferedWriter bw = new BufferedWriter(new FileWriter(csvFile))) {
            bw.write(String.join(",", headers));
            bw.newLine();
            for (String[] car : cars) {
                bw.write(String.join(",", car));
                bw.newLine();
            }
        }
    }
}
