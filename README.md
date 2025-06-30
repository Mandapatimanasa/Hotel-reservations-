# Hotel-reservations-
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Date;
import java.util.Scanner;

public class HotelReservationSystem {  // Main Class
    public static void main(String[] args) {
        ArrayList<Room> rooms = new ArrayList<>();
        rooms.add(new Room(101, "Standard", 100.0));
        rooms.add(new Room(102, "Deluxe", 200.0));
        rooms.add(new Room(103, "Suite", 300.0));

        Scanner scanner = new Scanner(System.in);
        System.out.println("Enter User ID:");
        int userId = scanner.nextInt();

        System.out.println("Available Rooms:");
        for (Room room : rooms) {
            if (room.available) {
                System.out.println(room.getDetails());
            }
        }

        System.out.println("Enter Room ID to book:");
        int roomId = scanner.nextInt();
        Room selectedRoom = null;

        for (Room room : rooms) {
            if (room.roomId == roomId && room.available) {
                selectedRoom = room;
                break;
            }
        }

        if (selectedRoom != null) {
            Reservation reservation = new Reservation(userId, selectedRoom, new Date(), new Date());
            Payment payment = new Payment(userId, selectedRoom.price);
            payment.processPayment();
            FileStorage.saveReservation(reservation);

            System.out.println("Booking Confirmed: " + reservation.getDetails());
            System.out.println("Payment Status: " + payment.getPaymentStatus());
        } else {
            System.out.println("Room not available or invalid selection.");
        }

        scanner.close();
    }
}

// Room Class
class Room {
    int roomId;
    String category;
    double price;
    boolean available;

    public Room(int roomId, String category, double price) {
        this.roomId = roomId;
        this.category = category;
        this.price = price;
        this.available = true;
    }

    public void bookRoom() {
        available = false;
    }

    public void cancelBooking() {
        available = true;
    }

    public String getDetails() {
        return "Room ID: " + roomId + ", Category: " + category + ", Price: $" + price;
    }
}

// Reservation Class
class Reservation {
    int userId;
    Room room;
    Date checkIn;
    Date checkOut ;

    public Reservation(int userId, Room room, Date checkIn, Date checkOut) {
        this.userId = userId;
        this.room = room;
        this.checkIn = checkIn;
        this.checkOut = checkOut;
        room.bookRoom();
    }

    public void cancelReservation() {
        room.cancelBooking();
    }

    public String getDetails() {
        return "User ID: " + userId + ", " + room.getDetails() + ", Check-in: " + checkIn + ", Check-out: " + checkOut;
    }
}

// Payment Class
class Payment {
    int userId;
    double amount;
    String status;

    public Payment(int userId, double amount) {
        this.userId = userId;
        this.amount = amount;
        this.status = "Pending";
    }

    public void processPayment() {
        status = "Completed";
    }

    public String getPaymentStatus() {
        return "User ID: " + userId + ", Amount: $" + amount + ", Status: " + status;
    }
}

// File Storage Class
class FileStorage {
    public static void saveReservation(Reservation reservation) {
        try (FileWriter writer = new FileWriter("reservations.txt", true)) {
            writer.write(reservation.getDetails() + "\n");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
