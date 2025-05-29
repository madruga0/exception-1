# You couldn't see this repository. 
# Just for my only studies and notes.
# But if you think necessary, make yourself at home.
# Thx and God bless u!

```java
package TratamentoExceptions;
import java.util.*;
public class Program {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        try {
            //codigo de execucao normal do trecho
            // aconteceu algo aqui
            String[] arr = sc.nextLine().split(" ");
            int pos = sc.nextInt();
            System.out.println(arr[pos]);
        }
        catch(Exception e) {
        //code to be executed if an exception occurs
        //specifies the type of exception to be handled
        //gets this 'something'
        }
    }
}
```

# Try/catch/printStacktrace

```java
package TratamentoExceptions;

import java.util.*;

public class Program {
    public static void main(String[] args) {

        method1();

        System.out.println("End of program");
    }

    public static void method1() {
        System.out.println("METHOD1 START");
        method2();
        System.out.println("METHOD1 END");
    }

 // stack trace
    public static void method2() {
        System.out.println("METHOD2 START");
        Scanner sc = new Scanner(System.in);

        try {
            String[] arr = sc.nextLine().split(" ");
            int pos = sc.nextInt();
            System.out.println(arr[pos]);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Invalid position");
            e.printStackTrace(); // mostra toda a chamada de metodos que acarretou aquela exception
        } catch (InputMismatchException e) {
            System.out.println("Input error");
        } finally {

        }
        sc.close();
        System.out.println("METHOD2 END");
    }
}
```

# finally

```java
package TratamentoExceptions;

import java.io.File;
import java.io.FileNotFoundException;
import java.util.*;

public class Program {
    public static void main(String[] args) {
        File file = new File("/home/me/java-estudos/Main.java");
        Scanner sc = null;
        try{
            sc = new Scanner(file);
            while(sc.hasNextLine()) {
                System.out.println(sc.nextLine());
            }
        }
        catch (FileNotFoundException e) {
            System.out.println("Error opening file: " + e.getMessage());
        }
        finally {
            if(sc != null) {
                sc.close();
            }
            System.out.println("Finally block executed");
        }
    }
}
```

# Class Reservation

```java
package TratamentoExceptions.ExceptionsPersonalizadas.ReservationProblem;

import java.text.SimpleDateFormat;
import java.util.*;
import java.util.concurrent.TimeUnit;

public class Reservation {

    private Integer roomNumber;
    private Date checkIn;
    private Date checkOut;

    // this is static for don't generate one SimpleDateFormat for each Reservation class created
    private static SimpleDateFormat sdf = new SimpleDateFormat("dd/MM/yyyy");

    public Reservation(Integer roomNumber, Date checkIn, Date checkOut) {
        this.roomNumber = roomNumber;
        this.checkIn = checkIn;
        this.checkOut = checkOut;
    }
    public Integer getRoomNumber() {
        return roomNumber;
    }
    public void setRoomNumber(Integer roomNumber) {
        this.roomNumber = roomNumber;
    }
    public Date getCheckIn() {
        return checkIn;
    }

    public Date getCheckOut() {
        return checkOut;
    }

    public long duration() {
        // diference between dates in milliseconds
        long diff = checkOut.getTime() - checkIn.getTime();
        return TimeUnit.DAYS.convert(diff, TimeUnit.MILLISECONDS);
    }

    public void updateDates(Date checkIn, Date checkOut) {
        this.checkIn = checkIn;
        this.checkOut = checkOut;
    }

    @Override
    public String toString() {
        return "Room "
            + roomNumber
            + ", check-in: "
            + sdf.format(checkIn)
            + ", check-out: "
            + sdf.format(checkOut)
            + ", "
            + duration()
            + " nights";
    }
}
```

# Best way to do

## Program.java

```java
package TratamentoExceptions.ExceptionsPersonalizadas.ReservationProblem;

import java.text.*;
import java.util.*;

public class Program {
        // '.parse' could be an exception, our 'main' method could throw an exception
        // one of two things: either you handle the exception (try/catch) or you throw the exception

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);
        SimpleDateFormat sdf = new SimpleDateFormat("dd/MM/yyyy");
        try {

            System.out.print("Room number: ");
            int number = sc.nextInt();
            System.out.print("Check-in date (dd/mm/yyyy): ");
            Date checkIn = sdf.parse(sc.next());
            System.out.print("Check-out date (dd/mm/yyyy): ");
            Date checkOut = sdf.parse(sc.next());

            Reservation reservation = new Reservation(number, checkIn, checkOut);
            System.out.println("Reservation: " + reservation);

            System.out.println();
            System.out.print("Enter data to update the reservation: ");
            System.out.print("Check-in date (dd/mm/yyyy): ");
            checkIn = sdf.parse(sc.next());
            System.out.print("Check-out date (dd/mm/yyyy): ");
            checkOut = sdf.parse(sc.next());

            reservation.updateDates(checkIn, checkOut);
            System.out.print("Reservation: " + reservation);
        }
        catch (ParseException e) {
            System.out.println("Invalid date format");
        }
        catch (IllegalArgumentException e) {
            System.out.println("Error in reservation: " + e.getMessage());
        }
        sc.close();
    }
}

```

## Reservation.java

```java
package TratamentoExceptions.ExceptionsPersonalizadas.ReservationProblem;

import java.text.SimpleDateFormat;
import java.util.*;
import java.util.concurrent.TimeUnit;

import TratamentoExceptions.ExceptionsPersonalizadas.ReservationProblem.exceptions.DomainException;

public class Reservation {

    private Integer roomNumber;
    private Date checkIn;
    private Date checkOut;

    // this is static for don't generate one SimpleDateFormat for each Reservation class created
    private static SimpleDateFormat sdf = new SimpleDateFormat("dd/MM/yyyy");

    public Reservation(Integer roomNumber, Date checkIn, Date checkOut) {
        this.roomNumber = roomNumber;
        this.checkIn = checkIn;
        this.checkOut = checkOut;
    }
    public Integer getRoomNumber() {
        return roomNumber;
    }
    public void setRoomNumber(Integer roomNumber) {
        this.roomNumber = roomNumber;
    }
    public Date getCheckIn() {
        return checkIn;
    }

    public Date getCheckOut() {
        return checkOut;
    }

    public long duration() {
        // diference between dates in milliseconds
        long diff = checkOut.getTime() - checkIn.getTime();
        return TimeUnit.DAYS.convert(diff, TimeUnit.MILLISECONDS);
    }

    // I propagated the exception in my method signature so I don't have to handle exceptions inside the try/catch
    public void updateDates(Date checkIn, Date checkOut) throws DomainException {
        Date now = new Date();
        if (checkIn.before(now) || checkOut.before(now)) {
            throw new DomainException( "Reservation dates for update must be future dates");
        }
        if(!checkOut.after(checkIn)) {
            throw new DomainException( "Check-out date must be after check-in date");
        }
        this.checkIn = checkIn;
        this.checkOut = checkOut;
    }

    @Override
    public String toString() {
        return "Room "
            + roomNumber
            + ", check-in: "
            + sdf.format(checkIn)
            + ", check-out: "
            + sdf.format(checkOut)
            + ", "
            + duration()
            + " nights";
    }
}
```

## DomainException.java

```java
package TratamentoExceptions.ExceptionsPersonalizadas.ReservationProblem.exceptions;

public class DomainException extends Exception {

    public DomainException(String msg) {
        super(msg);
    }

}



// my exception being an extension of Exception, the compiler forces it to be handled
// my exception being an extension of RunTimeException, the compiler does not force it to be handled
```

# IMPORTANT
## What is the difference between Exception and RuntimeException? 
> Let's understand the fundament of Java about problems: **whenever something can be done with the problem, an exception must be captured**
### RuntimeException
> when an exception **can be** prevented
### Exception
> For errors that **must be** prevented, `Exception` is used, which is more generic and involves any exception, including a `RuntimeException` since all exceptions must be derived from `Exception`.
